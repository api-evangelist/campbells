# Campbell's (campbells)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Campbell's (Campbell Soup Company, now The Campbell's Company) is the consumer brand trusted for generations to provide authentic, flavourful, and readily available soups, meals, and recipes. It ran a Campbell's Kitchen Developer API that let developers embed Campbell's, Swanson, Pace, Prego and Pepperidge Farm recipes, products, nutrition and UPC data into their own applications; that programme has been retired and its developer domain no longer resolves. What Campbell's still serves is a live, public, unauthenticated content and recipe-search API on www.campbells.com — 316 recipes, 210 products, 85 recipe collections and 36 FAQs behind the WordPress REST namespaces the site is built on — plus a separately-hosted corporate content API. There is no developer portal, no documentation, no contract and no terms governing any of it.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/campbells/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **Position:** Producing
- **Access:** 3rd-Party

## Tags

 - Food, Consumer Packaged Goods, Recipes, Brands, Nutrition, Content, Product Catalog, WordPress

## Timestamps

- **Created:** 2026-03-21
- **Modified:** 2026-09-05

## APIs

### Campbell's Content API

The public WordPress REST API behind www.campbells.com, exposing Campbell's own content types — recipe (316), product (210), external_product and csc_faq (36) — together with six Campbell's taxonomies covering recipe collections, product category, product line, product tag, main ingredient and dietary alternative. Every read endpoint answers anonymously with no key. Campbell's publishes no OpenAPI and no documentation for it; the route descriptor and per-resource JSON Schema captured in this repo were fetched from the API itself.

**Human URL:** [https://www.campbells.com/](https://www.campbells.com/)

**Base URL:** `https://www.campbells.com/wp-json/wp/v2`

#### Tags

 - Recipes, Products, Nutrition, Content

#### Properties

- [JSONSchema](json-schema/_index.yml)
- [x-RouteDescriptor](discovery/campbells-wp-v2-routes.json)
- [DataModel](data-model/campbells-data-model.yml)
- [AgentSkill](skills/campbells-product-catalog.md)

### Campbell's Recipe Search API

The yrsc-search/v1 namespace on www.campbells.com that powers the site's recipe and product search. Three public endpoints — query (term, limit, offset, sort, filters, facetFilters, retrieveFacets, vertical, mode), autocomplete (term required) and featured-collections. Responses are Yext-backed, returning flattened recipe summaries with times and servings alongside the raw ce_recipe entity. No credential required and no documentation published.

**Human URL:** [https://www.campbells.com/all-recipes/](https://www.campbells.com/all-recipes/)

**Base URL:** `https://www.campbells.com/wp-json/yrsc-search/v1`

#### Tags

 - Search, Recipes, Autocomplete

#### Properties

- [x-RouteDescriptor](discovery/campbells-yrsc-search-v1-routes.json)
- [AgentSkill](skills/campbells-recipe-search.md)

### The Campbell's Company Corporate Content API

The public WordPress REST API behind the corporate site www.thecampbellscompany.com, carrying csc_faq, csc_person and csc_resource content types with csc_brand, csc_person_group, csc_resource_cat and csc_topic taxonomies. Same posture as the consumer surface — live, anonymous, undocumented.

**Human URL:** [https://www.thecampbellscompany.com/](https://www.thecampbellscompany.com/)

**Base URL:** `https://www.thecampbellscompany.com/wp-json/wp/v2`

#### Tags

 - Corporate, Content, People

#### Properties

- [x-RouteDescriptor](discovery/thecampbellscompany-wp-v2-routes.json)

## Common Properties

- [Website](https://www.campbells.com/)
- [ParentCompany](https://www.thecampbellscompany.com/)
- [LinkedIn](https://www.linkedin.com/company/the-campbells-company)
- [x-Recipes](https://www.campbells.com/recipes/)
- [x-Products](https://www.campbells.com/products/)
- [Support](https://www.campbells.com/contact-us/)
- [HelpCenter](https://www.campbells.com/faqs/)
- [TermsOfService](https://www.thecampbellscompany.com/terms-of-use/)
- [PrivacyPolicy](https://www.thecampbellscompany.com/privacy-policy/)
- [LLMsTxt](llms/campbells-llms.txt)
- [Authentication](authentication/campbells-authentication.yml)
- [Conventions](conventions/campbells-conventions.yml)
- [ErrorCatalog](errors/campbells-problem-types.yml)
- [Lifecycle](lifecycle/campbells-lifecycle.yml)
- [Conformance](conformance/campbells-conformance.yml)
- [DataModel](data-model/campbells-data-model.yml)
- [RateLimits](rate-limits/campbells-rate-limits.yml)
- [Plans](plans/campbells-plans-pricing.yml)
- [Packages](packages/campbells-packages.yml)
- [AgentSkill](skills/_index.yml)
- [x-Discovery](discovery/campbells-discovery.yml)
- [DomainSecurity](security/campbells-domain-security.yml)

## Enrichment

Campbell's retired the Campbell's Kitchen Developer API — `developer.campbellskitchen.com` no
longer resolves in DNS. It does still serve a live, public, unauthenticated content and
recipe-search API on its own consumer and corporate sites, with no portal, no documentation,
no OpenAPI, no plan and no terms. The route descriptors and JSON Schema documents in
`discovery/` and `json-schema/` were fetched from that API itself and are saved verbatim;
everything derived from them says so in its own `method:` field.

## Maintainers

**FN:** Kin Lane

**Email:** kin@apievangelist.com
