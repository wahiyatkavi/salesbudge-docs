# Authentication

All **external** (integrator) HTTP calls use an API key. CRM dashboard calls use JWT login and are not documented here.

## Header

```http
X-API-Key: cp_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

| Rule | Detail |
|------|--------|
| Format | Starts with `cp_live_` |
| Where to create | CRM → **Organization → API Keys** |
| Shown once | Copy the full secret at creation time |
| Transport | HTTPS only in production |

## Key types

| Type | Allowed routes |
|------|----------------|
| `INTEGRATION` | All `/api/v1/external/**` — **use this for websites / full SDK** |
| `SDK` | Only `POST /api/v1/external/events` |

### Example: wrong key type

```bash
# SDK-scoped key calling customers → 403
curl -s -o /tmp/out.json -w "%{http_code}" \
  -X PUT "https://api.salesbudge.com/api/v1/external/customers" \
  -H "X-API-Key: cp_live_SDK_SCOPED_KEY" \
  -H "Idempotency-Key: test-1" \
  -H "Content-Type: application/json" \
  -d '{"email":"a@example.com"}'
```

Response body typically includes:

```json
{
  "status": 403,
  "code": "API_KEY_SCOPE_DENIED",
  "message": "..."
}
```

## Ping (auth check)

```bash
curl -s "https://api.salesbudge.com/api/v1/external/ping" \
  -H "X-API-Key: $SB_API_KEY"
```

**200**

```json
{
  "merchantId": "49f9a104-4442-423a-b105-75aa9279fb21",
  "authenticated": true
}
```

**401** — missing, invalid, revoked, disabled, or expired key.

## Security checklist

- Never put the key in public Git repos or client-side source that you do not control. The browser SDK needs a key in the page; treat it as a **publishable** website key and revoke/rotate if abused. Prefer domain allowlists / rate limits operationally.
- Prefer server-side `INTEGRATION` keys for backend jobs.
- Rotate compromised keys in the CRM (revoke old, create new).

## Related

- [Idempotency](idempotency.md) — required on writes  
- [Errors](errors.md) — `401` / `403` codes  
- [Getting started](../getting-started.md)
