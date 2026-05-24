# supermarket-price-tracker

This project tracks the price of an online shopping basket across several supermarkets and shows which store is cheapest — overall, by product, and by category. It's deliberately simple: CSV data and one static HTML page. Everything is versioned in git, the commit history doubles as price history.

## Files

- `config.json` — what to track: the list of stores and the fixed basket (foods and categories). The source of truth; changes only when the user asks.
- `prices.csv` — the latest prices. Regenerated each month. The canonical price record (git history = price history). Columns: `category,item,<store1>,<store2>,…` — one row per basket item, one price column per store in `config.json` `stores` order. If `config.json` changes, the store columns must be regenerated to match.
- `index.html` — a self-contained interactive page. Embeds the CSV in a `<script type="text/csv" id="prices">` block so it works by double-click (no server, no `fetch`). Renders the ranking.
- `README.md` — human-readable summary of the latest run: search date, cheapest store overall, cheapest store per category, and any notable movers vs. the previous run. Overwritten each month.
- `search-log.csv` — audit log of the latest price lookup. Replaced each run, not appended. One row per `(item, store)` pair, with columns: `date,item,store,price,url`. The `url` MUST be the page the agent actually read the price from.
- `category-averages.csv` — derived from `prices.csv`. One row per category with the mean item price for each store. Columns: `category,items,<store1>,<store2>,…` where `items` is the number of basket items in that category. Regenerated each month from `prices.csv` — never edit by hand.

## The agent's job

Once a month:

1. Search current supermarket prices for the basket in `config.json`. For each `(item, store)`, record the price and the source product-page URL. Fan out sub-agents in parallel, batched by **product** (~6 items per agent).
2. Write the latest prices to `prices.csv` (one row per item, one column per store — same shape as today).
3. Replace the contents of the `#prices` `<script>` block in `index.html` with the same CSV. `prices.csv` and the inline copy must stay in lockstep — `prices.csv` is canonical, the inline copy is what the page actually reads.
4. Rewrite `search-log.csv` with one row per `(item, store)`: search date, price, and the URL the agent actually used to obtain the price.
5. Regenerate `category-averages.csv` from `prices.csv` (mean item price per category per store, rounded to 2 decimals).
6. Rewrite `README.md` summarizing the run: date, overall cheapest store for the full basket, cheapest store per category, and anything that changed meaningfully vs. last month.
7. Commit and push.

## Search strategy

- Units: calculate €/kg for everything, except eggs (€/dozen).
- Pack size: ignore packs > 1 kg (or > 12 eggs).
- Delivery postcode: 08001 (Barcelona).
- Store websites often block automated requests on product pages. Third-party price trackers and search-result snippets are usually the working path. Don't retry a source that fails.
- Watch for stale data: cached snippets and tracker entries can be months or years old.
- Sanity-check outliers: if one store's price is an outlier, re-verify.
- Retry-run an agent once for products that have not been found.
