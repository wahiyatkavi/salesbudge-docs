# Getting started

Follow these steps once. After that you can integrate without SalesBudge support.

## 1. Create your organization

1. Go to https://app.salesbudge.com and sign up.  
2. Verify your email.  
3. You are redirected to your tenant CRM: `https://{slug}.salesbudge.com`.

> Joining an **existing** org is invitation-only (Org → Team). Signup always creates a **new** organization.

## 2. Create an API key

1. In the CRM open **Organization → API Keys**.  
2. Create a key of type **Integration** (required for full SDK: customers, devices, products, events).  
3. Copy the secret once (`cp_live_…`). Store it in your secret manager / env vars — it is not shown again.

| Key type | Can call |
|----------|----------|
| `INTEGRATION` | All `/api/v1/external/**` routes (recommended for websites) |
| `SDK` | **Only** `POST /api/v1/external/events` |

Using an `SDK` key for `identify` / device register returns `403 API_KEY_SCOPE_DENIED`.

## 3. Verify the key

```bash
export SB_API_KEY="cp_live_YOUR_KEY"

curl -s "https://api.salesbudge.com/api/v1/external/ping" \
  -H "X-API-Key: $SB_API_KEY"
```

```json
{
  "merchantId": "49f9a104-4442-423a-b105-75aa9279fb21",
  "authenticated": true
}
```

## 4. Pick an integration path

### Website (most common)

1. [Install the browser SDK](sdk/install.md)  
2. Follow [Website integration](guides/website-integration.md)  
3. Confirm **Marketing → Visitors** and **Sales → Leads** in the CRM  

### Server / backend only

1. Read [Authentication](api/authentication.md) and [Idempotency](api/idempotency.md)  
2. Follow [Server-only integration](guides/server-integration.md)  

## 5. Confirm data in the CRM

| What you sent | Where to look |
|---------------|---------------|
| Anonymous browsing | Marketing → Visitors |
| `identify()` / customer upsert with email/phone | Marketing → Visitors → Identified |
| Intent events (`DEMO_REQUESTED`, etc.) + PII | Sales → Leads (when automation rules allow) |
| Product catalog sync | Org → Products (if enabled) / event product names |

## Base URLs

| Environment | API | SDK |
|-------------|-----|-----|
| Production | `https://api.salesbudge.com` | `https://cdn.salesbudge.com/sdk/customer-platform.min.js` |
| Local (engineers) | `http://localhost:8080` | Local build of the SDK |

## Next

- [SDK install](sdk/install.md)  
- [External API overview](api/external.md)  
- [Event names & funnel](concepts/events.md)  
- [Error codes](api/errors.md)
