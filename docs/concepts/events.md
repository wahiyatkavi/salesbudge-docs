# Events (concepts)

Every meaningful activity is an **event**: product view, identify, demo request, custom actions, and more.

- Events are immutable and time-ordered.  
- CRM objects (leads, pipeline) are **derived** from identity + events + automation rules.  
- Ingest validates `eventName` against **platform defaults** + your **custom catalog**.

## Platform defaults

| Event | Typical trigger | SDK helper |
|-------|-----------------|------------|
| `NEW_CUSTOMER` | After identify / new contact | `trackNewCustomer()` |
| `PRODUCT_VIEWED` | Product page | `trackProductView()` |
| `CATEGORY_VIEWED` | Category browse | `trackCategoryView(category)` |
| `INTERESTED` | Strong interest / cart-like | `trackInterested()` |
| `DEMO_REQUESTED` | Contact / demo form | `trackDemoRequested()` |

Names are case-insensitive on ingest; stored uppercase.

## Property rules

| Rule | Limit |
|------|-------|
| Max properties per event | **15** |
| SDK caller-provided properties | **10** (SDK may add locale/page context) |
| Custom event properties | Must be allowlisted in Event catalog |

## Marketing funnel (CRM)

Fixed MVP stages:

**Landing → Product → Interested → Demo → Won**

Custom event volume is reported separately (not mixed into those five stages).

## How to send events

| Path | Docs |
|------|------|
| Browser | [SDK identify & events](../sdk/identify-and-events.md) |
| HTTP | [Events API](../api/events.md) |
| Custom names | [Custom events](custom-events.md) |
