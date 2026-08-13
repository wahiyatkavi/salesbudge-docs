# Events (external API)

Ingest a business event for a customer. Used by the browser SDK `track*()` methods and by servers.

## Endpoint

```http
POST https://api.salesbudge.com/api/v1/external/events
```

## Headers

| Header | Required |
|--------|----------|
| `X-API-Key` | Yes (`INTEGRATION` or `SDK`) |
| `Idempotency-Key` | Yes (use the same value as `eventId`) |
| `Content-Type` | `application/json` |

## Rate limit

**120 requests / minute** per API key (or IP if unauthenticated). Over limit → `429 RATE_LIMITED`.

## Request body

| Field | Required | Max / type | Accepted values / notes |
|-------|----------|------------|-------------------------|
| `eventId` | **Yes** | 200 | Unique per merchant; retries must reuse the same id |
| `eventName` | **Yes** | 200 | See [accepted names](#accepted-eventname-values) |
| `customerId` | One of ref\* | UUID | Preferred when known |
| `email` | ↑ | 320 | Resolves / creates customer |
| `phone` | ↑ | 50 | Resolves / creates customer |
| `externalId` | ↑ | 200 | Your customer external id |
| `productId` | No | UUID | Catalog product UUID |
| `productVariantId` | No | UUID | Optional variant |
| `quantity` | No | integer | Commerce |
| `unitPrice` | No | decimal | Commerce |
| `currency` | No | 3 | e.g. `USD`, `INR` |
| `properties` | No | **≤ 15 keys** | String keys; values JSON-serializable |
| `occurredAt` | No | ISO-8601 | Defaults to ingest time |

\*At least one of: `customerId`, `email`, `phone`, `externalId`.

## Accepted `eventName` values

### Platform defaults (always available)

| Name | Typical use |
|------|-------------|
| `NEW_CUSTOMER` | After identify / new contact |
| `PRODUCT_VIEWED` | Product detail page |
| `CATEGORY_VIEWED` | Category browse — set `properties.category` |
| `INTERESTED` | Strong interest / add-to-cart style |
| `DEMO_REQUESTED` | Contact / demo / quote form |

Names are case-insensitive on ingest and stored uppercase.

### Custom events

- Defined in CRM → **Org → Event catalog** (max **10** per merchant)  
- Name pattern: `^[A-Z][A-Z0-9_]{0,62}$` (e.g. `NEWSLETTER_SIGNUP`)  
- Properties must match the catalog allowlist → otherwise `422 UNKNOWN_EVENT_FIELD`  
- Unknown name → `422 UNSUPPORTED_EVENT_NAME`  

See [Custom events](../concepts/custom-events.md).

## Product without a synced catalog

Pass soft references in `properties`:

```json
"properties": {
  "productExternalId": "lead-management",
  "productName": "Lead Management",
  "category": "Sales",
  "source": "website"
}
```

## curl examples

### Product viewed

```bash
curl -s -X POST "https://api.salesbudge.com/api/v1/external/events" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: evt-pv-001" \
  -H "Content-Type: application/json" \
  -d '{
    "eventId": "evt-pv-001",
    "eventName": "PRODUCT_VIEWED",
    "email": "ada@example.com",
    "properties": {
      "productExternalId": "lead-management",
      "productName": "Lead Management",
      "category": "Sales",
      "source": "docs-curl"
    }
  }'
```

### Demo requested

```bash
curl -s -X POST "https://api.salesbudge.com/api/v1/external/events" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: evt-demo-001" \
  -H "Content-Type: application/json" \
  -d '{
    "eventId": "evt-demo-001",
    "eventName": "DEMO_REQUESTED",
    "customerId": "6ad6e6d1-2f9c-4c1a-8c8b-2a6e6b5a9b21",
    "properties": { "source": "contact-form" }
  }'
```

### Category viewed

```bash
curl -s -X POST "https://api.salesbudge.com/api/v1/external/events" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: evt-cat-001" \
  -H "Content-Type: application/json" \
  -d '{
    "eventId": "evt-cat-001",
    "eventName": "CATEGORY_VIEWED",
    "email": "ada@example.com",
    "properties": { "category": "Sales" }
  }'
```

## Sample response

`201 Created`:

```json
{
  "id": "664ab46c-ddf1-4082-b904-68c9cc7cb8d1",
  "merchantId": "49f9a104-4442-423a-b105-75aa9279fb21",
  "customerId": "6ad6e6d1-2f9c-4c1a-8c8b-2a6e6b5a9b21",
  "eventId": "evt-pv-001",
  "eventName": "PRODUCT_VIEWED",
  "occurredAt": "2026-08-13T10:05:00.000Z",
  "ingestedAt": "2026-08-13T10:05:00.200Z",
  "replayed": false
}
```

## Errors

| Code | HTTP | When |
|------|------|------|
| `MISSING_CUSTOMER_REFERENCE` | 422 | No customerId/email/phone/externalId |
| `UNSUPPORTED_EVENT_NAME` | 422 | Name not in defaults or catalog |
| `UNKNOWN_EVENT_FIELD` | 422 | Custom event property not allowlisted |
| `TOO_MANY_EVENT_FIELDS` | 422 | More than 15 properties |
| `EVENT_ID_ALREADY_EXISTS` | 409 | Different payload reused `eventId` incorrectly |
| `CUSTOMER_NOT_FOUND` | 404 | `customerId` unknown |
| `RATE_LIMITED` | 429 | Over 120/min |
| `MISSING_IDEMPOTENCY_KEY` | 400 | Header missing |

## Related

- [Concepts: events](../concepts/events.md)  
- [SDK identify & events](../sdk/identify-and-events.md)  
- [Custom events](../concepts/custom-events.md)
