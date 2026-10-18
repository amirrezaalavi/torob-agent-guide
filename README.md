# Torob agent guide

A practical, read-only playbook for finding and validating Torob listings using fast anonymous HTTP for discovery and BrowserOS for rendered-page verification.

> **Status:** first-pass notes, based on live read-only tests observed on 2026-10-18. Torob can change markup, ranking, prices, and routes. Recheck before relying on this guide.

## Start here

- New agents: read [`AGENTS.md`](AGENTS.md).
- [Fast search tutorial](docs/fast-search.md): CLI discovery, BrowserOS verification, timing rules.
- [Site map](docs/site-map.md): search, category, product, and seller pages.
- [Observed routes and data](docs/observed-routes-and-data.md): page routes, query parameters, currency pitfalls, and API limits.
- [What to ask the user](docs/user-inputs.md): scope checklist.
- [Initial benchmark](benchmarks/2026-10-18-cli-vs-browseros.md): measured test and limitations.

## Recommended pattern

1. Get a precise brief: item/model, budget and currency, condition, compatibility requirements, workload, location, and costs to include.
2. Use a direct, anonymous request to Torob's public search page to collect candidate product URLs. Try direct access first; do not copy BrowserOS cookies.
3. Filter out ads, near-matches, wrong variants, and explicit used/refurbished listings that violate the brief. Deduplicate by Torob product ID.
4. Open exact product pages in BrowserOS or directly via public HTTP to inspect variant, current seller offers, condition, and seller information.
5. Compare more than one exact-variant offer where possible. Record freshness as **unverified** unless an update date is actually visible.
6. Report snapshot prices, not guaranteed checkout prices. A seller count is a count of listed offers, not physical units.

## Safe boundaries

Research is read-only unless the user separately authorizes an action. Do not sign in, copy/export cookies, contact sellers, submit questions, save favorites, create alerts, click through to purchase, or place orders. Do not treat undocumented internal endpoints as a stable public API. See [Observed routes and data](docs/observed-routes-and-data.md).
