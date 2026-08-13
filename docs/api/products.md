# Products (external API)

Keep your product catalog in sync so events and leads can attach product context.

**Preferred:** batch sync via `/products/catalog` (or SDK `syncCatalog()`).

## Upsert one product

```http
PUT https://api.salesbudge.com/api/v1/external/products
```

### Request body

| Field | Required | Notes |
|-------|----------|-------|
| `externalId` | **Yes** | Your SKU / slug (stable) |
| `name` | **Yes** | Display name |
| `description` | No | |
| `category` | No | Used in analytics / lead routing |
| `brand` | No | |

### curl

```bash
curl -s -X PUT "https://api.salesbudge.com/api/v1/external/products" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: product-lead-management" \
  -H "Content-Type: application/json" \
  -d '{
    "externalId": "lead-management",
    "name": "Lead Management",
    "description": "Capture and assign leads",
    "category": "Sales",
    "brand": "Acme"
  }'
```

### Sample response

```json
{
  "id": "3d91254b-96cc-4ce0-9e04-b948ce8c594e",
  "merchantId": "49f9a104-4442-423a-b105-75aa9279fb21",
  "externalId": "lead-management",
  "name": "Lead Management",
  "description": "Capture and assign leads",
  "category": "Sales",
  "brand": "Acme",
  "status": "ACTIVE",
  "createdAt": "2026-08-13T10:00:00.000Z",
  "updatedAt": "2026-08-13T10:00:00.000Z"
}
```

`status` is typically `ACTIVE` or `DISABLED`.

## Sync catalog (batch)

```http
PUT https://api.salesbudge.com/api/v1/external/products/catalog
```

### Request body

```json
{
  "products": [
    {
      "externalId": "lead-management",
      "name": "Lead Management",
      "category": "Sales"
    },
    {
      "externalId": "team-rbac",
      "name": "Team & Role-Based Access",
      "category": "Team"
    }
  ]
}
```

### curl

```bash
curl -s -X PUT "https://api.salesbudge.com/api/v1/external/products/catalog" \
  -H "X-API-Key: $SB_API_KEY" \
  -H "Idempotency-Key: catalog-v1" \
  -H "Content-Type: application/json" \
  -d '{
    "products": [
      {"externalId":"lead-management","name":"Lead Management","category":"Sales"},
      {"externalId":"team-rbac","name":"Team & Role-Based Access","category":"Team"}
    ]
  }'
```

### Sample response

```json
{
  "products": [
    {
      "id": "3d91254b-96cc-4ce0-9e04-b948ce8c594e",
      "merchantId": "49f9a104-4442-423a-b105-75aa9279fb21",
      "externalId": "lead-management",
      "name": "Lead Management",
      "category": "Sales",
      "status": "ACTIVE",
      "createdAt": "2026-08-13T10:00:00.000Z",
      "updatedAt": "2026-08-13T10:00:00.000Z"
    }
  ],
  "totalCount": 2
}
```

## SDK equivalent

```javascript
await CustomerPlatform.syncCatalog([
  { externalId: "lead-management", name: "Lead Management", category: "Sales" },
]);
```

## Related

- [Events](events.md) — attach `productExternalId` in properties  
- [SDK reference](../sdk/reference.md)
