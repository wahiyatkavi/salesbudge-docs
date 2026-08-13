# Website integration guide

End-to-end recipe for a marketing / product website.

## Prerequisites

1. Integration API key from CRM → Org → API Keys  
2. [SDK installed](../sdk/install.md)  

## Step 1 — Init on every page

```html
<script src="https://cdn.salesbudge.com/sdk/customer-platform.min.js"></script>
<script>
  CustomerPlatform.init({
    apiKey: "cp_live_…",
    apiBaseUrl: "https://api.salesbudge.com",
  });
</script>
```

## Step 2 — Sync catalog (once per deploy or on catalog pages)

```javascript
await CustomerPlatform.syncCatalog([
  { externalId: "lead-management", name: "Lead Management", category: "Sales" },
  { externalId: "team-rbac", name: "Team & Role-Based Access", category: "Team" },
]);
```

Or use [Products API](../api/products.md) from CI.

## Step 3 — Track browsing

```javascript
// Category page
CustomerPlatform.trackCategoryView("Sales");

// Product page
CustomerPlatform.trackProductView({
  externalId: "lead-management",
  properties: { source: "website" },
});
```

## Step 4 — Capture leads on forms

```javascript
await CustomerPlatform.identify({
  email: form.email.value,
  firstName: form.firstName.value,
  lastName: form.lastName.value,
  productExternalId: "lead-management",
});
CustomerPlatform.trackDemoRequested({ source: "contact-form" });
await CustomerPlatform.flush();
```

Show thank-you **only** after success. See [Identify & events](../sdk/identify-and-events.md).

## Step 5 — Verify

| Check | Where |
|-------|-------|
| Anonymous traffic | CRM → Marketing → Visitors |
| Identified contacts | Marketing → Visitors → Identified |
| Leads | Sales → Leads |
| Raw events | Marketing → Event explorer |

## Optional: custom events

1. CRM → Org → Event catalog → create `NEWSLETTER_SIGNUP` with allowed fields  
2. `CustomerPlatform.track("NEWSLETTER_SIGNUP", { listId: "weekly" })`  

Details: [Custom events](../concepts/custom-events.md).

## Server companion

For order/webhook events that never hit the browser, also use the [server guide](server-integration.md).
