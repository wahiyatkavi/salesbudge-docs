# Events

Every meaningful business activity is captured as a standardized event (page view, product view, identify, demo request, etc.).

- Events are immutable and ordered.
- Operational CRM state (leads, customers, pipeline) is derived and kept separately for day-to-day work.
- Merchants configure a **catalog** of allowed event names; ingest validates against platform defaults plus custom definitions.

## Platform defaults

| Event | Typical use |
|-------|-------------|
| `NEW_CUSTOMER` | After `identify()` |
| `PRODUCT_VIEWED` | Product detail / catalog item |
| `CATEGORY_VIEWED` | Category browse |
| `INTERESTED` | Cart / strong interest |
| `DEMO_REQUESTED` | Contact / demo form intent |

## Custom events

In the CRM (**Org → Event catalog**), merchants with `ORG_MANAGE_EVENT_CATALOG` may define up to **10** custom events (names like `MY_EVENT`). Users with `ORG_VIEW_EVENT_CATALOG` can read the catalog but not edit it.

Custom events may declare an allowlist of property field names. Ingest rejects unknown fields and caps payloads at **15** properties (including SDK context enrichment).

## Marketing funnel

The CRM funnel chart uses fixed MVP stages:

**Landing** → **Product** → **Interested** → **Demo** → **Won**

Custom event volume is reported separately via the marketing custom-events API (not mixed into the five funnel stages).

Use the [SDK](../sdk/install.md) on websites or the [external API](../api/external.md) from servers.
