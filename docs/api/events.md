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
| `Idempotency-Key` | Yes (unique per logical write; retries must reuse the same key + body) |
| `Content-Type` | `application/json` |

> Tip: many clients set `Idempotency-Key` equal to `eventId`. That is a convenient pattern, not a hard requirement — both values must stay stable across retries.

## Rate limit

**120 requests / minute** per API key (or IP if unauthenticated). Over limit → `429 RATE_LIMITED`.

## Request body

| Field | Required | Max / type | Accepted values / notes |
|-------|----------|------------|-------------------------|
| `eventId` | **Yes** | 200 | Unique per merchant; retries must reuse the same id |
| `eventName` | **Yes** | 200 | See [accepted names](#accepted-eventname-values) |
| `customerId` | Optional | UUID | If present and known → attach event. Unknown id → `404 CUSTOMER_NOT_FOUND` |
| `email` | Optional | 320 | Upserts (creates/updates) that person, then attaches the event |
| `phone` | Optional | 50 | Upserts person, then attaches the event |
| `externalId` | Optional | 200 | Upserts person by your external id, then attaches the event |
| `productId` | No | UUID | Catalog product UUID |
| `productVariantId` | No | UUID | Optional variant |
| `quantity` | No | integer | Commerce |
| `unitPrice` | No | decimal | Commerce |
| `currency` | No | 3 | e.g. `USD`, `INR` |
| `properties` | No | **≤ 15 keys** | String keys; values JSON-serializable |
| `occurredAt` | No | ISO-8601 | Defaults to ingest time |

### How the customer is resolved

| You send | What happens |
|----------|--------------|
| Known `customerId` | Attach event to that customer |
| `email` / `phone` / `externalId` (no `customerId`) | **Upsert** that identity, then attach event |
| None of the above | **Create an anonymous visitor**, then attach event |

PII on the event (`email` / `phone`) can also trigger lead automation the same way identify does.

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
- Property keys must be configured on that event in CRM **before** you send them  
- Empty property list in CRM → you may send the event **with no properties**; sending any property returns `400 UNKNOWN_EVENT_FIELD`  
- Unknown property key → `400 UNKNOWN_EVENT_FIELD`  
- Unknown name → `400 UNSUPPORTED_EVENT_NAME`  

See [Custom events](../concepts/custom-events.md).

## Product without a synced catalog

Pass soft references in `properties` (platform default events only — custom events still need CRM allowlisted keys):

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
| `UNSUPPORTED_EVENT_NAME` | 400 | Name not in defaults or catalog |
| `UNKNOWN_EVENT_FIELD` | 400 | Custom event property not allowlisted (or no fields configured yet) |
| `TOO_MANY_EVENT_FIELDS` | 400 | More than 15 properties |
| `EVENT_ALREADY_INGESTED` | 409 | Same `eventId` already stored for this merchant |
| `CUSTOMER_NOT_FOUND` | 404 | `customerId` provided but unknown |
| `RATE_LIMITED` | 429 | Over 120/min |
| `MISSING_IDEMPOTENCY_KEY` | 400 | Header missing |
| `IDEMPOTENCY_KEY_REUSED` | 422 | Same idempotency key, different body |

## Related

- [Concepts: events](../concepts/events.md)  
- [SDK identify & events](../sdk/identify-and-events.md)  
- [Custom events](../concepts/custom-events.md)
