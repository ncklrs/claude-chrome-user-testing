# Session Recorder Skill

Record user testing sessions as Playwright Traces for replay and analysis. Traces capture the full testing session including screenshots, DOM snapshots, network requests, and console logs.

## When to Use

- `--record` flag is present in the command
- User wants to replay or share a testing session
- Need to debug issues found during testing
- Creating documentation of UX issues

## Trace File Format

Playwright Trace files (`.zip`) contain:
- **Screenshots**: Captured at each action
- **DOM Snapshots**: Full page state at each step
- **Network Requests**: All HTTP requests/responses
- **Console Logs**: JavaScript console output
- **Action Timeline**: Timestamped sequence of all interactions

## Implementation

### Starting a Recording

At the beginning of a test session when `--record` flag is present:

```javascript
// Via browser_run_code
async (page) => {
  // Ensure recordings directory exists
  const fs = require('fs');
  const path = require('path');
  const recordingsDir = process.env.RECORD_PATH || 'recordings';
  if (!fs.existsSync(recordingsDir)) {
    fs.mkdirSync(recordingsDir, { recursive: true });
  }

  // Start tracing
  await page.context().tracing.start({
    screenshots: true,
    snapshots: true,
    sources: false  // Don't include source code
  });

  return 'Recording started';
}
```

### Stopping a Recording

At the end of a test session:

```javascript
// Via browser_run_code
async (page) => {
  const tracePath = process.env.TRACE_PATH;
  await page.context().tracing.stop({ path: tracePath });
  return `Trace saved to: ${tracePath}`;
}
```

## Trace Filename Convention

Format: `{command}-{persona}-{YYYY-MM-DD-HHmmss}.zip`

Examples:
- `user-test-genz-digital-native-2025-01-06-143022.zip`
- `stripe-test-impulse-buyer-2025-01-06-143156.zip`
- `user-test-boomer-tech-averse-2025-01-06-150830.zip`

### Generating Filename

```javascript
const generateTracePath = (command, personaId, basePath = 'recordings') => {
  const now = new Date();
  const timestamp = now.toISOString()
    .replace(/[-:]/g, '')
    .replace('T', '-')
    .slice(0, 15);
  return `${basePath}/${command}-${personaId}-${timestamp}.zip`;
};
```

## Recording Flow

### 1. Pre-Test Setup

When `--record` flag is detected:

```
1. Parse --record-path or use default 'recordings/'
2. Generate trace filename
3. Ensure output directory exists
4. Start Playwright tracing
5. Announce: "Starting session recording..."
```

### 2. During Test

- All browser interactions are automatically captured
- Screenshots taken at each action
- Network requests logged
- No additional steps needed

### 3. Post-Test Cleanup

```
1. Stop tracing with output path
2. Verify file was created
3. Report: "Session recorded to: {path}"
4. Report: "View at: https://trace.playwright.dev"
```

## Viewing Traces

Traces can be viewed at: https://trace.playwright.dev

### Instructions for Users

1. Open [trace.playwright.dev](https://trace.playwright.dev) in browser
2. Drag and drop the `.zip` trace file
3. Explore the timeline:
   - Click actions to see screenshots
   - View network requests tab
   - Check console output
   - See DOM snapshots at each step

### Trace Viewer Features

| Tab | Description |
|-----|-------------|
| Actions | Timeline of all interactions |
| Network | HTTP requests and responses |
| Console | JavaScript console output |
| Source | (Optional) Source code context |

## Integration with Test Modes

### Standard Mode

```
Starting session recording...

"Hi, I'm Harold. Let me see what this website is about..."
[Full narration continues]

Session recorded to: recordings/user-test-boomer-tech-averse-2025-01-06-143022.zip
View trace at: https://trace.playwright.dev (drag and drop the file)
```

### Quiet Mode (`--quiet --record`)

```
Testing https://example.com as boomer-tech-averse...
Recording session...

[Screenshot: confusion-nav.png]
[Screenshot: task-complete.png]

# Summary
- Tasks: 2/3 completed
- Issues: 1 critical, 2 major

Session recorded to: recordings/user-test-boomer-tech-averse-2025-01-06-143022.zip
View trace at: https://trace.playwright.dev
```

### Multi-Persona Mode (`--personas --record`)

Each persona test gets its own trace file:

```
recordings/
├── user-test-boomer-tech-averse-2025-01-06-143022.zip
├── user-test-genz-digital-native-2025-01-06-143024.zip
└── user-test-millennial-tech-skeptic-2025-01-06-143026.zip
```

## Error Handling

### Tracing Not Supported

If browser doesn't support tracing:
```
Warning: Session recording not available. Continuing without recording.
```

### Directory Creation Failed

If recordings directory can't be created:
```
Error: Could not create recordings directory. Check permissions.
```

### Trace Save Failed

If trace file can't be saved:
```
Warning: Failed to save trace file. Test results are still available.
```

## Best Practices

1. **Use with CI/CD**: Combine `--record` with `--quiet` for automated testing
2. **Share Traces**: Trace files are self-contained, easily shareable
3. **Storage**: Traces can be large (10-50MB); manage storage accordingly
4. **Privacy**: Traces may contain sensitive data; handle appropriately
