---
name: campbells-product-catalog
description: Page and filter the Campbell's product catalogue and its five classification taxonomies — category, product line, tag, main ingredient and dietary alternative — through the public campbells.com content API.
api: campbells:campbells-content-api
generated: '2026-09-05'
method: generated
source: >-
  Grounded in discovery/campbells-wp-v2-routes.json and the provider-served
  OPTIONS schema saved at json-schema/campbells-product.json. Requests shown
  were executed against the live API on 2026-09-05. No OpenAPI exists, so
  operations are identified by method + path.
operations:
  - GET /wp-json/wp/v2/product
  - GET /wp-json/wp/v2/product/{id}
  - GET /wp-json/wp/v2/external_product
  - GET /wp-json/wp/v2/csc_product_cat
  - GET /wp-json/wp/v2/csc_product_line
  - GET /wp-json/wp/v2/csc_product_tag
  - GET /wp-json/wp/v2/csc_main_ingredient
  - GET /wp-json/wp/v2/csc_dietary_alternative
  - GET /wp-json/wp/v2/csc_faq
  - GET /wp-json/wp/v2/types
---

# Working with the Campbell's product catalogue

Base: `https://www.campbells.com/wp-json/wp/v2`

Public, no credential. Same caution as the recipe skill: this is the consumer
site's own backend, not a supported developer API. There is no contract, no
published rate limit and no support channel.

## Resolve the shape first

```
GET /wp-json/wp/v2/types
```

Returns the live registry of content types. Campbell's own types are `product`,
`recipe`, `external_product` and `csc_faq`; the rest are WordPress core. Each
entry names its `rest_base` and the taxonomies attached to it — do this once at
the start of a run rather than hard-coding the surface.

For the field list of any one type, send an OPTIONS request:

```
OPTIONS /wp-json/wp/v2/product
```

The `schema` object in that response is a JSON Schema document for the resource.
The four Campbell's ones are saved in `json-schema/` in this repo.

## List products

```
GET /wp-json/wp/v2/product?per_page=100&_fields=id,slug,link,title,csc_product_line
```

210 products as of 2026-09-05 (`X-WP-Total: 210`). `per_page` maxes at 100.

## The five product taxonomies

| Taxonomy | Collection | Filters `product` by |
|---|---|---|
| `csc_product_cat` | `/csc_product_cat` | product category |
| `csc_product_line` | `/csc_product_line` | brand/product line (Chunky, SpaghettiOs, Slow Kettle …) |
| `csc_product_tag` | `/csc_product_tag` | free tags |
| `csc_main_ingredient` | `/csc_main_ingredient` | primary ingredient |
| `csc_dietary_alternative` | `/csc_dietary_alternative` | dietary variants (gluten-free, heart-healthy …) |

Fetch the term list, then filter products by term **id**:

```
GET /wp-json/wp/v2/csc_product_line?per_page=100&_fields=id,slug,name,count
GET /wp-json/wp/v2/product?csc_product_line=<id>&per_page=100&_fields=id,slug,link
```

Every taxonomy filter has an `_exclude` twin (`csc_product_line_exclude`), and
`tax_relation=AND|OR` controls how multiple taxonomy filters combine.

## Retail destinations

`external_product` is a separate type for products carried on outside retail
destinations. It carries no `csc_*` taxonomy — check
`json-schema/campbells-external-product.json` before assuming a field exists.

## Consumer FAQs

```
GET /wp-json/wp/v2/csc_faq?per_page=100&_fields=id,slug,title,csc_topic
```

36 entries, classified by the `csc_topic` taxonomy. This is the closest thing
Campbell's has to a structured support corpus.

## Custom fields

Product specifics live under `acf`, and — as with recipes — the OPTIONS schema
types `acf` as a bare object without declaring its inner keys. Validate what you
get; do not assume a field is present across the catalogue.

## Sync pattern

1. `GET /wp/v2/types` to confirm the surface has not changed.
2. Pull each taxonomy once and cache term id → name.
3. Page `/wp/v2/product` with `_fields` and `orderby=modified`, reading
   `X-WP-Total` / `X-WP-TotalPages` for the bounds.
4. On later runs use `modified_after=<last run ISO 8601>` — there is no ETag or
   Last-Modified, so this is the only incremental signal available.
5. Back off on your own schedule. No `RateLimit-*` or `Retry-After` header is
   ever returned, so the API will not tell you when you are asking too much.
