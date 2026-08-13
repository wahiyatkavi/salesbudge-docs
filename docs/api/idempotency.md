# Idempotency

Every **write** to the external API requires an `Idempotency-Key` header so retries are safe.

## Header

```http
Idempotency-Key: your-stable-unique-string
```

| Rule | Detail |
|------|--------|
| Required on | `PUT` / `POST` under `/api/v1/external/**` (customers, events, devices, products) |
| Scope | Per merchant |
| Same key + same body | Returns the original response; often includes `"replayed": true` |
| Same key + different body | `422 IDEMPOTENCY_KEY_REUSED` |
| Concurrent duplicate | `409 IDEMPOTENCY_IN_PROGRESS` |
| Missing | `400 MISSING_IDEMPOTENCY_KEY` |

## How to choose keys

| Operation | Suggested key pattern |
|-----------|------------------------|
| Identify / upsert customer | `identify-{email}` or `cust-{externalId}` |
| Event | Same as `eventId` (convenient; SDK uses a separate stable key pair) |
| Device register | `device-{deviceKey}` (SDK may add suffixes such as `-anonymous`) |
| Catalog sync | `catalog-{version}` e.g. `catalog-2026-08-13` |

Keys should be unique **per logical operation**. Reusing a key for a different payload fails.

## Example

```bash
curl -s -X PUT "https://api.salesbudge.com/api/v1/external/customers" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: identify-ada@example.com" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "ada@example.com",
    "firstName": "Ada",
    "lastName": "Lovelace"
  }'
```

First call → `200` with `"created": true` (or `"created": false` if matched).  
Identical retry → `200` with `"replayed": true` (same customer id).

## SDK behavior

The browser SDK always sets `Idempotency-Key` for you (device register, identify, events, catalog). You do not set it manually when using `CustomerPlatform.*`.

## Related

- [Authentication](authentication.md)  
- [Errors](errors.md)
