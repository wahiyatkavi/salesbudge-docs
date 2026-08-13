# Errors & troubleshooting

External API errors return JSON:

```json
{
  "timestamp": "2026-08-13T10:00:00.000Z",
  "status": 400,
  "code": "UNSUPPORTED_EVENT_NAME",
  "message": "…",
  "requestId": "uuid"
}
```

Always log `code` and `requestId` when debugging.

## By HTTP status

### 400 Bad Request

| Code | Meaning | Fix |
|------|---------|-----|
| `MISSING_IDEMPOTENCY_KEY` | Write without `Idempotency-Key` | Add header |
| `MISSING_CUSTOMER_IDENTITY` | Customer upsert with no identity | Send email, phone, externalId, or anonymousCustomerId |
| `UNSUPPORTED_EVENT_NAME` | Event name not in defaults/catalog | Use a platform default or add custom event in CRM |
| `UNKNOWN_EVENT_FIELD` | Custom event property not allowlisted / no fields configured | Configure fields in Org → Event catalog |
| `TOO_MANY_EVENT_FIELDS` | > 15 properties | Reduce payload |
| `EMPTY_FILE` / `INVALID_CSV` | CRM bulk upload only | Fix CSV header/rows |

### 401 Unauthorized

| Code | Meaning | Fix |
|------|---------|-----|
| `AUTHENTICATION_REQUIRED` | Missing API key | Send `X-API-Key` |
| `INVALID_API_KEY` | Invalid / revoked / expired key | Create a new Integration key |

### 403 Forbidden

| Code | Meaning | Fix |
|------|---------|-----|
| `API_KEY_SCOPE_DENIED` | `SDK` key used on non-events route | Use an **Integration** key for customers / devices / products |
| `FORBIDDEN` | CRM user missing a permission (e.g. products) | Need Owner or Marketing manager+ for catalog in CRM |

### 404 Not Found

| Code | Meaning | Fix |
|------|---------|-----|
| `CUSTOMER_NOT_FOUND` | Customer id missing on CRM/detail lookups | Use a known id from identify / prior ingest response |
| `API_KEY_NOT_FOUND` | CRM key ops | — |

### 409 Conflict

| Code | Meaning | Fix |
|------|---------|-----|
| `IDENTITY_CONFLICT` | Identifiers map to different customers | Resolve duplicate identities |
| `IDENTIFIER_ALREADY_ASSIGNED` | Email/phone owned elsewhere | Use the existing customer |
| `EXTERNAL_ID_CONFLICT` | Different externalId already set | Don’t change externalId arbitrarily |
| `EVENT_ALREADY_INGESTED` | Same `eventId` already stored | New unique `eventId` for new events; retries must reuse the same id |
| `IDEMPOTENCY_IN_PROGRESS` | Concurrent same key | Retry shortly |
| `CUSTOMER_ALREADY_ANONYMIZED` | Anonymize twice | Stop |

### 422 Unprocessable

| Code | Meaning | Fix |
|------|---------|-----|
| `IDEMPOTENCY_KEY_REUSED` | Same idempotency key, different body | New key for new payload |

### 429 Too Many Requests

| Code | Meaning | Fix |
|------|---------|-----|
| `RATE_LIMITED` | Event ingest > 120/min | Back off and retry |

## Troubleshooting checklist

1. **Ping works?**  
   `GET /api/v1/external/ping` with your key → `"authenticated": true`.

2. **Wrong key type?**  
   Website SDK needs **Integration**, not SDK-only.

3. **Browser SDK silent?**  
   Set `debug: true` in `init()`. Check Network for `/external/` calls and console errors.

4. **Events missing in CRM?**  
   Confirm `eventName` spelling; check Marketing → Event explorer; confirm retention window.

5. **Lead not created?**  
   Need PII (email/phone/name) + automation rules enabled; anonymous views alone don’t create leads.

6. **Geolocation prompt?**  
   Optional. `collectGeolocation: false` skips it; denial never blocks init.

7. **CORS?**  
   External API allows browser origins. Prefer the official SDK for websites.

## Related

- [Authentication](authentication.md)  
- [Idempotency](idempotency.md)  
- [Getting started](../getting-started.md)
