# Errors & troubleshooting

External API errors return JSON:

```json
{
  "timestamp": "2026-08-13T10:00:00.000Z",
  "status": 422,
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
| `EMPTY_FILE` / `INVALID_CSV` | CRM bulk upload only | Fix CSV header/rows |

### 401 Unauthorized

| Code | Meaning | Fix |
|------|---------|-----|
| (auth) | Invalid / missing / revoked API key | Create a new Integration key; check `X-API-Key` |

### 403 Forbidden

| Code | Meaning | Fix |
|------|---------|-----|
| `API_KEY_SCOPE_DENIED` | `SDK` key used on non-events route | Use an **Integration** key for full SDK / customers / devices / products |

### 404 Not Found

| Code | Meaning | Fix |
|------|---------|-----|
| `CUSTOMER_NOT_FOUND` | Unknown `customerId` | Upsert customer first or pass email/phone |
| `API_KEY_NOT_FOUND` | CRM key ops | — |

### 409 Conflict

| Code | Meaning | Fix |
|------|---------|-----|
| `IDENTITY_CONFLICT` | Identifiers map to different customers | Resolve duplicate identities |
| `IDENTIFIER_ALREADY_ASSIGNED` | Email/phone owned elsewhere | Use the existing customer |
| `EXTERNAL_ID_CONFLICT` | Different externalId already set | Don’t change externalId arbitrarily |
| `EVENT_ID_ALREADY_EXISTS` | Same eventId, conflicting reuse | New unique `eventId` for new events |
| `IDEMPOTENCY_IN_PROGRESS` | Concurrent same key | Retry shortly |
| `CUSTOMER_ALREADY_ANONYMIZED` | Anonymize twice | Stop |

### 422 Unprocessable

| Code | Meaning | Fix |
|------|---------|-----|
| `UNSUPPORTED_EVENT_NAME` | Name not in defaults/catalog | Use a platform default or add custom event in CRM |
| `UNKNOWN_EVENT_FIELD` | Property not allowlisted for custom event | Update event catalog fields |
| `TOO_MANY_EVENT_FIELDS` | > 15 properties | Reduce payload |
| `MISSING_CUSTOMER_REFERENCE` | Event without customer ref | Pass customerId / email / phone / externalId |
| `IDEMPOTENCY_KEY_REUSED` | Same key, different body | New key for new payload |

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
