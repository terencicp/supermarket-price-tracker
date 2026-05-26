# supermarket-price-tracker

This project tracks the price of an online shopping basket across several supermarkets and shows which store is cheapest — overall, by product, and by category. It's deliberately simple: CSV data, one static HTML dashboard, markdown README summary. Everything is versioned in git, the commit history doubles as price history.

## Files

- `config.json` — what to track: the list of stores and the fixed basket (foods and categories). The source of truth; changes only when the user asks.
- `prices.csv` — the latest prices. Regenerated each month. The canonical price record (git history = price history). Columns: `category,item,<store1>,<store2>,…` — one row per basket item, one price column per store in `config.json` `stores` order. If `config.json` changes, the store columns must be regenerated to match.
- `index.html` — a self-contained interactive page. Embeds the CSV in a `<script type="text/csv" id="prices">` block so it works by double-click (no server, no `fetch`). Renders the ranking.
- `README.md` — human-readable summary of the latest run: search date, cheapest store overall, cheapest store per category, and any notable movers vs. the previous run. Overwritten each month.
- `category-avg.csv` — derived from `prices.csv` and `shipping.csv`. One row per category with the mean item price for each store. Columns: `category,items,<store1>,<store2>,…` where `items` is the number of basket items in that category. Regenerated each month from `prices.csv`. A final `Shipping` row is appended by copying the `fee_eur` column from `shipping.csv`.
- `search-log.csv` — audit log of the latest price lookup. Replaced each run, not appended. One row per `(item, store)` pair, with columns: `date,item,store,price,url`. The `url` MUST be the page the agent actually read the price from.
- `shipping.csv` — home-delivery shipping costs per store. Updated manually.
- `documentation/` — one markdown file per store describing how to fetch that store's prices: endpoint or scrape strategy, request shape, response shape, quirks, and a runnable example.

## The agent's job

Once a month:

1. Search current supermarket prices for the basket in `config.json`. For each `(item, store)`, record the price and the source product-page URL.
2. Write the latest prices to `prices.csv`.
3. Replace the contents of the `#prices` `<script>` block in `index.html` with the same prices CSV data.
4. Regenerate `category-avg.csv` from `prices.csv` (mean item price per category per store, rounded to 2 decimals).
5. Rewrite `README.md` summarizing the run: date, overall cheapest store for the full basket, cheapest store per category, and anything that changed meaningfully vs. last month.
6. Commit and push.

## Search strategy

Run a subagent for each store to find prices for each product in the basket. Follow the API described in the docs in `documentation/supermarket_name.md` for each supermarket. The subagent should stop and report if the documented API fails.

Consider:
- Units: calculate €/kg for everything, except eggs (€/dozen).
- Pack size: ignore packs > 1 kg (or > 12 eggs).
- No frozen vegetables.
- Delivery postcode: 08001 (Barcelona).
- Sanity-check outliers: if one store's price is an outlier, re-verify.
- Retry-run an agent once for products that have not been found.
