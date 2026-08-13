# Identify and events (SDK)

## Recommended website flow

```text
Page load → init() → anonymous device + customer
        → trackProductView / trackCategoryView while browsing
        → form submit → identify() → trackDemoRequested() → flush()
        → CRM: Identified visitor + Sales lead (when automation allows)
```

## Anonymous browsing

After `init()`, the SDK has a device and anonymous `customerId`. Track product/category views without email:

```javascript
CustomerPlatform.trackCategoryView("Sales");
CustomerPlatform.trackProductView({
  externalId: "lead-management",
  properties: { source: "website" },
});
```

## Lead capture (contact / demo form)

```javascript
async function onContactSubmit({ email, firstName, lastName, message }) {
  try {
    await CustomerPlatform.identify({
      email,
      firstName,
      lastName,
      productExternalId: "lead-management",
    });
    CustomerPlatform.trackDemoRequested({
      source: "contact-form",
      messagePreview: String(message || "").slice(0, 120),
    });
    await CustomerPlatform.flush();
    showThankYou(); // only after success
  } catch (err) {
    showError(err.message || "Could not submit. Please try again.");
  }
}
```

**Do not** show a success thank-you if `identify` or the intent event fails.

## Device, locale, and location

On `init()`, the SDK registers the device with:

| Field | Source |
|-------|--------|
| `userAgent`, `platform`, screen size | Browser |
| `locale`, `languages` | `navigator` |
| `timezone` | `Intl` |
| Country | Server-side (CDN headers / timezone / geocode) |
| `latitude` / `longitude` | Optional; `collectGeolocation` defaults to `true` |

Each `track()` may auto-add `locale`, `timezone`, `pageUrl`, `referrer` when there is room in the 15-property cap.

## Property limits

| Limit | Value |
|-------|-------|
| Caller properties per event | **10** |
| After SDK enrichment | **15** |

## Silent marketing home pages

Some marketing home pages load the SDK for session continuity but intentionally **do not** fire product/demo events. Track on catalog/product pages and successful form submit.

## Verify in CRM

| Step | CRM location |
|------|----------------|
| Anonymous session | Marketing → Visitors |
| After identify | Marketing → Visitors → Identified |
| Intent + PII | Sales → Leads |

## Related

- [Method reference](reference.md)  
- [Custom events](../concepts/custom-events.md)  
- [Website guide](../guides/website-integration.md)
