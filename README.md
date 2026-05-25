# supermarket-price-tracker

Monthly price comparison of a fixed 27-item basket across 9 supermarkets that deliver to Barcelona (postcode 08001). Prices in €/kg (eggs in €/dozen). See [`AGENTS.md`](AGENTS.md) for the methodology and [`index.html`](index.html) for the interactive ranking.

## Latest run — 2026-05-26

All 243 cells filled this month — no blanks for the first time. Comparison is on the full 27-item basket (sum of €/kg plus eggs in €/dozen).

### Cheapest store overall

| # | Store           | 27-item basket |
|---|-----------------|----------------|
| 1 | **Alcampo**     | **€110.16**    |
| 2 | Carrefour       | €113.13        |
| 3 | Mercadona       | €115.70        |
| 4 | Consum          | €117.56        |
| 5 | Bonpreu Esclat  | €119.57        |
| 6 | Dia             | €121.62        |
| 7 | Caprabo         | €128.04        |
| 8 | Condis          | €132.78        |
| 9 | Ametller Origen | €139.87        |

(Last month's leaderboard was an 18-item subset because of gaps, so ranks aren't directly comparable. Still, the big move is Alcampo climbing from the middle of the pack to first.)

### Cheapest store per category

| Category | Cheapest        | Avg €/kg       | Notes |
|----------|-----------------|----------------|-------|
| Seeds    | Alcampo         | 1.72           | Cheapest on chickpeas (€1.63) and white beans (€1.79) |
| Starches | Bonpreu Esclat  | 1.97           | 0.8 kg potato bag at €1.24/kg is the standout |
| Veggies  | Bonpreu Esclat  | 2.38           | Frozen plain spinach (€2.23) and green beans (€1.80) carry it |
| Fruit    | Alcampo         | 1.92           | Cheapest on apples (€1.44) and kiwis (€2.88) |
| Avocado  | Dia             | 3.76           | 450 g tray €1.69 |
| Nuts     | Carrefour       | 12.94          | In-shell walnuts (€6.14) help; Consum lost its hazelnut granel deal |
| Meat     | Mercadona       | 5.27           | Sub-kg lomo trozo €4.95 and contramuslos sin piel €4.20 |
| Yogurt   | Dia             | 1.32 /kg       | Yogur natural desnatado 6×125 g |
| Eggs     | Alcampo         | 2.59 /dozen    | Auchan barn-housed M, 12 ud |

### Movers vs. last run

- **Hazelnuts shot up everywhere**: Consum €9.99 → €24.95 (granel option gone), Mercadona €16.25 → €21.50. No store under €19/kg.
- **Onions**: huge spread opened up. Dia dropped to €1.00/kg (cheapest in dataset), Alcampo went the other way (€1.50 → €2.35).
- **Spinach**: Alcampo dropped €7.97 → €1.67 (switched to a 750 g frozen bag), Bonpreu €4.30 → €2.23 (also frozen).
- **Bananas**: Dia €1.59 → €1.19, Alcampo €2.37 → €1.48. The two Plátano-Canario-only stores (Consum, Caprabo) hold at €2.90.
- **Carrefour sweet potato**: €2.79 → €5.90 (only eco SKU available this run; pushed Carrefour out of the Starches lead).
- **Consum potato/orange**: previous granel deals (€1.04 / €1.45) were gone — now €1.90 / €1.95. Two of the main reasons Consum fell from 1st to 4th.
- **Ametller plain yogurt**: €4.38 → €2.00 (an Essencials 4×125 g multipack appeared).
- **Mercadona meat**: pork loin €6.30 → €4.95 (sub-kg trozo), chicken thigh €6.50 → €4.20 (sin piel) — earns Mercadona the meat category outright.

### Caveats

- "Cheapest reasonable" within rules sometimes lands on a frozen SKU (Alcampo spinach, Bonpreu green beans, Caprabo cauliflower). Flagged in [`search-log.csv`](search-log.csv) via the product name.
- Cooked chickpeas €/kg uses jar gross weight (drained ≈ 60 %). Convention is consistent across all stores.
- Some "fresh produce by piece" prices come from the small per-kg figure listed on a single-unit page; mallas above 1 kg are skipped per the methodology even when they'd be cheaper per kg.
- Pack-size rule (≤1 kg / ≤12 eggs) is applied strictly. One exception: Mercadona's coliflor is sold as a single ~1.04 kg head with no smaller option, kept in.
