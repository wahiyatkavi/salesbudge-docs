# Identify and events

After the SDK loads:

- **Anonymous browsing** — the SDK registers a device and records page/product events.
- **Lead capture** — call `identify({ email, … })` on form submit, then track an intent event (e.g. demo request).

Typical website flow: home → product views → contact form → lead appears in CRM **Sales**.

Event names and payloads must match your merchant event catalog (defaults + custom events configured in the CRM).

## Device, locale, and location

On `init()`, the SDK registers the browser device and sends:

| Field | Source |
|-------|--------|
| `userAgent`, `platform`, screen size | Browser |
| `locale`, `languages` | `navigator.language` / `languages` |
| `timezone` | `Intl` resolved time zone |
| Country | Derived server-side from CDN/edge headers (e.g. CloudFront) |
| `latitude` / `longitude` | Only if `init({ collectGeolocation: true })` (may prompt the user) |

Each `track()` call auto-enriches properties (when space allows) with `locale`, `timezone`, `pageUrl`, and `referrer`. Callers may still pass up to **10** custom property fields; with context the payload may include up to **15**.

See also: [Events concept](../concepts/events.md) · [External API](../api/external.md)
