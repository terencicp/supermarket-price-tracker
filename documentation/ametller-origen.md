# Ametller Origen API

Storefront `www.ametllerorigen.com` is a Mobify PWA on **Salesforce
Commerce Cloud** (SCAPI). Public HTML has no prices — they're loaded via
the Shopper Search API after a SLAS guest-token dance.

Two reachable hosts:

- Direct: `https://4jppt37a.api.commercecloud.salesforce.com` — used below.
- Cloudflare-fronted proxy: `https://www.ametllerorigen.com/mobify/proxy/api` — same paths, drop in if the direct host is ever blocked.

## Config

Found inline in any product page's first `<script>` tag (look for
`"commerceAPI"` in the `__CONFIG__` blob):

```
clientId       fd3c9db8-2a0d-4f4b-9e74-294e068f9ae4
organizationId f_ecom_blzv_prd
shortCode      4jppt37a
siteId         ametller
redirect_uri   https://www.ametllerorigen.com/callback   # only one that's whitelisted
```

## Auth: SLAS guest token (PKCE, 2 calls)

This is a SLAS **public** client — no secret, must use PKCE. Naive
`client_credentials` + `Basic <client_id:>` returns 400 "Invalid header".

1. `GET /shopper/auth/v1/organizations/{orgId}/oauth2/authorize?client_id=…&channel_id=ametller&code_challenge=…&redirect_uri=https://www.ametllerorigen.com/callback&response_type=code&hint=guest&usid=<uuid>` — **don't follow the 303**, grab `code` (and `usid`) from the `Location` header.
2. `POST /shopper/auth/v1/organizations/{orgId}/oauth2/token` (form-urlencoded) with `grant_type=authorization_code_pkce`, `code`, `code_verifier`, `client_id`, `channel_id=ametller`, `redirect_uri`, `usid` → returns `access_token` (valid ~30 min).

## Endpoint: product search

```
GET https://4jppt37a.api.commercecloud.salesforce.com/search/shopper-search/v1/organizations/f_ecom_blzv_prd/product-search?siteId=ametller&q=<term>&limit=20
Authorization: Bearer <access_token>
```

Search terms must be in **Catalan** (`pit de pollastre`, `llet`, `ou`).

## Response shape

Each `hits[]` entry:

- `productId`, `productName`, `currency` (`"EUR"`)
- `price` — pack price (€)
- `pricePerUnit` — **€/kg** (or €/L) — use this directly, like Mercadona's `reference_price`
- `image.link` — product image URL
- `c_instaleapHit.quantity` + `c_instaleapHit.unit` — pack quantity and unit (`0.415` + `"KG"` = 415g)
- `orderable` — availability

Product detail at
`GET /product/shopper-products/v1/organizations/{orgId}/products/{id}?siteId=ametller`
adds `unitMeasure` (`"KG"`/`"L"`), `unitQuantity`, `ean`, `brand`, plus
custom `c_ao_*` fields (nutrients, ingredients, etc.).

## Example

```bash
python3 - <<'PY'
import urllib.request, urllib.parse, json, base64, hashlib, secrets, uuid
ORG='f_ecom_blzv_prd'; CID='fd3c9db8-2a0d-4f4b-9e74-294e068f9ae4'
SITE='ametller'; RU='https://www.ametllerorigen.com/callback'
B='https://4jppt37a.api.commercecloud.salesforce.com'
v=base64.urlsafe_b64encode(secrets.token_bytes(64)).rstrip(b'=').decode()
ch=base64.urlsafe_b64encode(hashlib.sha256(v.encode()).digest()).rstrip(b'=').decode()
u=str(uuid.uuid4())
class NR(urllib.request.HTTPRedirectHandler):
    def redirect_request(self,*a,**k): return None
try:
    urllib.request.build_opener(NR).open(
        f'{B}/shopper/auth/v1/organizations/{ORG}/oauth2/authorize?'
        + urllib.parse.urlencode({'client_id':CID,'channel_id':SITE,
            'code_challenge':ch,'redirect_uri':RU,'response_type':'code',
            'hint':'guest','usid':u}))
except urllib.error.HTTPError as e: loc=e.headers['Location']
qs=urllib.parse.parse_qs(urllib.parse.urlparse(loc).query)
body=urllib.parse.urlencode({'grant_type':'authorization_code_pkce',
    'code':qs['code'][0],'code_verifier':v,'client_id':CID,'channel_id':SITE,
    'redirect_uri':RU,'usid':qs['usid'][0]}).encode()
tok=json.loads(urllib.request.urlopen(urllib.request.Request(
    f'{B}/shopper/auth/v1/organizations/{ORG}/oauth2/token',data=body,
    headers={'Content-Type':'application/x-www-form-urlencoded'},
    method='POST')).read())['access_token']
r=urllib.request.urlopen(urllib.request.Request(
    f'{B}/search/shopper-search/v1/organizations/{ORG}/product-search'
    f'?siteId={SITE}&q=pit+de+pollastre&limit=5',
    headers={'Authorization':f'Bearer {tok}'}))
for h in json.loads(r.read())['hits']:
    print(f"{h['price']:>6.2f}€  {h['pricePerUnit']:>6.2f}€/kg  {h['productName']}")
PY
```

Recent run for `pit de pollastre` returned 9 hits; cheapest €3.90,
representative €5.31 / €12.80/kg for the standard filetejat pack.
