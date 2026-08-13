# Customers (external API)

Create or update a customer (identity + optional PII). Used by the SDK `identify()` call and by server integrations.

## Endpoint

```http
PUT https://api.salesbudge.com/api/v1/external/customers
```

Alias (same body/behavior):

```http
PUT https://api.salesbudge.com/api/v1/external/customers/identify
```

## Headers

| Header | Required |
|--------|----------|
| `X-API-Key` | Yes (`INTEGRATION` key) |
| `Idempotency-Key` | Yes |
| `Content-Type` | `application/json` |

## Request body

| Field | Required | Max | Accepted values / notes |
|-------|----------|-----|-------------------------|
| `externalId` | One of identity\* | 200 | Your stable user id / CRM id |
| `email` | ↑ | 320 | Valid email; stored normalized lowercase |
| `phone` | ↑ | 50 | Digits / E.164-style; normalized server-side |
| `anonymousCustomerId` | ↑ | UUID | Link an anonymous SDK session into this identity |
| `firstName` | No | 100 | PII; can qualify lead automation |
| `lastName` | No | 100 | PII |
| `category` | No | 100 | Lead routing / product context hint |
| `productExternalId` | No | 200 | Lead routing hint (catalog external id) |

\*At least one of: `externalId`, `email`, `phone`, or `anonymousCustomerId`.

## curl

```bash
curl -s -X PUT "https://api.salesbudge.com/api/v1/external/customers" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: identify-ada@example.com" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "ada@example.com",
    "firstName": "Ada",
    "lastName": "Lovelace",
    "category": "Sales",
    "productExternalId": "lead-management"
  }'
```

### Link anonymous visitor → identified customer

```bash
curl -s -X PUT "https://api.salesbudge.com/api/v1/external/customers" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: identify-merge-ada" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "ada@example.com",
    "firstName": "Ada",
    "anonymousCustomerId": "2f84920f-b2b6-496f-8f43-93db05f4ba9a"
  }'
```

## Sample response

Always **`200 OK`**. Use the `created` flag to tell create vs update (keeps idempotent upserts simple — replays stay `200` too).

```json
{
  "id": "6ad6e6d1-2f9c-4c1a-8c8b-2a6e6b5a9b21",
  "merchantId": "49f9a104-4442-423a-b105-75aa9279fb21",
  "externalId": null,
  "status": "ACTIVE",
  "anonymous": false,
  "mergedIntoCustomerId": null,
  "version": 1,
  "identifiers": [
    {
      "type": "EMAIL",
      "value": "ada@example.com",
      "normalizedValue": "ada@example.com",
      "createdAt": "2026-08-13T10:00:00.000Z"
    }
  ],
  "firstName": "Ada",
  "lastName": "Lovelace",
  "createdAt": "2026-08-13T10:00:00.000Z",
  "updatedAt": "2026-08-13T10:00:00.000Z",
  "created": true,
  "replayed": false
}
```

### Response fields

| Field | Meaning |
|-------|---------|
| `id` | Customer UUID (use as `customerId` on events) |
| `status` | `ACTIVE`, `MERGED`, or `ANONYMIZED` |
| `anonymous` | `false` after identify with email/phone/externalId |
| `created` | `true` if this call created the row |
| `replayed` | `true` if this was an idempotent replay |

## Errors

| Code | HTTP | When |
|------|------|------|
| `MISSING_CUSTOMER_IDENTITY` | 400 | No identity field provided |
| `IDENTITY_CONFLICT` | 409 | Identifiers point at different customers |
| `IDENTIFIER_ALREADY_ASSIGNED` | 409 | Email/phone owned by another customer |
| `EXTERNAL_ID_CONFLICT` | 409 | Customer already has a different `externalId` |
| `MISSING_IDEMPOTENCY_KEY` | 400 | Header missing |
| `API_KEY_SCOPE_DENIED` | 403 | Using an `SDK`-scoped key |

## Side effects

- PII + enabled automation rules can **create a Sales lead** (source often `PII` / SDK).  
- After identify, the person moves from Marketing **Visitors (anonymous)** to **Identified**.

## Related

- [SDK identify](../sdk/identify-and-events.md)  
- [Events API](events.md)  
- [Identity concepts](../concepts/identity.md)
