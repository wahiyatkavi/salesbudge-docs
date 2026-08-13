# Server-only integration guide

Use the external HTTP API from your backend, mobile app, or ETL jobs.

## Prerequisites

1. **Integration** API key  
2. Read [Authentication](../api/authentication.md) and [Idempotency](../api/idempotency.md)  

```bash
export SB_API_KEY="cp_live_YOUR_KEY"
export BASE="https://api.salesbudge.com/api/v1/external"
```

## 1. Ping

```bash
curl -s "$BASE/ping" -H "X-API-Key: $SB_API_KEY"
```

## 2. Upsert customer

```bash
curl -s -X PUT "$BASE/customers" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: identify-user-42" \
  -H "Content-Type: application/json" \
  -d '{
    "externalId": "user-42",
    "email": "user42@example.com",
    "firstName": "Jordan",
    "lastName": "Lee"
  }'
```

Save returned `id` as `customerId`.

Full field table: [Customers](../api/customers.md).

## 3. Sync products (optional but recommended)

```bash
curl -s -X PUT "$BASE/products/catalog" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: catalog-v1" \
  -H "Content-Type: application/json" \
  -d '{
    "products": [
      {"externalId":"sku-1","name":"Widget","category":"Hardware"}
    ]
  }'
```

## 4. Ingest events

```bash
curl -s -X POST "$BASE/events" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: order-1001-interested" \
  -H "Content-Type: application/json" \
  -d '{
    "eventId": "order-1001-interested",
    "eventName": "INTERESTED",
    "externalId": "user-42",
    "properties": {
      "productExternalId": "sku-1",
      "source": "checkout"
    }
  }'
```

Accepted names and property rules: [Events API](../api/events.md).

## 5. Idempotent retries

On network failure, **retry with the same** `Idempotency-Key` and body. Do not generate a new key for the same logical operation.

## 6. Verify in CRM

Same as the website guide: Visitors, Identified, Leads, Event explorer.

## Related

- [Errors](../api/errors.md)  
- [Devices](../api/devices.md) if you manage device identity yourself
