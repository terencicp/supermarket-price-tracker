# Condis API

Condis search runs on **Empathy.co**, a third-party search SaaS. The endpoint
is reachable directly — **no cookies or session needed**. JSON.

The `/api/proxy/signin?provider=anonymous` flow on `compraonline.condis.es` is a
red herring: it only gates cart/account routes (`/private/*`), not search.

## Endpoint

```
GET https://api.empathy.co/search/v1/query/condis/search
```

Query parameters:

- `query` — search term. **The param is `query`, not `q`.**
- `lang=es`
- `store=718` — **mandatory** (Condis' store ID). Omitting it → `400 Missing mandatory parameters: store`.
- `start` — pagination offset (e.g. `0`)
- `rows` — page size (e.g. `24`)

## Response shape

Top-level keys: `catalog`, `direct`, `banner`, `promoted`. Use `catalog`:

- `catalog.numFound` — total hit count
- `catalog.content[]` — products
- `catalog.facets[]`, `catalog.pagination`, `catalog.stats`

Each product:

- `id`, `externalId`
- `description`, `brand`
- `price` — `{ current, regular }`
- `pum` — price-per-unit **string**, e.g. `"0,87€/Litro"` or `"8,96€/Kg"` —
  must be parsed (comma decimal, localized unit); there is no numeric €/kg field
- `section`, `family`, `category[]`
- `url` — append to `https://compraonline.condis.es`
- `images[]` — resolve against `https://cdn.condis.es`

## Optional helper

- `GET https://api.empathy.co/search/v1/query/condis/topclicked?lang=es&store=718&rows=N`
  — popular products. (`/suggestions`, `/nextqueries`, `/popularsearches` are
  not configured for this tenant.)

## Example

```
curl 'https://api.empathy.co/search/v1/query/condis/search?query=pechuga%20de%20pollo&lang=es&store=718&start=0&rows=24' \
  -H 'accept: application/json'
```
