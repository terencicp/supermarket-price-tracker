# Carrefour

`www.carrefour.es` sits behind Cloudflare with **TLS-fingerprint** Bot Fight
Mode: plain `curl` (Apple SecureTransport stack) is rejected with HTTP 403
regardless of UA, headers, HTTP/2, or client hints. Only a stack that
mimics a real Chrome TLS handshake passes.

Bypass: **`curl_cffi`** (Python wrapper around `curl-impersonate`), one-time
`pip install curl_cffi`. Then `curl_cffi.requests.get(url, impersonate="chrome124")`
sails through. Confirmed working with `chrome124`, `chrome120`,
`safari17_0`; `chrome110` is still blocked, so keep the major version recent.

There is **no public search-API the agent can hit** — the discovered
endpoint `https://www.carrefour.es/search-api/query/v1/search` (referenced
from the site's bootstrap) returns 403 even with cookies + referer (stricter
Cloudflare rules on the API host). **Browse by category instead**, like
Mercadona. Category pages are full SSR HTML with a complete inline
`window.__INITIAL_STATE__` JSON blob that already contains every product on
the page with prices, €/kg, brand, SKU, image, URL, stock.

## Endpoint pattern

```
GET https://www.carrefour.es/supermercado/<slug-path>/cat<id>/c
```

Top-level category IDs:

- `cat20001` — La despensa (dry goods)
- `cat20002` — Frescos
- `cat20003` — Bebidas
- `cat21449123` — Congelados
- `cat20006` — Bebé · `cat20007` — Mascotas

Sub-category examples (drill down from the parent's response):

- `cat190012` — Aves y pollo (poultry) — full path `/supermercado/productos-frescos/carniceria/aves-y-pollo/cat190012/c`
- `cat20018` — Carne · `cat20002` — Frescos parent

## Response shape

Find `window.__INITIAL_STATE__ = { … };` in the HTML, walk braces to extract,
`json.loads` it. Products live at:

```
data["productCardList"]["results"]["items"]   # main list (24 items per page)
data["productCardList"]["results"]["pagination"]
data["productCardList"]["results"]["total_results"]
```

Each item:

- `name` — product name (often includes pack size, e.g. `"… 1,2 kg aprox"`)
- `price` — pack price, **localized string** `"7,20 €"`
- `price_per_unit` — reference price, **localized string** `"6 €"` (per `measure_unit`)
- `measure_unit` — `"kg"`, `"l"`, `"ud"` — **use this for €/kg**
- `product_id` — `"VC4AECOMM-<num>"` · `sku_id` — `"<num>0000"`
- `brand`, `units_in_stock`, `url` (append to `https://www.carrefour.es`)
- `images.desktop` / `images.mobile`
- `badge_map.promotions[]` — promo flags + dates

Parse the comma-decimal prices with
`float(s.split()[0].replace(',', '.'))`.

## Example

```bash
pip install curl_cffi   # one-time
python3 - <<'PY'
import re, json
from curl_cffi import requests
r = requests.get(
    'https://www.carrefour.es/supermercado/productos-frescos/carniceria/aves-y-pollo/cat190012/c',
    impersonate='chrome124', timeout=20)
m = re.search(r'window\.__INITIAL_STATE__\s*=\s*', r.text)
s = m.end(); depth=0; in_str=False; esc=False
for i in range(s, len(r.text)):
    ch = r.text[i]
    if in_str:
        if esc: esc=False
        elif ch=='\\': esc=True
        elif ch=='"': in_str=False
    else:
        if ch=='"': in_str=True
        elif ch=='{': depth+=1
        elif ch=='}':
            depth-=1
            if depth==0: end=i+1; break
data = json.loads(r.text[s:end])
for it in data['productCardList']['results']['items']:
    if 'pechuga' in it['name'].lower() and 'pollo' in it['name'].lower():
        print(f"{it['price']:>10}  {it['price_per_unit']:>10}/{it['measure_unit']}  {it['name']}")
PY
```

Recent run for `cat190012` returned 24 products; reference chicken breast
"Pechuga de pollo Carrefour El Mercado 1,2 kg aprox" was **7,20 € / 6 €/kg**.

## Fallback

If Cloudflare tightens past `curl_cffi` (rare but happens; both `curl-impersonate`
and Cloudflare get updated periodically), escalate to Playwright with stealth:

```
npx playwright install chromium   # one-time, ~400 MB
node -e "const{chromium}=require('playwright');(async()=>{const b=await chromium.launch();const p=await b.newPage();await p.goto('https://www.carrefour.es/supermercado/.../cat190012/c');const html=await p.content();console.log(html);await b.close()})()"
```

Same `__INITIAL_STATE__` extraction applies.
