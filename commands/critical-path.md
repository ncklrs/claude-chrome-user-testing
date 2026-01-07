---
description: Test critical user journeys defined in your project. Validates must-work paths like purchase flows, signups, and other essential functionality.
---

# Critical Path Command

Test must-work user journeys. Critical paths are essential flows that must always work - if they fail, users can't accomplish core tasks.

## Arguments

Parse the following from `$ARGUMENTS`:

- `--url <url>` (required): Base URL for testing
- `--path <name>` (optional): Run specific path only (default: all paths)
- `--persona <id>` (optional): Override default persona for all paths
- `--quiet` (optional): Summary only output
- `--fail-fast` (optional): Stop on first failure
- `--record` (optional): Record sessions as Playwright Traces
- `--record-path <path>` (optional): Custom trace output path

## Defining Critical Paths

Create `.claude/critical-paths.json` in your project:

```json
{
  "paths": [
    {
      "name": "purchase-flow",
      "description": "Complete purchase from browse to confirmation",
      "persona": "impulse-buyer",
      "priority": "critical",
      "steps": [
        { "action": "navigate", "to": "/" },
        { "action": "task", "do": "find and select a product" },
        { "action": "task", "do": "add to cart" },
        { "action": "task", "do": "proceed to checkout" },
        { "action": "task", "do": "complete guest checkout" },
        { "action": "verify", "element": "order confirmation number" }
      ]
    },
    {
      "name": "account-creation",
      "description": "New user registration flow",
      "persona": "genz-digital-native",
      "priority": "critical",
      "steps": [
        { "action": "navigate", "to": "/signup" },
        { "action": "task", "do": "complete signup form" },
        { "action": "task", "do": "submit registration" },
        { "action": "verify", "element": "welcome message or dashboard" }
      ]
    },
    {
      "name": "password-reset",
      "description": "Password recovery flow",
      "persona": "boomer-tech-averse",
      "priority": "high",
      "steps": [
        { "action": "navigate", "to": "/login" },
        { "action": "task", "do": "find forgot password link" },
        { "action": "task", "do": "enter email address" },
        { "action": "task", "do": "submit reset request" },
        { "action": "verify", "element": "confirmation message" }
      ]
    }
  ]
}
```

## Step Types

### navigate
Go to a specific URL path.

```json
{ "action": "navigate", "to": "/products" }
```

### task
Perform a task as the persona would.

```json
{ "action": "task", "do": "add the first product to cart" }
```

### verify
Confirm an element or state exists.

```json
{ "action": "verify", "element": "success message" }
{ "action": "verify", "text": "Thank you for your order" }
{ "action": "verify", "url": "/confirmation" }
```

### wait
Wait for a condition (optional).

```json
{ "action": "wait", "for": "page load" }
{ "action": "wait", "seconds": 2 }
```

### input
Enter specific data.

```json
{ "action": "input", "field": "email", "value": "test@example.com" }
```

## Priority Levels

| Priority | Meaning | On Failure |
|----------|---------|------------|
| `critical` | Must work, site is broken without it | Report as CRITICAL |
| `high` | Very important, major feature | Report as HIGH |
| `medium` | Important but has workarounds | Report as MEDIUM |
| `low` | Nice to have | Report as LOW |

## Report Format

### Standard Report

```markdown
# Critical Path Report

## Summary
- **Paths Tested**: 3
- **Passed**: 2
- **Failed**: 1
- **Date**: [timestamp]

## Results

### PASS: purchase-flow
- **Description**: Complete purchase from browse to confirmation
- **Persona**: impulse-buyer
- **Duration**: 45s
- **Steps**: 6/6 completed

### PASS: account-creation
- **Description**: New user registration flow
- **Persona**: genz-digital-native
- **Duration**: 28s
- **Steps**: 4/4 completed

### FAIL: password-reset
- **Description**: Password recovery flow
- **Persona**: boomer-tech-averse
- **Priority**: HIGH
- **Failed At**: Step 4 - "submit reset request"
- **Error**: Button click did not trigger form submission
- **Duration**: 15s (stopped at failure)

![Failure Screenshot](screenshots/critical-password-reset-fail.png)

## Action Required

### CRITICAL Issues
None

### HIGH Priority Issues
1. **password-reset**: Reset form submission broken
   - Step 4 failed: Form did not submit
   - Impact: Users cannot recover forgotten passwords
   - Recommendation: Check form submission handler
```

### Quiet Mode

```
Critical Path Test: https://example.com
Testing 3 paths...

[1/3] purchase-flow.......... PASS (45s)
[2/3] account-creation....... PASS (28s)
[3/3] password-reset......... FAIL (15s)
      Failed at: Step 4 - submit reset request
      Error: Button click had no effect

Results: 2/3 PASSED
Critical: 0 failures
High: 1 failure

FAIL: 1 critical path broken
```

## Execution Flow

### Phase 1: Load Paths

1. Read `.claude/critical-paths.json`
2. If `--path` specified, filter to that path
3. Sort by priority (critical first)

### Phase 2: Execute Each Path

For each path:

1. Announce path name and description
2. Load specified persona (or override)
3. Execute each step in order
4. On step failure:
   - Capture screenshot
   - Record error details
   - If `--fail-fast`: stop all testing
   - Otherwise: continue to next path
5. Record pass/fail status and timing

### Phase 3: Report

1. Generate summary
2. List all results
3. Highlight failures with details
4. Provide recommendations

## Example Usage

### Run all critical paths
```
/critical-path --url https://example.com
```

### Run specific path
```
/critical-path --url https://example.com --path purchase-flow
```

### CI/CD mode with fail-fast
```
/critical-path --url https://example.com --quiet --fail-fast
```

### Override persona for all paths
```
/critical-path --url https://example.com --persona screen-reader-user
```

### With recording for debugging
```
/critical-path --url https://example.com --record
```

## No Paths Defined

If `.claude/critical-paths.json` doesn't exist:

```
No critical paths defined.

Create .claude/critical-paths.json to define your critical user journeys.

Example:
{
  "paths": [
    {
      "name": "main-flow",
      "description": "Primary user journey",
      "persona": "genz-digital-native",
      "steps": [
        { "action": "navigate", "to": "/" },
        { "action": "task", "do": "complete the main action" },
        { "action": "verify", "element": "success indicator" }
      ]
    }
  ]
}

Or run smoke tests instead:
/smoke-test --url https://example.com --preset navigation
```

## CI/CD Integration

### Exit Codes (Conceptual)

| Scenario | Status |
|----------|--------|
| All paths pass | SUCCESS |
| Any critical path fails | FAILURE |
| Only low priority fails | WARNING |

### Example CI Usage

```
# Run critical paths before deploy
/critical-path --url $STAGING_URL --quiet --fail-fast

# If passes, continue with deployment
```

## Best Practices

1. **Define early**: Create critical paths when building features
2. **Keep paths focused**: Each path tests one journey
3. **Use appropriate personas**: Match persona to the flow
4. **Test regularly**: Run before every deployment
5. **Update when features change**: Keep paths current
6. **Prioritize correctly**: Only mark truly essential flows as critical
