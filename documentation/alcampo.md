# Alcampo API

Search API powering `compraonline.alcampo.es`. Runs on the **Last Mile**
grocery platform. No auth, no cookies. JSON.

## Endpoint

```
GET https://www.compraonline.alcampo.es/api/webproductpagews/v6/product-pages/search
```

Query parameters:

- `q` — search term
- `tag=web` — **required** platform tag
- `maxPageSize` — page size (site uses 300; small values like 50 are fine)
- `maxProductsToDecorate` — how many products returned with full pricing/images (e.g. 50)
- `includeAdditionalPageInfo=true` — also returns category facets, breadcrumbs

Notes: the live path is **`v6`** (the older `v5` is deprecated). No retailer
code in the URL — the retailer is implied by the host; Last Mile routes the
tenant server-side via the host header.

## Response shape

`productGroups[]`, each with `decoratedProducts[]`. Each product:

- `productId`, `retailerProductId`, `name`, `brand`
- `packSizeDescription` — e.g. `"750g - 1250g"` for variable-weight items
- `price` — `{ amount, currency }` — pack price
- `unitPrice` — `{ price: { amount, currency }, unit }`; `unit` is `"fop.price.per.kg"` — **use `unitPrice.price.amount` for €/kg**
- `available`, `image.src`

## Optional helpers

- Autocomplete: `GET /api/search/v1/suggestions/refined?searchTerm=<q>&limit=20&regionId=<uuid>`
- `regionId` is tied to a delivery postcode — capture one per postcode you care about.

## Example

```
curl 'https://www.compraonline.alcampo.es/api/webproductpagews/v6/product-pages/search?q=pechuga%20de%20pollo&tag=web&maxPageSize=50&maxProductsToDecorate=50&includeAdditionalPageInfo=true' \
  -H 'accept: application/json'
```
