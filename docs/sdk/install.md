# Install the browser SDK

## Production install

```html
<script src="https://cdn.salesbudge.com/sdk/customer-platform.min.js"></script>
<script>
  (async () => {
    await CustomerPlatform.init({
      apiKey: "cp_live_YOUR_INTEGRATION_KEY",
      apiBaseUrl: "https://api.salesbudge.com",
      debug: false,
      collectGeolocation: true,
    });
  })();
</script>
```

CDN path **must** include `/sdk/`:  
`https://cdn.salesbudge.com/sdk/customer-platform.min.js`

Use an **Integration** API key (not SDK-only). See [Authentication](../api/authentication.md).

## Config object pattern

Some sites set config before loading the script:

```html
<script>
  window.__CUSTOMER_PLATFORM_CONFIG__ = {
    apiBaseUrl: "https://api.salesbudge.com",
    apiKey: "cp_live_…",
    debug: false,
    collectGeolocation: true,
  };
</script>
<script src="https://cdn.salesbudge.com/sdk/customer-platform.min.js"></script>
<script>
  CustomerPlatform.init(window.__CUSTOMER_PLATFORM_CONFIG__);
</script>
```

## `init()` options

| Option | Required | Default | Description |
|--------|----------|---------|-------------|
| `apiKey` | **Yes** | — | `cp_live_…` Integration key |
| `apiBaseUrl` | No | `http://localhost:8080` | Production: `https://api.salesbudge.com` |
| `batchSize` | No | `10` | Events per flush batch |
| `flushIntervalMs` | No | `5000` | Auto-flush interval |
| `maxQueueSize` | No | `100` | Oldest events dropped when exceeded |
| `maxEventAgeMs` | No | 30 days | Drop stale queued events |
| `collectGeolocation` | No | `true` | May prompt; denial is OK |
| `debug` | No | `false` | Console logging |
| `storageKeyPrefix` | No | `"customer-platform"` | localStorage key prefix |

## What `init()` does

1. Registers the device → `POST /api/v1/external/devices/register`  
2. Restores any pending event queue from localStorage  
3. Starts auto-flush  

## Local development

```javascript
await CustomerPlatform.init({
  apiKey: "cp_live_…",
  apiBaseUrl: "http://localhost:8080",
  debug: true,
});
```

Serve a local SDK build if you are developing the SDK itself.

## Next

- [Method reference](reference.md)  
- [Identify & events](identify-and-events.md)  
- [Website integration guide](../guides/website-integration.md)
