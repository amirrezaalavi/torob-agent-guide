# Torob CLI vs BrowserOS: first HPE Gen9 test

**Observed:** 2026-10-18. Read-only comparison; no seller contact or purchase. This is an initial field note, not a controlled benchmark or current quote.

## Shared task

- Assumption: HPE ProLiant DL380 Gen9 2U rack, 8SFF when the listing specified it.
- Budget: ≤200,000,000 Toman.
- Condition: used.
- Ordered queries given to both methods:
  1. `سرور HPE DL380 Gen9 استوک`
  2. `سرور HPE DL380 G9`
  3. `رم سرور DDR4 ECC RDIMM استوک`
  4. `هارد SAS سرور 2.5 استوک`
  5. `پردازنده Xeon E5 v4 سرور استوک`
- Both agents selected one complete server bundle, so no extra CPU/RAM/disks were added.

## Candidate bundles

| Method | Listing | Listed components | Visible offer | Seller |
|---|---|---|---:|---|
| Anonymous CLI | [HP DL380 G9 8SFF](https://torob.com/p/9051f28f-472a-4624-b9ae-c5e04d78a0f1/) | 2× Xeon E5-2680 v4; 32GB RAM; 2×240GB HPE SSD; 2×1TB HDD; P440 2GB RAID; 2×800W PSU | 137,000,000 Toman | فوآد |
| BrowserOS Neo | [HPE ProLiant DL380 Gen9 8SFF](https://torob.com/p/46696be7-66ed-4980-a2a6-90894b0da74a/) | 2× Xeon E5-2680 v4; 32GB DDR4 ECC; 480GB SSD; 2TB HDD; P440ar 2GB RAID; 800W redundant PSU | 68,000,000 Toman | راساپرداز آراد |

Both product pages were independently opened after the agents returned. Anonymous CLI fetches returned HTTP 200 for both pages; BrowserOS also displayed the bundle titles, used condition (`کارکرده`), seller, and visible price. The exact RAM DIMM layout and PSU count/model are not fully specified by the listing text and remain unverified. No listing update date was confirmed; availability is not guaranteed.

## Search-result disagreement

A direct anonymous GET of the exact first query returned HTTP 200 and product cards, but its raw HTML did **not** contain the BrowserOS candidate's product ID `46696be7-66ed-4980-a2a6-90894b0da74a`. BrowserOS's rendered result page showed that 68M bundle. A direct product-page fetch worked once the URL was known. Thus search discovery/recall differed; CLI was not merely a faster mirror of BrowserOS.

The CLI candidate was found through its full five-query workflow and has a different product ID. The two bundles are broadly similar in CPU/RAM and total listed storage capacity, but not identical. The 68M offer is unusually low relative to the 137M offer and should be treated as an unverified outlier, not automatically the best buy.

## Timings reported by agents

| Method | Reported total | Per-page/search figures | Timing scope |
|---|---:|---|---|
| CLI | 3.2985 s | Five search fetches: 0.524–0.705 s in the aggregate summary; an earlier first-query fetch in the trace was 0.917 s. Product detail fetch about 0.245 s. | `perf_counter` inside a batched Python workflow, through five searches, detail-page fetch, parsing, and total calculation. |
| BrowserOS | 49.223 s | Five search navigation/read steps: 3.841–6.242 s; product detail read 3.719 s. | From first Torob navigation through rendered result reads and a product-page read, spanning multiple MCP calls/tool gaps. |

The displayed totals are about **14.9× apart**, but the measurement scopes are not identical: CLI was timed inside a batched script; BrowserOS includes navigation/render/read and gaps across MCP calls. Treat the ratio as an initial practical signal, **not a controlled speed verdict**. For a rigorous benchmark, run one bounded script per method with matching timer start/stop boundaries, exact queries, and product checks.

## Takeaways

- CLI is very fast for repeated anonymous fetches and can parse many result cards in one process.
- BrowserOS surfaced a relevant bundle missing from the direct CLI search HTML, then made rendered seller/offer details easy to inspect.
- The stronger first-pass pattern is hybrid: CLI for candidate discovery; BrowserOS/product pages for recall checks and offer validation.
- Search/card price is not a guaranteed checkout price. Shop counts do not prove physical inventory; update-date freshness remained unverified.
