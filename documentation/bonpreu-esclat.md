# Bonpreu Esclat API

Search API powering `compraonline.bonpreuesclat.cat`. Runs on the **same Last
Mile platform as Alcampo** — identical endpoint, params, and response shape.
No auth, no cookies. JSON.

## Endpoint

```
GET https://www.compraonline.bonpreuesclat.cat/api/webproductpagews/v6/product-pages/search
```

Same query parameters as Alcampo: `q`, `tag=web` (required), `maxPageSize`,
`maxProductsToDecorate`, `includeAdditionalPageInfo=true`. Retailer implied by
the host. See [alcampo.md](alcampo.md) for the full parameter and response
documentation.

## Response shape

Identical to Alcampo: `productGroups[].decoratedProducts[]` with `productId`,
`retailerProductId`, `name`, `brand`, `packSizeDescription`,
`price.{amount,currency}`, `unitPrice.price.amount` (€/kg), `available`,
`image.src`.

## Quirk: Catalan catalog

Bonpreu's catalog is in **Catalan**, not Spanish. Search terms must be
localized, e.g.:

- chicken breast → `pit de pollastre`
- milk → `llet`
- egg → `ou`

## Example

```
curl 'https://www.compraonline.bonpreuesclat.cat/api/webproductpagews/v6/product-pages/search?q=pit%20de%20pollastre&tag=web&maxPageSize=50&maxProductsToDecorate=50&includeAdditionalPageInfo=true' \
  -H 'accept: application/json'
```
