# External API

Integrations that do not use the browser SDK call the **external** HTTP API with an API key:

- Base path: `/api/v1/external/…`
- Auth: API key header (same keys as Org → API Keys)
- Common operations: device register, event ingest, product catalog sync

Production: `https://api.salesbudge.com`  
OpenAPI (when enabled on your environment): `/swagger-ui/index.html`

Full authenticated merchant REST (JWT) is **not** documented here — that is private platform API.
