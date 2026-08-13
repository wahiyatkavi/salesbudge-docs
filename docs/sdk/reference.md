# SDK method reference

Global: `window.CustomerPlatform` after the CDN script loads.

All network writes use `X-API-Key` and `Idempotency-Key` automatically.

## Lifecycle

### `init(options): Promise<void>`

See [Install](install.md) for the full options table.

```javascript
await CustomerPlatform.init({
  apiKey: "cp_live_…",
  apiBaseUrl: "https://api.salesbudge.com",
});
```

### `flush(): Promise<void>`

Send queued events immediately.

```javascript
await CustomerPlatform.flush();
```

## Identity

### `identify(traits): Promise<string | undefined>`

Creates/updates a customer and returns `customerId`.

| Trait | Required | Notes |
|-------|----------|-------|
| `email` | One of identity\* | |
| `externalId` | ↑ | Your user id |
| `phone` | ↑ | |
| `firstName` | No | PII |
| `lastName` | No | PII |
| `category` | No | Lead hint |
| `productExternalId` | No | Lead hint |

\*At least one of `email`, `externalId`, `phone` — or PII-only when an anonymous session already exists.

```javascript
const customerId = await CustomerPlatform.identify({
  email: "buyer@example.com",
  firstName: "Sam",
  lastName: "Lee",
  productExternalId: "lead-management",
});
```

Maps to `PUT /api/v1/external/customers` with `anonymousCustomerId` from the session.

## Events

Caller may pass up to **10** custom properties. SDK may add `locale`, `timezone`, `pageUrl`, `referrer` (total ≤ **15**).

### `track(eventName, properties?): Promise<void>`

```javascript
CustomerPlatform.track("NEWSLETTER_SIGNUP", { listId: "weekly" });
```

`eventName` must be a [platform default](../concepts/events.md) or a [custom catalog](../concepts/custom-events.md) name.

### Helpers

| Method | Event name | Notes |
|--------|------------|-------|
| `trackNewCustomer(props?)` | `NEW_CUSTOMER` | |
| `trackProductView({ productId?, externalId?, … })` | `PRODUCT_VIEWED` | Needs `productId` **or** `externalId` |
| `trackCategoryView(category, props?)` | `CATEGORY_VIEWED` | Sets `properties.category` |
| `trackInterested(options)` | `INTERESTED` | Needs product ref |
| `trackDemoRequested(props?)` | `DEMO_REQUESTED` | Contact / demo forms |
| `trackProductAddedToCart(options)` | `INTERESTED` | Deprecated alias |

```javascript
CustomerPlatform.trackProductView({
  externalId: "lead-management",
  properties: { source: "pricing-page" },
});

CustomerPlatform.trackCategoryView("Sales");

CustomerPlatform.trackDemoRequested({ source: "contact-form" });
```

## Catalog

### `syncCatalog(products[]): Promise<CatalogSyncResponse>`

```javascript
await CustomerPlatform.syncCatalog([
  { externalId: "sku-1", name: "Widget", category: "Hardware", brand: "Acme" },
]);
```

| Field | Required |
|-------|----------|
| `externalId` | Yes |
| `name` | Yes |
| `description` | No |
| `category` | No |
| `brand` | No |

### `upsertProduct(product): Promise<ProductResponse>`

Single-product upsert (same fields as one catalog item).

## Session helpers

| Method | Returns |
|--------|---------|
| `getCustomerId()` | Current customer UUID or `undefined` |
| `getDeviceKey()` | Device key string |
| `getPendingEventCount()` | Queued events waiting to send |
| `getDroppedEventCount()` | Events dropped (queue full / expired) |

## Related

- [Event delivery](delivery.md)  
- [Identify & events guide](identify-and-events.md)  
- [External events API](../api/events.md)
