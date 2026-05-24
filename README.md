# supermarket-price-tracker

Monthly price comparison of a fixed 27-item basket across 9 supermarkets that deliver to Barcelona (postcode 08001). Prices in €/kg (eggs in €/dozen). See [`AGENTS.md`](AGENTS.md) for the methodology and [`index.html`](index.html) for the interactive ranking.

## Latest run — 2026-05-24

This is the **first real-data run** — prior commits contained placeholder dummy values.

### Cheapest store overall

Comparing on the 18 items where every store had a price (i.e. excluding items any store didn't sell or surface):

| # | Store           | 18-item basket |
|---|-----------------|----------------|
| 1 | **Consum**      | **€79.05**     |
| 2 | Mercadona       | €80.34         |
| 3 | Carrefour       | €92.07         |
| 4 | Dia             | €92.52         |
| 5 | Bonpreu Esclat  | €92.87         |
| 6 | Alcampo         | €93.82         |
| 7 | Condis          | €99.79         |
| 8 | Caprabo         | €100.13        |
| 9 | Ametller Origen | €112.89        |

Consum and Mercadona are clearly the value leaders; Ametller Origen is the outlier on the expensive end (a premium fresh-produce chain — expected).

### Cheapest store per category

Means computed from items the store actually stocks (€/kg, except Eggs €/dozen).

| Category | Cheapest      | Avg €/kg | Notes |
|----------|---------------|----------|-------|
| Seeds    | Mercadona     | 1.89     | Hacendado store-brand dominates |
| Starches | Alcampo       | 1.81     | Bonpreu lower at 1.22 but missing Sweet potato |
| Veggies  | Condis        | 2.62     | Cheap onion, cauliflower, eggplant |
| Fruit    | Carrefour     | 1.91     | Especially competitive on oranges & bananas |
| Avocado  | Bonpreu       | 3.99     | |
| Nuts     | Consum        | 8.78     | Granel (loose) selling crushes the packaged competition |
| Meat     | Carrefour     | 4.52     | Chicken thigh at €2.79/kg is the standout |
| Yogurt   | Mercadona / Caprabo | 1.33 | Tied (basic store-brand multipacks) |
| Eggs     | Bonpreu       | 2.95 /dozen | |

### Movers vs. last run

No comparison — this is the first real run. Next month we'll see actual price drift.

### Caveats

- 9 of 243 cells are blank because the item wasn't found in that store's catalogue (or only as a non-comparable format like baby spinach 70 g, or stale cached SKU). See [`prices.csv`](prices.csv) for the gaps.
- Some prices are derived from third-party trackers (`soysuper.com`, `mercastic.es`, `missupermercados.com`, `finditapp.es`, `tulistadelmerca.es`) because several supermarket sites (Alcampo, Carrefour, Caprabo, Condis, Bonpreu, Ametller) block automated requests on their product pages.
- Cooked chickpeas: €/kg uses jar gross weight (drained is ~60%). All stores follow this convention so the comparison is fair.
- Fresh items sold by piece with no posted €/kg (potato 5-kg bags, oranges in mesh, chicken trays) are converted from the smaller listed pack where possible; otherwise the per-kg derived from the only listed pack is used and noted in [`search-log.csv`](search-log.csv).
