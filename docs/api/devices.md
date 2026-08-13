# Devices (external API)

Register a browser/app device and optionally link it to a customer. The SDK calls this on `init()`.

## Endpoint

```http
POST https://api.salesbudge.com/api/v1/external/devices/register
```

## Headers

| Header | Required |
|--------|----------|
| `X-API-Key` | Yes (`INTEGRATION`) |
| `Idempotency-Key` | Yes |
| `Content-Type` | `application/json` |

## Request body

| Field | Required | Max / range | Notes |
|-------|----------|-------------|-------|
| `deviceKey` | **Yes** | 200 | Stable id for this browser/app install |
| `customerId` | No | UUID | Link to existing customer; omit to create/keep anonymous |
| `userAgent` | No | 512 | Browser UA string |
| `locale` | No | 64 | e.g. `en-IN` |
| `languages` | No | 256 | Comma-separated language tags |
| `timezone` | No | 64 | IANA, e.g. `Asia/Kolkata` |
| `platform` | No | 64 | e.g. `MacIntel` |
| `screenWidth` | No | integer | |
| `screenHeight` | No | integer | |
| `latitude` | No | -90 … 90 | Optional geolocation |
| `longitude` | No | -180 … 180 | Optional geolocation |

## Country resolution (server)

Stored on the device and customer when possible:

1. Edge / CDN country headers when present (`CloudFront-Viewer-Country`, `CF-IPCountry`, …)  
2. Common IANA timezone → country mapping (e.g. `Asia/Kolkata` → `IN`)  
3. Reverse geocode of `latitude` / `longitude` when provided  

## curl

```bash
curl -s -X POST "https://api.salesbudge.com/api/v1/external/devices/register" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: device-browser-abc123" \
  -H "Content-Type: application/json" \
  -d '{
    "deviceKey": "browser-abc123",
    "locale": "en-IN",
    "languages": "en-IN,en",
    "timezone": "Asia/Kolkata",
    "platform": "MacIntel",
    "screenWidth": 1440,
    "screenHeight": 900,
    "userAgent": "Mozilla/5.0 ..."
  }'
```

## Sample response

`201 Created`:

```json
{
  "id": "9b0c2e1c-c2e8-4052-b276-b177145b928d",
  "merchantId": "49f9a104-4442-423a-b105-75aa9279fb21",
  "deviceKey": "browser-abc123",
  "customerId": "2f84920f-b2b6-496f-8f43-93db05f4ba9a",
  "linkType": "ANONYMOUS",
  "linkedAt": "2026-08-13T10:00:00.000Z",
  "replayed": false
}
```

### `linkType` values

| Value | Meaning |
|-------|---------|
| `ANONYMOUS` | Linked to an anonymous visitor customer |
| `IDENTIFIED` | Linked to an identified customer |

Use returned `customerId` for subsequent events until `identify` merges/replaces identity.

## Related

- [SDK install](../sdk/install.md) (calls this automatically)  
- [Customers](customers.md)
