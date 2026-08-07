# Install the browser SDK

Load the minified bundle and config before your app scripts:

```html
<script>
  window.__CUSTOMER_PLATFORM_CONFIG__ = {
    apiBaseUrl: "https://api.salesbudge.com",
    sdkScriptUrl: "https://cdn.salesbudge.com/customer-platform.min.js",
    apiKey: "cp_live_…",
    debug: false
  };
</script>
<script src="https://cdn.salesbudge.com/customer-platform.min.js"></script>
```

Local development can point `apiBaseUrl` at `http://localhost:8080` and `sdkScriptUrl` at a local `sdk/customer-platform.min.js` copy.

Source: GitHub `salesbudge-sdk` (build with `npm run build`).
