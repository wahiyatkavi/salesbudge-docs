# External API overview

Base URL (production):

```text
https://api.salesbudge.com/api/v1/external
```

All routes require `X-API-Key`. All **writes** require `Idempotency-Key`. See [Authentication](authentication.md) and [Idempotency](idempotency.md).

## Endpoints

| Operation | Method | Path | Docs |
|-----------|--------|------|------|
| Health / auth check | `GET` | `/ping` | [Authentication](authentication.md) |
| Upsert customer | `PUT` | `/customers` | [Customers](customers.md) |
| Identify (alias) | `PUT` | `/customers/identify` | Same as `/customers` |
| Ingest event | `POST` | `/events` | [Events](events.md) |
| Register device | `POST` | `/devices/register` | [Devices](devices.md) |
| Upsert product | `PUT` | `/products` | [Products](products.md) |
| Sync catalog | `PUT` | `/products/catalog` | [Products](products.md) |

## Common headers

```http
Content-Type: application/json
X-API-Key: cp_live_…
Idempotency-Key: unique-per-operation
```

## Minimal end-to-end (server)

```bash
export SB_API_KEY="cp_live_YOUR_KEY"
export BASE="https://api.salesbudge.com/api/v1/external"

# 1) Ping
curl -s "$BASE/ping" -H "X-API-Key: $SB_API_KEY"

# 2) Upsert customer
curl -s -X PUT "$BASE/customers" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: identify-buyer@example.com" \
  -H "Content-Type: application/json" \
  -d '{"email":"buyer@example.com","firstName":"Sam"}'

# 3) Ingest event (replace CUSTOMER_UUID)
curl -s -X POST "$BASE/events" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: evt-demo-001" \
  -H "Content-Type: application/json" \
  -d '{
    "eventId": "evt-demo-001",
    "eventName": "DEMO_REQUESTED",
    "email": "buyer@example.com",
    "properties": { "source": "api-docs" }
  }'
```

## Rate limits

| Route | Limit | Error |
|-------|-------|-------|
| `POST /events` | 120 requests / minute / API key (or IP) | `429 RATE_LIMITED` |

## CORS

Browser calls to `/api/v1/external/**` are CORS-enabled. Prefer the [browser SDK](../sdk/install.md) for websites so device identity and retries are handled for you.

## What is not in this API

Merchant CRM features (team, pipeline UI, JWT login, audit log UI) use authenticated dashboard APIs and are not part of the public integrator surface.

## Related

- [Customers](customers.md) · [Events](events.md) · [Devices](devices.md) · [Products](products.md) · [Errors](errors.md)
