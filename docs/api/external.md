# External API

Integrations that do not use the browser SDK call the **external** HTTP API with an API key:

- Base path: `/api/v1/external/…`
- Auth: `X-API-Key` header (same keys as Org → API Keys)
- Writes: `Idempotency-Key` header (required on customer, event, and product upserts)

Production: `https://api.salesbudge.com`  
OpenAPI (when enabled on your environment): `/swagger-ui/index.html`

## Core operations

| Operation | Method | Notes |
|-----------|--------|-------|
| Upsert customer / PII | `PUT /customers` | Creates lead when automation rules allow |
| Ingest event | `POST /events` | Platform defaults + merchant custom catalog names |
| Register device | `POST /devices/register` | Anonymous session bootstrap |
| Sync products | `PUT /products/catalog` | Preferred catalog setup |

### Event ingest

`eventName` must be a **platform default** (`NEW_CUSTOMER`, `PRODUCT_VIEWED`, `CATEGORY_VIEWED`, `INTERESTED`, `DEMO_REQUESTED`) or a **custom event** defined in Org → Event catalog.

Custom events validate `properties` keys against the catalog field list (max 15 keys). Unknown names return `422 UNSUPPORTED_EVENT_NAME`.

## Device register context

`POST /api/v1/external/devices/register` accepts optional browser context alongside `deviceKey` / `customerId`:

`userAgent`, `locale`, `languages`, `timezone`, `platform`, `screenWidth`, `screenHeight`, `latitude`, `longitude`.

Country is taken from edge headers (`CloudFront-Viewer-Country`, `CF-IPCountry`, …) when present and stored on the device and customer.

Full authenticated merchant REST (JWT) is **not** documented here — that is private platform API.
