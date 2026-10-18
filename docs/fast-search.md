# Fast Torob search: CLI for breadth, BrowserOS for validation

## 1. Define the search

Before searching, write down:

- Persian and/or English query terms, including exact model/variant when known.
- Budget, currency, whether it is a hard maximum, and whether shipping/tax/setup are included.
- New, used, refurbished, or mixed condition policy.
- Required fit/specifications and the acceptable alternatives.
- Stop condition: e.g. five search queries plus two product pages.

For a comparison of tools, give both methods the same ordered queries and stop condition.

## 2. Direct anonymous CLI discovery

The public route observed is `https://torob.com/search/?query=<URL-encoded query>`. This returned HTTP 200 and useful server-rendered result links in the tests below. Use direct access first, no cookies, and disable proxying for the first attempt.

```python
from html.parser import HTMLParser
from time import perf_counter
from urllib.parse import urlencode, urljoin
from urllib.request import Request, build_opener, ProxyHandler

class ProductLinks(HTMLParser):
    def __init__(self):
        super().__init__()
        self.current_href = None
        self.current_text = []
        self.rows = []

    def handle_starttag(self, tag, attrs):
        if tag == "a":
            self.current_href = dict(attrs).get("href", "")
            self.current_text = []

    def handle_data(self, data):
        if self.current_href is not None:
            self.current_text.append(data)

    def handle_endtag(self, tag):
        if tag == "a" and self.current_href is not None:
            self.rows.append((self.current_href, " ".join(" ".join(self.current_text).split())))
            self.current_href = None
            self.current_text = []

query = "سرور HPE DL380 Gen9 استوک"
params = {"query": query}
# Add only filter params observed in Torob's UI-generated URL.
# Example observed 90–110M Toman range:
# params.update(price__gt="90000000", price__lt="110000000")
url = "https://torob.com/search/?" + urlencode(params)
opener = build_opener(ProxyHandler({}))  # direct, anonymous
req = Request(url, headers={"User-Agent": "Mozilla/5.0 (compatible; read-only research)"})
started = perf_counter()
with opener.open(req, timeout=20) as response:
    body = response.read().decode("utf-8", "replace")
    status = response.status
elapsed = perf_counter() - started

parser = ProductLinks()
parser.feed(body)
products = {}
for href, raw_text in parser.rows:
    if "/p/" not in href:
        continue
    full_url = urljoin("https://torob.com", href)
    # Deduplicate repeated card links by the canonical product URL/ID.
    product_id = full_url.split("/p/", 1)[1].split("/", 1)[0]
    products.setdefault(product_id, {"url": full_url, "raw_text": raw_text})

print({"status": status, "elapsed_s": round(elapsed, 3), "bytes": len(body),
       "unique_products": len(products), "url": url})
for item in products.values():
    print(item["url"], item["raw_text"])
```

Treat parsed cards as candidates, not verified offers. Preserve original Persian digits and price strings until normalization is tested. Do not assume the raw HTML and browser results are identical.

## 3. Use BrowserOS Neo for rendered verification

Use the dedicated BrowserOS Neo MCP when available. Open an agent-owned tab, leave the user's tabs as found, and use `run` to compose open → wait for expected text → snapshot/read. Example shape:

```js
const query = 'سرور HPE DL380 Gen9 استوک';
const url = 'https://torob.com/search/?query=' + encodeURIComponent(query);
const p = await browser.open(url);
await p.waitForText('DL380', {timeout: 15000});
const snapshot = await p.snapshot({mode: 'interactive', depth: 3});
const text = await p.read();
return {url: snapshot.url, snapshot: snapshot.text, text};
```

Use the result-page product URL to open the exact product detail. Re-snapshot after page changes; refs can go stale. Prefer `waitForText`/selector waits to arbitrary sleeps. Do not copy cookies or use browser session data in CLI.

## 4. Verify each candidate

On the product page, verify:

- Exact product/variant: capacity, region, interface, chassis/SFF vs LFF, etc.
- Condition labels (`کارکرده`, `استوک`, refurbished, new).
- Current seller offer(s), amount, seller name, warranty/return notes, and online vs in-person status when visible.
- Included components for bundles; never add a component again if the bundle already lists it.
- Whether the low price is an outlier against other offers; compare exact variants only.
- Freshness date if visible; otherwise write **unverified**.

A page that says “from” is not necessarily the seller/variant the user will buy. Keep search-card price and product-page price separate when they disagree.

## 5. Timing a method comparison

Use the same model assumption, exact queries in the same order, condition/budget, and completion criterion. Record both:

- **Per-operation time:** CLI request+parse or BrowserOS navigation/render/read.
- **End-to-end time:** from just before the first query through the same number of product-page checks and the same report-ready shortlist.

Use one batched CLI script and one bounded BrowserOS run where practical. If BrowserOS uses multiple MCP calls, include that orchestration overhead for both methods or label the timing scopes as different. Do not call a ratio “controlled” when stop boundaries differ. Record missing results as well as elapsed time; speed without recall is not a win.

## 6. Common failure modes

- Sponsored results or keyword matches can be accessories, not the requested item.
- Duplicate anchor links inflate counts; deduplicate by product ID.
- CLI search may omit a product shown by BrowserOS; direct product-page access can still work if the URL is known.
- Search-card price may be stale or differ from exact seller offers.
- Seller/offer count is not stock quantity.
- Do not use undocumented authenticated endpoints or replay Torob's merchant redirect as a search API.
