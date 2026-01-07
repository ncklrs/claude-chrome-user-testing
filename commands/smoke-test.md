---
description: Run pre-configured smoke tests for common user flows. Quick validation of login, signup, checkout, navigation, and search functionality using appropriate personas.
---

# Smoke Test Command

Run pre-built test configurations for common user flows. Each preset uses an appropriate default persona and validates specific functionality.

## Arguments

Parse the following from `$ARGUMENTS`:

- `--url <url>` (required): Target URL to test
- `--preset <preset>` (required): Preset name to run
- `--persona <id>` (optional): Override the default persona
- `--gender <m|f|n>` (optional): Gender variant
- `--quiet` (optional): Summary only, no narration
- `--record` (optional): Record session as Playwright Trace
- `--record-path <path>` (optional): Custom trace output path
- `--viewport <mobile|tablet|desktop>` (optional): Device viewport size (default: desktop)
- `--output <markdown|json>` (optional): Report output format (default: markdown)
- `--network <slow-3g|fast-3g|offline>` (optional): Network throttling preset

## Available Presets

| Preset | Default Persona | What It Tests |
|--------|-----------------|---------------|
| `login` | busy-executive | Authentication flow works |
| `signup` | genz-digital-native | Registration flow works |
| `checkout` | impulse-buyer | Purchase flow works |
| `navigation` | boomer-tech-averse | Site navigation works |
| `search` | power-user | Search functionality works |

## Preset Details

### login

Tests the authentication flow:
1. Find login link/button
2. Navigate to login page
3. Enter test credentials
4. Submit login form
5. Verify successful login (dashboard, welcome message, etc.)

**Default Persona**: `busy-executive` (expects quick, efficient login)

**Success Criteria**:
- Login form is accessible
- Form accepts credentials
- User is logged in after submission

### signup

Tests the registration flow:
1. Find signup/register link
2. Navigate to signup page
3. Complete registration form
4. Submit form
5. Verify account creation

**Default Persona**: `genz-digital-native` (expects minimal friction)

**Success Criteria**:
- Signup form is accessible
- Form fields are clear
- Registration completes successfully

### checkout

Tests the purchase flow:
1. Find a product
2. Add product to cart
3. Navigate to cart
4. Proceed to checkout
5. Verify checkout page loads

**Default Persona**: `impulse-buyer` (expects fast checkout)

**Success Criteria**:
- Products are findable
- Add to cart works
- Cart is accessible
- Checkout initiates

**Note**: Does not complete payment. Use `/stripe-test` for full payment testing.

### navigation

Tests site navigation:
1. Identify main navigation items
2. Visit each main section (up to 5)
3. Verify pages load
4. Check for dead ends

**Default Persona**: `boomer-tech-averse` (tests clarity)

**Success Criteria**:
- Navigation is visible and clear
- All main links work
- No dead-end pages

### search

Tests search functionality:
1. Find search input
2. Enter search term
3. Submit search
4. Verify results appear
5. Click a result

**Default Persona**: `power-user` (expects efficient search)

**Success Criteria**:
- Search is accessible
- Results are returned
- Results are clickable

## Pre-Flight Checklist

Before running:

1. **Chrome Integration**: Verify `claude --chrome` is active
2. **URL Accessible**: Navigate to target URL
3. **Preset Loading**: Load preset from `skills/smoke-testing/presets/`
4. **Persona Loading**: Load default persona (or override)

## Execution Flow

### Phase 1: Setup

```
"Running [preset] smoke test on [url]..."
"Using persona: [persona name]"
```

### Phase 2: Execute Preset Tasks

For each task in the preset:
1. Announce the task
2. Execute with persona timing (unless `--quiet`)
3. Capture screenshot on completion or failure
4. Record pass/fail status

### Phase 3: Report

```markdown
# Smoke Test Report: [preset]

## Configuration
- **URL**: [url]
- **Preset**: [preset]
- **Persona**: [name] ([id])
- **Date**: [timestamp]

## Results

| Step | Status | Notes |
|------|--------|-------|
| Find login | PASS | Found in header |
| Navigate to login | PASS | - |
| Enter credentials | PASS | - |
| Submit form | PASS | - |
| Verify login | PASS | Dashboard loaded |

## Summary
- **Status**: PASS / FAIL
- **Steps**: X/Y passed
- **Duration**: Xs

## Issues Found
[Any issues encountered]

## Screenshots
[Key moment screenshots]
```

## Quiet Mode Output

```
Smoke Test: login @ https://example.com
Persona: busy-executive

[1/5] Find login.......... PASS
[2/5] Navigate............ PASS
[3/5] Enter credentials... PASS
[4/5] Submit form......... PASS
[5/5] Verify login........ PASS

Result: PASS (5/5 steps)
Duration: 12s
```

## Example Usage

### Basic smoke test
```
/smoke-test --url https://example.com --preset login
```

### With custom persona
```
/smoke-test --url https://shop.example.com --preset checkout --persona comparison-shopper
```

### CI/CD mode
```
/smoke-test --url https://example.com --preset signup --quiet
```

### With recording
```
/smoke-test --url https://example.com --preset navigation --record
```

### Run multiple presets
```
/smoke-test --url https://example.com --preset login --quiet
/smoke-test --url https://example.com --preset navigation --quiet
/smoke-test --url https://example.com --preset search --quiet
```

## Custom Presets

Create custom presets in `.claude/smoke-presets/`:

```json
{
  "name": "onboarding",
  "description": "Test new user onboarding flow",
  "defaultPersona": "genz-digital-native",
  "tasks": [
    "complete signup",
    "skip or complete profile setup",
    "view welcome tutorial",
    "reach main dashboard"
  ],
  "successCriteria": [
    "User reaches dashboard",
    "Core features are accessible"
  ],
  "timeout": 180
}
```

Then run:
```
/smoke-test --url https://example.com --preset onboarding
```

## Error Handling

### Preset Not Found
```
Error: Preset '[name]' not found.

Available presets: login, signup, checkout, navigation, search

Check custom presets in: .claude/smoke-presets/
```

### Step Failed
```
[3/5] Submit form......... FAIL
  Error: Form submission failed - "Invalid email format"

Smoke test FAILED at step 3.
Remaining steps skipped.
```

### Timeout
```
[4/5] Verify login........ TIMEOUT
  Waited 30s for dashboard to load.

Smoke test FAILED - step timed out.
```
