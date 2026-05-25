# Mercadona API

Online store API powering `tienda.mercadona.es`. No auth, no anti-bot. JSON.

## Endpoints

There is **no text-search endpoint** — you browse the category tree.

- `GET /api/categories/` — 26 top-level categories, each with subcategories (`id`, `name`).
- `GET /api/categories/{id}/` — expands a top category: `.categories[]` subcategories, each with a `.products[]` array.
- `GET /api/products/{id}/` — single product detail.

Useful category IDs: Meat = `3`, "Aves y pollo" (poultry) = `38`.

## Product shape

Each product in `products[]`:

- `display_name` — product name
- `price_instructions`:
  - `bulk_price` — total pack price (€)
  - `unit_price` — price per selling unit (€/kg when sold by weight)
  - `reference_price` + `reference_format` — normalized reference price and its unit (e.g. `4.09`, `"kg"`) — **use this for €/kg**
  - `unit_size`, `size_format`, `pack_size` — pack size info
  - `is_pack`, `selling_method`

## Pricing / region

Prices are postal-code scoped. Set the postcode with `POST /api/postal-codes/actions/change-pc/` (body: postal code). Defaults are usable for Barcelona testing.

## Example

```
curl 'https://tienda.mercadona.es/api/categories/38/' -H 'accept: application/json'
```
