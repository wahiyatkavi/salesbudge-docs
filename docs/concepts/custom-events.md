# Custom events

Define merchant-specific event names beyond the five platform defaults.

## Limits

| Limit | Value |
|-------|-------|
| Max custom events per merchant | **10** |
| Name pattern | `^[A-Z][A-Z0-9_]{0,62}$` |
| Examples | `NEWSLETTER_SIGNUP`, `WEBINAR_REGISTERED` |
| Property allowlist | Configured per event in CRM |
| Max properties on ingest | **15** |

## Setup (CRM)

1. Open **Organization → Event catalog**.  
2. Permission needed to edit: `ORG_MANAGE_EVENT_CATALOG` (view: `ORG_VIEW_EVENT_CATALOG`).  
3. Create an event name and optional allowed property field names.  

## Send from SDK

```javascript
CustomerPlatform.track("NEWSLETTER_SIGNUP", {
  listId: "weekly",
});
```

## Send from API

```bash
curl -s -X POST "https://api.salesbudge.com/api/v1/external/events" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: newsletter-001" \
  -H "Content-Type: application/json" \
  -d '{
    "eventId": "newsletter-001",
    "eventName": "NEWSLETTER_SIGNUP",
    "email": "ada@example.com",
    "properties": { "listId": "weekly" }
  }'
```

## Common errors

| Code | Cause |
|------|-------|
| `UNSUPPORTED_EVENT_NAME` | Name not created in catalog (and not a platform default) |
| `UNKNOWN_EVENT_FIELD` | Property key not in the event’s allowlist |
| `TOO_MANY_EVENT_FIELDS` | More than 15 properties |

## Related

- [Events concept](events.md)  
- [Events API](../api/events.md)
