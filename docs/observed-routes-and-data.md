# Observed public routes and data

Scope: routes/data observed during read-only Torob searches on 2026-10-18. This is not an official API contract. Prefer public pages and the site's own UI over undocumented endpoints.

## Public page routes

| Purpose | Observed pattern | Notes |
|---|---|---|
| Home | `https://torob.com/` | Navigation to categories/search. |
| Search | `/search/?query={URL-encoded query}` | Public, anonymous GET returned HTTP 200 in tested requests. |
| Search price range | `price__gt={lower}` and `price__lt={upper}` | Observed after applying Torob UI filter. Example: 90,000,000–110,000,000 Toman produced `price__gt=90000000&price__lt=110000000`. Re-read the UI-generated URL when scope matters; don't assume inclusive/exclusive semantics from parameter names alone. |
| Category | `/browse/{id}/{slug}/` | Use actual breadcrumb links. |
| Brand category | `/browse/{category-id}/{category-slug}/b/{brand-id}/{brand-slug}/` | Observed for mobile brands. |
| Product detail | `/p/{UUID}/{slug}/` | Public product page; exact seller/variant needs checking. |
| Seller shop | `/shop/{shop-id}/{slug}/` | Public seller context, subject to Torob's displayed data. |

## Structured data and currency

- Product-page HTML was observed to contain JSON-LD offer data (`AggregateOffer` / `Offer`) with `priceCurrency: "IRR"`.
- Torob's visible UI prices were in Toman. Example observed: page displayed **137,000,000 Toman**, while structured data carried **1,370,000,000 IRR**. Convert Rial to Toman by dividing by 10, and cross-check the result against visible text before using it.
- Search-page HTML sometimes contains usable product links and visible text server-side, but it is not a complete or invariant mirror of the browser result set.

## Observed merchant redirect (do not use as search API)

- Product-offer links were observed with URLs beginning `https://api.torob.com/v4/product-page/redirect/` and query parameters such as `source`, `discover_method`, and tracking/session identifiers.
- This appears to be a merchant handoff/tracking URL. It was not used to search or verify stock. Its parameter schema is undocumented here; do not replay, synthesize, bulk-call, or treat it as stable API.
- No authenticated/private Torob endpoint was used or verified. Do not copy BrowserOS cookies or session headers into CLI requests.

## Data handling rules

1. Preserve original price text and condition labels; normalize only after recording the raw form.
2. Convert Toman/Rial consistently; a factor-of-ten currency error is easy to make.
3. Record whether a value came from the search card, product-page variant summary, or a specific seller offer.
4. Deduplicate search anchors by product UUID.
5. Treat seller counts as offer counts, not physical stock.
6. Mark offer freshness **unverified** when no visible update date exists.
7. Do not follow merchant redirects, initiate checkout, or contact sellers during read-only research.
