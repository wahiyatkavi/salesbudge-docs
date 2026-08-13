# SalesBudge

Event-native operational intelligence for sales and marketing teams.

These docs are for **merchants and developers** integrating a website or backend with SalesBudge. Everything you need to go live without contacting support is here: SDK methods, HTTP APIs, curls, field rules, and error codes.

## Choose your path

| Path | Use when | Start here |
|------|----------|------------|
| **Browser SDK** | You have a website / SPA and want auto device + page context | [Install SDK](sdk/install.md) |
| **External HTTP API** | Server, mobile, or non-browser systems | [External API](api/external.md) |
| **Both** | Website SDK + server events (orders, webhooks) | [Website guide](guides/website-integration.md) + [Server guide](guides/server-integration.md) |

## Quick verify

```bash
curl -s "https://api.salesbudge.com/api/v1/external/ping" \
  -H "X-API-Key: cp_live_YOUR_KEY"
```

Expected:

```json
{
  "merchantId": "uuid",
  "authenticated": true
}
```

## Production URLs

| Surface | URL |
|---------|-----|
| Docs (this site) | https://docs.salesbudge.com |
| Sign up / login | https://app.salesbudge.com |
| Tenant CRM | `https://{slug}.salesbudge.com` |
| API | https://api.salesbudge.com |
| SDK CDN | https://cdn.salesbudge.com/sdk/customer-platform.min.js |

## Docs map

1. [Getting started](getting-started.md) — account, API key, first event  
2. [Website integration](guides/website-integration.md) — end-to-end browser recipe  
3. [SDK method reference](sdk/reference.md) — every `CustomerPlatform.*` method  
4. [External API](api/external.md) — curls for customers, events, devices, products  
5. [Events](concepts/events.md) — platform defaults + custom catalog  
6. [Errors](api/errors.md) — status codes and how to fix them  

OpenAPI (when enabled on your environment): `https://api.salesbudge.com/swagger-ui/index.html`
