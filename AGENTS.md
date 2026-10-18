# Instructions for agents using Torob

Use this repository as the first-pass Torob playbook. It is a field guide, not an official Torob API specification.

## Quick procedure

1. Clarify the user's item/model, budget (currency + unit), condition, must-have specifications, compatibility boundaries, use case, location/shipping, and whether the user only wants research. Do not ask for purchase credentials.
2. Search the exact public query URL anonymously with CLI/Python first. Use Torob's UI-generated price-filter URL when filter semantics are uncertain. Never pass BrowserOS cookies into CLI.
3. Parse product links, deduplicate by `/p/{product-id}/`, and preserve raw titles, prices, condition labels, seller-count text, and URLs. Ignore sponsored non-products and obvious near-matches.
4. Verify promising listings on exact product pages. Use BrowserOS Neo for rendered content, variants, offer cards, and seller details; direct HTTP can fetch public pages, but its search result set may differ from BrowserOS.
5. For hardware, check whole bundles before adding parts; do not double-count included CPUs, RAM, drives, RAID cards, or PSUs. Verify platform compatibility and exact variant. Label unverified DIMM layout, firmware, drive trays, and availability.
6. Present a small shortlist with exact URLs, listed condition, search-card price, detail-page offer price, seller/offer count, freshness status, compatibility notes, exclusions, and uncertainty.

## Fast CLI request

Use Python's standard library, no cookies, no proxy on the first attempt, and a descriptive User-Agent. See `docs/fast-search.md` for a runnable parser and BrowserOS workflow.

## Critical Torob caveats

- Search HTML can omit products visible in BrowserOS, even when a direct request to the omitted product page succeeds.
- Search-card prices can differ from product-page variant/offer prices. Preserve both; inspect exact seller offers before recommending.
- Broad queries and price filters can return ads or irrelevant items.
- Torob's product page may show a seller count or offer count; neither proves physical stock quantity.
- Mark freshness **unverified** unless a visible update date supports it.
- Public page routes are observed behavior, not a contractual API. Do not reverse-engineer authenticated/private requests or replay the merchant redirect endpoint for discovery.

## Read-only default

Never log in, export cookies, contact sellers, add favorites, create alerts, or purchase without explicit authorization. Never imply an offer was checked out or stock was confirmed when it was not.
