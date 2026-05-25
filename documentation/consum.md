# Consum API

Catalog/search API powering `tienda.consum.es`. No auth. JSON.

## Endpoint

```
GET https://tienda.consum.es/api/rest/V1.0/catalog/product?q=<term>
```

- `q` — search term. **The search param is `q`.** `text=` is silently ignored
  and returns the entire ~9,265-product catalog — easy trap.

## Response shape

Top-level: `totalCount`, `hasMore`, `products[]`.

Each product:

- `code`, `ean`
- `productData` — `name`, `description`, `brand.name`, `url`, `imageURL`
- `priceData.prices[0].value`:
  - `centAmount` — pack price. **Despite the name, this is euros, not cents** (e.g. `1.17` = €1.17).
  - `centUnitAmount` — reference price per `unitPriceUnitType` (e.g. €/kg, €/L) — **use this for €/kg**
- `priceData.unitPriceUnitType` — the reference unit, e.g. `"1 L"`, `"1 Kg"`

## Example

```
curl 'https://tienda.consum.es/api/rest/V1.0/catalog/product?q=pechuga%20de%20pollo' -H 'accept: application/json'
```
