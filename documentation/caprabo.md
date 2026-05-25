# Caprabo (Capraboacasa) API

There is **no JSON API** — Caprabo's online store at
`www.capraboacasa.com` is server-rendered HTML. The trick: every product card
embeds a GA4 enhanced-ecommerce JSON object in a `data-metrics` attribute, so
you scrape the search page and decode the attribute. No auth, no cookies,
no Cloudflare.

Caprabo is owned by **Eroski**, so the catalog is the Eroski catalog —
brands frequently show as `EROSKI` / `E. NATUR`.

The corporate site `www.caprabo.com` is a separate, useless property; don't
use it. Its `sitemap.xml` is stale (last-modified 2016).

## Endpoint

```
GET https://www.capraboacasa.com/es/search/results/?q=<term>
```

- `q` — search term (Spanish, e.g. `pechuga+de+pollo`).
- Returns ~800KB of HTML with up to ~50 product cards.

## Response shape

Each product card carries:

```html
<div data-metrics="{ ...HTML-entity-encoded JSON... }" ...>
```

Once HTML-unescaped and JSON-parsed, the structure is:

```json
{
  "event": "select_item",
  "ecommerce": {
    "currency": "EUR",
    "items": [{
      "price": 5.7,
      "item_name": "Pechugas enteras pollo formato ahorro XXL EROSKI, bandeja aprox. 950 g",
      "item_id": "19230572",
      "item_brand": "EROSKI.",
      "item_category": "2059698", "item_category2": "2059746", "item_category3": "2059753",
      "item_affiliation": "Eroski"
    }]
  }
}
```

Use:

- `price` — pack price in €.
- `item_name` — product name; **pack size is embedded as text** (e.g. `"bandeja aprox. 950 g"`, `"320 g"`, `"docena"`).
- `item_id` — product ID; detail URL is `/es/productdetail/{item_id}-{slug}/`.

There is **no €/kg field** — compute it from `price ÷ pack_size`, parsing
the size out of `item_name`. Same data-metrics block appears 3× per card
(image-link, title-link, add-to-cart-button) — dedupe by `item_id`.

## Example

```
curl 'https://www.capraboacasa.com/es/search/results/?q=pechuga+de+pollo' \
  -H 'accept: text/html' -o page.html
python3 -c "
import re, html, json
page = open('page.html').read()
seen = {}
for m in re.findall(r'data-metrics=\"([^\"]+)\"', page):
    for it in json.loads(html.unescape(m)).get('ecommerce', {}).get('items', []):
        seen.setdefault(it['item_id'], it)
for it in seen.values():
    print(f\"{it['price']:>6.2f} €  {it['item_name']}\")
"
```

Recent run for `pechuga de pollo` returned 17 distinct products, cheapest
€5.70 for a ~950g pack (≈ €6.00/kg).
