# supermarket-price-tracker

Monthly price comparison of a fixed 27-item basket across 9 supermarkets that deliver to Barcelona (postcode 08001). Prices in €/kg (eggs in €/dozen). See [`AGENTS.md`](AGENTS.md) for the methodology and [`index.html`](index.html) for the interactive ranking.

## Latest run — 2026-05-26

All 243 cells filled. Carrefour takes 1st this run, edging Alcampo by €2.50 on the full 27-item basket.

### Cheapest store overall

| # | Store           | 27-item basket |
|---|-----------------|----------------|
| 1 | **Carrefour**   | **€110.38**    |
| 2 | Alcampo         | €112.88        |
| 3 | Consum          | €113.73        |
| 4 | Mercadona       | €115.22        |
| 5 | Dia             | €121.71        |
| 6 | Bonpreu  | €127.21        |
| 7 | Caprabo         | €128.52        |
| 8 | Condis          | €138.00        |
| 9 | Ametller | €138.48        |

Last run: Alcampo €110.16, Carrefour €113.13, Mercadona €115.70, Consum €117.56, Bonpreu €119.57, Dia €121.62, Caprabo €128.04, Condis €132.78, Ametller €139.87.

### Cheapest store per category

| Category | Cheapest        | Avg €/kg       | Notes |
|----------|-----------------|----------------|-------|
| Seeds    | Alcampo         | 1.72           | Cheapest on chickpeas (€1.63) and white beans (€1.79) |
| Starches | Bonpreu  | 1.96           | 0.8 kg potato bag at €1.24/kg holds the lead |
| Veggies  | Mercadona       | 2.85           | Cheapest on onion fresh (€2.00), bell pepper (€2.10), green beans plana (€3.93) |
| Fruit    | Alcampo         | 1.92           | Cheapest on apples (€1.44) and kiwis (€2.88) |
| Avocado  | Dia             | 3.76           | 450 g tray €1.69 |
| Nuts     | Carrefour       | 12.94          | In-shell walnuts (€6.14) and Frumesa almendra natural 400 g (€11.22) |
| Meat     | Mercadona       | 5.27           | Lomo trozo €4.95 and contramuslos sin piel €4.20 |
| Yogurt   | Dia             | 1.32 /kg       | Yogur natural desnatado 6×125 g |
| Eggs     | Alcampo         | 2.59 /dozen    | Auchan barn-housed M, 12 ud |

### Movers vs. last run

- **No-frozen rule bit harder this run** — Bonpreu, Caprabo, and Condis all had to swap frozen SKUs for fresh: Bonpreu spinach €2.23 → €4.30 and green beans €1.80 → €6.99; Caprabo cauliflower €2.13 → €5.25 and green beans €1.80 → €4.36; Condis green beans €1.79 → €6.00. Bonpreu drops from 5th to 6th as a result.
- **Alcampo loses 1st spot**: cauliflower €1.78 → €2.04 (switched off variable-weight 1 ud), spinach €1.67 → €4.13 (last month was a frozen 750 g bag, now refrigerated 300 g per the rules). Still a strong 2nd.
- **Carrefour sweet potato**: €5.90 → €2.89 (regular boniato available again, not only eco). Big driver of Carrefour's climb to 1st.
- **Carrefour chicken thigh**: €3.49 → €3.29 (jamoncitos still by far the cheapest meat protein in the dataset).
- **Caprabo yogurt**: €2.00 → €1.33 — Eroski Basic 8×125 g pack found, now tied with Mercadona.
- **Caprabo hazelnuts**: €27.93 → €21.66 (Eroski 175 g tostada replacing last run's outlier).
- **Consum cauliflower**: €3.98 → €2.20 (coliflor granel sold loose, much cheaper than the bandeja).
- **Bonpreu pork loin**: €7.99 → €8.99 (cinta extra fi up; no cheaper trozo available this run).
- **Ametller banana**: €2.89 → €1.99 (switched to a Tropical bunch SKU; cheaper than canario by piece).
- **Dia & Mercadona** held nearly flat — Dia changed essentially nothing, Mercadona's only material move was cooked chickpeas €2.61 → €2.13 (back to Hacendado tarro).
- **Onions** still wildly priced: Dia €1.00 cheapest in dataset, Carrefour €2.39 most expensive of the affordable group, Alcampo €2.35 hasn't budged.

### Caveats

- Bonpreu and Caprabo no longer get artificial wins from frozen spinach/green beans/cauliflower — those got swapped out this run per the methodology, which is a big reason their totals jumped vs last month.
- Cooked chickpeas €/kg uses jar gross weight (drained ≈ 60 %). Convention is consistent across all stores. Carrefour's €2.12 is the Classic 400 g jar (last month was an older 720 g SKU at €1.89; not under 1 kg constraint here, but the smaller jar is what's on shelf now).
- Some "fresh produce by piece" prices come from the small per-kg figure listed on a single-unit page; mallas above 1 kg are skipped per the methodology even when they'd be cheaper per kg.
- Pack-size rule (≤1 kg / ≤12 eggs) applied strictly. Exceptions kept: Mercadona's single ~1.04 kg coliflor (only option), Carrefour's coliflor verde 1 unit ≈1 kg, Condis coliflor priced per unit, Ametller coliflor extra ~1.5 kg head.
- Bonpreu cauliflower fresh option is only available as floretes safata 500 g at €3.98/kg — whole heads are priced per unit; this is the cleanest fresh per-kg fresh figure.
