# Identify and events

After the SDK loads:

- **Anonymous browsing** — the SDK registers a device and records page/product events.
- **Lead capture** — call `identify({ email, … })` on form submit, then track an intent event (e.g. demo request).

Typical website flow: home → product views → contact form → lead appears in CRM **Sales**.

Event names and payloads must match your merchant event catalog (defaults + custom events configured in the CRM).

See also: [Events concept](../concepts/events.md) · [External API](../api/external.md)
