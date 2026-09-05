---
name: campbells-recipe-search
description: Search Campbell's recipes and products, get type-ahead suggestions, and page the recipe catalogue through the public campbells.com content API. No credential required.
api: campbells:campbells-recipe-search-api
generated: '2026-09-05'
method: generated
source: >-
  Grounded in discovery/campbells-yrsc-search-v1-routes.json and
  discovery/campbells-wp-v2-routes.json — the route descriptors Campbell's own
  server publishes. Every request shown below was executed successfully against
  the live API on 2026-09-05. Campbell's publishes no OpenAPI, so operations are
  identified by method + path rather than by operationId.
operations:
  - GET /wp-json/yrsc-search/v1/query
  - GET /wp-json/yrsc-search/v1/autocomplete
  - GET /wp-json/yrsc-search/v1/featured-collections
  - GET /wp-json/wp/v2/recipe
  - GET /wp-json/wp/v2/recipe/{id}
  - GET /wp-json/wp/v2/csc_recipe_collection
---

# Searching Campbell's recipes

Base: `https://www.campbells.com/wp-json`

**Before you start.** This API is public and unauthenticated, but it is *not a
developer product*. Campbell's runs no developer portal, publishes no terms for
API use, documents no rate limits and offers no support channel for developers.
Treat it as a website's own backend that happens to answer you: be conservative
with request volume, cache what you fetch, and do not build anything that
assumes it will keep its current shape.

## 1. Free-text search (the fastest path to a recipe)

```
GET /wp-json/yrsc-search/v1/query?term=soup&limit=10
```

Arguments (all optional, from the provider's route descriptor): `term`, `limit`,
`offset`, `sort`, `filters`, `facetFilters`, `retrieveFacets`, `vertical`,
`mode`.

Each hit returns a flattened summary — `id`, `title`, `url`, `image`,
`description`, `total_time`, `prep_time`, `servings` — plus a `raw.data` block
carrying the underlying Yext entity (`type: ce_recipe`). Prefer the flattened
fields; `raw` is a vendor shape and is not contractual.

## 2. Type-ahead

```
GET /wp-json/yrsc-search/v1/autocomplete?term=tomato&limit=8
```

`term` is **required**. Omitting it returns HTTP 400 with
`{"code":"rest_missing_callback_param","message":"Missing parameter(s): term"}`.
The response is `{"term": "...", "suggestions": ["...", ...]}` — recipe titles
only, no ids, so follow a suggestion with a `query` call to resolve it.

## 3. Browse instead of search

```
GET /wp-json/yrsc-search/v1/featured-collections
```

Returns `{"terms": [{"name": "...", "yextValue": "..."}]}` — the collections
Campbell's is currently promoting. Use the `name` as a facet value against
`query`.

For the full curated set (85 collections with recipe counts):

```
GET /wp-json/wp/v2/csc_recipe_collection?per_page=100&_fields=id,slug,name,count
```

## 4. Page the whole recipe catalogue

```
GET /wp-json/wp/v2/recipe?per_page=100&page=1&_fields=id,slug,link,title,modified
```

316 recipes as of 2026-09-05. Rules that will bite you if you ignore them:

- `per_page` is clamped to **1-100**. `per_page=500` returns HTTP 400
  `rest_invalid_param`, and `data.details.per_page` spells out the bound.
- Read `X-WP-Total` and `X-WP-TotalPages` from the response headers rather than
  paging until you get an empty array. A `Link: <...>; rel="next"` header is
  also returned, and all three are listed in `access-control-expose-headers`, so
  a browser client can read them.
- Always send `_fields`. The default recipe representation is multi-kilobyte;
  trimming to the fields you need cut one product response from several KB to
  180 bytes in testing.
- Filter by collection with `csc_recipe_collection=<term id>`, exclude with
  `csc_recipe_collection_exclude`, and combine taxonomies with `tax_relation`.
- Incremental sync: `modified_after=<ISO 8601>` with `orderby=modified`.

## 5. One recipe

```
GET /wp-json/wp/v2/recipe/{id}?_embed
```

The custom recipe fields — ingredients, steps, times, nutrition — arrive inside
the `acf` object. That object's inner shape is **not** declared in the schema
Campbell's serves (see `json-schema/campbells-recipe.json`, where `acf` is typed
only as an object), so validate defensively rather than assuming keys.

If you want a *stable, standardised* recipe representation, fetch the recipe's
`link` and parse the schema.org `Recipe` JSON-LD from the page instead. That
block is well-formed and carries `recipeIngredient`, `recipeInstructions`,
ISO-8601 `prepTime`/`cookTime`/`totalTime`, `recipeYield` and a full
`NutritionInformation` node. It is the only standards-conformant view of this
data Campbell's publishes.

## Errors

Not RFC 9457. The envelope is `{"code", "message", "data": {"status", ...}}`.
Branch on `code`, never on `message`:

| code | status | meaning |
|---|---|---|
| `rest_missing_callback_param` | 400 | a required argument was omitted; `data.params` names it |
| `rest_invalid_param` | 400 | an argument failed validation; `data.details` explains |
| `rest_post_invalid_id` | 404 | no such recipe id |
| `rest_no_route` | 404 | wrong path — note `/wp-json/` itself 404s; address a namespace root |
| `rest_forbidden` | 401 | privileged route; not reachable without a credential you cannot get |

## What is not here

No rate-limit headers are returned, so there is no runtime signal telling you to
back off — pace yourself. No `ETag` or `Last-Modified`, so conditional requests
are unavailable; use `modified_after` for incremental work. No idempotency
mechanism, no reversal operation and no dry-run mode — none of which matters for
these read paths, but all of which do if you ever reach a write route.
