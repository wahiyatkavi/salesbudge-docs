# Event delivery (SDK)

The browser SDK is built for unreliable networks.

## How delivery works

1. Events are written to **localStorage** before send.  
2. A background flush sends batches (`batchSize`, default 10) on an interval (`flushIntervalMs`, default 5s).  
3. On success, events are removed from the queue.  
4. On 5xx / network errors, exponential backoff retries (about 1s → 10 minutes).  
5. Retries reuse the same `eventId` and `Idempotency-Key` (safe).  
6. Queue restores after refresh / `init()`.

## Limits

| Setting | Default | Behavior |
|---------|---------|----------|
| `maxQueueSize` | 100 | Oldest dropped when full |
| `maxEventAgeMs` | 30 days | Stale events dropped |

## Helpers

```javascript
CustomerPlatform.getPendingEventCount();
CustomerPlatform.getDroppedEventCount();
await CustomerPlatform.flush(); // force send now
```

## Debugging

```javascript
await CustomerPlatform.init({
  apiKey: "cp_live_…",
  apiBaseUrl: "https://api.salesbudge.com",
  debug: true,
});
```

Check the browser Network tab for:

- `POST …/external/devices/register`  
- `PUT …/external/customers`  
- `POST …/external/events`  

## Related

- [Idempotency](../api/idempotency.md)  
- [Errors](../api/errors.md)
