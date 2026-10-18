# Torob site map (observed)

This is a practical map of the public shopping-comparison flow, not a complete crawl or official site specification. Page structure and paths can change.

## 1. Home and navigation

- Home: `https://torob.com/`
- Category/browse pages use links shaped like `/browse/{category-id}/{slug}/`.
- Nested brand pages may extend the category route with `/b/{brand-id}/{slug}/`.
- Use Torob's actual links rather than guessing category or brand IDs.

## 2. Search results

- Search page: `https://torob.com/search/?query={URL-encoded query}`.
- Search page exposes product cards, title, price, seller/offer count, condition hints, filters, sorting, and sponsored content.
- Price filters observed through the UI are encoded as `price__gt` and `price__lt`; see `observed-routes-and-data.md`.
- Search results can mix exact products with ads, accessories, wrong generations, empty chassis, and similarly named items.
- Repeated card links occur; count unique product IDs, not raw anchors.

## 3. Product detail

- Product route: `https://torob.com/p/{product-id}/{slug}/`.
- Product pages show the product title and breadcrumb/category, variants, seller offer(s), visible prices, and sometimes seller rating/tenure, location, and price-history controls.
- Offers can be separated into online and in-person purchase modes.
- A product page may show “from” pricing or multiple variants. Check that price, region, capacity, condition, and seller all match the target.
- Search-page price and product-page offer can differ; preserve both values and explain the discrepancy.

## 4. Seller store

- Seller pages observed in link form `/shop/{shop-id}/{slug}/`.
- Use seller information as Torob-reported context (name, location, badges/history), not independent proof of quality or stock.
- Do not contact sellers unless the user explicitly asks and authorizes it.

## 5. Merchant handoff

- Product offer links can pass through `api.torob.com/v4/product-page/redirect/` before a retailer destination.
- This was observed as a click/merchant-tracking route, not as a search API. Do not use it for bulk discovery or follow it automatically. User-initiated merchant navigation is outside read-only research.

## 6. What is not established

- No stable, documented public Torob search API was verified in this first pass.
- The anonymous HTML search page is useful but not complete: a same-query BrowserOS page can show a product ID absent from direct CLI HTML.
- Do not infer physical inventory from shop counts or freshness from a current-looking price.
- Do not infer that the user is signed in or copy session cookies. CLI discovery should remain anonymous.
