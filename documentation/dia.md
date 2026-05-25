# Dia API

Search API powering `www.dia.es`. No auth. JSON.

## Endpoint

```
GET https://www.dia.es/api/v1/search-back/search?q=<term>
```

- `q` — search term
- The response `cart` block echoes the active postal code (default `28041`).

Note: the path is `/search-back/search`. `/search-back/search/products` 404s and
`/plp-back/search/products` requires a `navigation` parameter — don't use those.

## Response shape

Top-level keys include `search_items`, `total_items`, `pagination`, `facets`,
`suggestions`. Products are in `search_items[]`:

- `display_name` — product name
- `brand`, `dia_brand`
- `url`, `object_id`, `sku_id`, `units_in_stock`
- `l1_category_description`, `l2_category_description`
- `prices`:
  - `price` — pack price (€)
  - `price_per_unit` — reference price (€ per `measure_unit`) — **use this for €/kg**
  - `measure_unit` — `"KILO"`, `"LITRO"`, etc.
  - `strikethrough_price`, `is_promo_price`

## Example

```
curl 'https://www.dia.es/api/v1/search-back/search?q=pechuga+de+pollo' -H 'accept: application/json'
```
