# Network Throttling Skill

Simulate slow or offline network conditions during user testing. Tests how well sites handle poor connectivity.

## When to Use

- Testing loading states and spinners
- Validating offline/PWA functionality
- Simulating real-world mobile conditions
- Testing error handling for slow requests
- Pairing with `bad-connection-user` persona

## Network Presets

### slow-3g

Simulates a poor mobile connection:

| Metric | Value |
|--------|-------|
| Download | 400 Kbps |
| Upload | 400 Kbps |
| Latency | 400ms |

**Use for**: Testing patience thresholds, loading indicators, progressive loading

### fast-3g

Simulates a decent mobile connection:

| Metric | Value |
|--------|-------|
| Download | 1.6 Mbps |
| Upload | 750 Kbps |
| Latency | 100ms |

**Use for**: Typical mobile user experience, image loading, API response times

### offline

Simulates no network access:

| Metric | Value |
|--------|-------|
| Download | 0 |
| Upload | 0 |
| Status | Offline |

**Use for**: Service worker testing, offline fallbacks, cached content

## Implementation via CDP

Network throttling uses Chrome DevTools Protocol:

```javascript
async (page) => {
  const client = await page.context().newCDPSession(page);

  await client.send('Network.emulateNetworkConditions', {
    offline: false,
    downloadThroughput: 50000,   // bytes/sec (400 Kbps)
    uploadThroughput: 50000,
    latency: 400                 // ms
  });

  return 'Network throttling applied';
}
```

### Throughput Conversion

| Speed | Bytes/sec |
|-------|-----------|
| 400 Kbps | 50,000 |
| 750 Kbps | 93,750 |
| 1.6 Mbps | 200,000 |
| 10 Mbps | 1,250,000 |

## Persona Patience Adjustment

When throttling is active, automatically adjust persona timing:

| Network | Patience Multiplier |
|---------|---------------------|
| normal | 1x |
| fast-3g | 1.5x |
| slow-3g | 3x |
| offline | N/A (expect failures) |

## Testing Patterns

### Loading States

```
1. Apply slow-3g throttling
2. Navigate to page
3. Observe: Does spinner appear? How long until content?
4. Narrate: "This is taking a while... at least there's a loading indicator."
```

### Offline Mode

```
1. Apply offline throttling
2. Try to navigate
3. Observe: Does site handle gracefully? Cached content?
4. Narrate: "Nothing's loading. Let me check if there's an offline mode..."
```

### Progressive Loading

```
1. Apply fast-3g throttling
2. Navigate to image-heavy page
3. Observe: Do images load progressively? Placeholders?
4. Narrate: "The text loaded first, images are still coming in..."
```

## Report Section

When network throttling is used, include in report:

```markdown
## Session Overview
- **Network**: slow-3g (400 Kbps, 400ms latency)

## Network Experience

### Loading Performance
- Page load time: ~8 seconds
- First meaningful paint: ~3 seconds
- Loading indicator: Yes/No

### Issues Under Throttling
- [List issues exacerbated by slow network]

### Positive Behaviors
- [What the site did well under poor conditions]
```

## Combination Testing

### Mobile + Slow Network

Most realistic mobile testing:

```
/user-test --url https://example.com --persona genz-digital-native --viewport mobile --network slow-3g
```

Tests: Real-world mobile conditions where users are impatient AND on poor connections.

### Bad Connection Persona + Throttling

Full experience simulation:

```
/user-test --url https://example.com --persona bad-connection-user --network slow-3g
```

The `bad-connection-user` persona already has higher patience for slow loads. Adding actual throttling makes the simulation realistic.

## Best Practices

1. **Start with fast-3g**: Most common real-world scenario
2. **Use slow-3g for stress testing**: Catches edge cases
3. **Test offline intentionally**: PWA and service worker validation
4. **Combine with mobile viewport**: Most realistic testing
5. **Match persona expectations**: Don't use slow-3g with impatient personas unless testing abandonment
6. **Note loading indicators**: Key UX element under slow conditions
