# Smoke Testing Skill

Run pre-configured smoke tests for common user flows. Quick validation that critical functionality works.

## What is Smoke Testing?

Smoke testing is a quick sanity check to ensure basic functionality works before deeper testing. The name comes from electronics - if you turn on a circuit and smoke comes out, you know something is wrong without further testing.

## When to Use

- Before releases to catch obvious breaks
- After deployments to verify functionality
- In CI/CD pipelines for quick validation
- Before detailed user testing sessions

## Built-in Presets

### login

**Purpose**: Verify authentication works

**Steps**:
1. Find login link or button on page
2. Navigate to login page
3. Locate email/username and password fields
4. Enter test credentials (use `test@example.com` / `testpassword123`)
5. Submit the form
6. Verify login succeeded (dashboard, user menu, etc.)

**Failure Indicators**:
- No login link found
- Form submission error
- Redirect back to login
- Error message displayed

### signup

**Purpose**: Verify registration works

**Steps**:
1. Find signup/register link
2. Navigate to registration page
3. Fill required fields (name, email, password)
4. Submit registration
5. Verify account created (welcome page, confirmation)

**Test Data**:
- Name: Test User
- Email: `test-{timestamp}@example.com`
- Password: TestPassword123!

### checkout

**Purpose**: Verify purchase flow initiates

**Steps**:
1. Browse to find any product
2. Click add to cart button
3. Navigate to cart
4. Click checkout/proceed button
5. Verify checkout page loads

**Does NOT**:
- Complete payment (use `/stripe-test` for that)
- Fill payment details
- Submit orders

### navigation

**Purpose**: Verify site navigation works

**Steps**:
1. Identify main navigation (header menu)
2. List navigation items
3. Visit first 5 unique sections
4. Verify each page loads
5. Check for navigation back to home

**Checks**:
- All nav links work (no 404s)
- Pages have content
- No dead ends (can always navigate away)

### search

**Purpose**: Verify search works

**Steps**:
1. Find search input (header, sidebar, etc.)
2. Enter a generic search term ("test", "product", etc.)
3. Submit search
4. Verify results page loads
5. Click first result
6. Verify result page loads

**Checks**:
- Search is discoverable
- Results are returned
- Results are clickable

## Preset File Format

```json
{
  "name": "preset-name",
  "description": "What this preset tests",
  "defaultPersona": "persona-id",
  "tasks": [
    "First task description",
    "Second task description",
    "Third task description"
  ],
  "successCriteria": [
    "What indicates success",
    "Another success indicator"
  ],
  "timeout": 120,
  "testData": {
    "email": "test@example.com",
    "password": "testpassword123"
  }
}
```

## Execution Logic

### Task Execution

For each task in `tasks` array:

```
1. Announce task (unless --quiet)
2. Execute using persona's timing
3. Capture evidence:
   - Screenshot on success
   - Screenshot + error details on failure
4. Record status: PASS / FAIL / SKIP
5. If FAIL and not --continue-on-error: stop
```

### Status Determination

**PASS**: All success criteria met
**FAIL**: Any task fails or criteria unmet
**SKIP**: Step skipped due to earlier failure

### Timing

- Use persona's timing for realism
- In `--quiet` mode: minimal delays
- Timeout per step: 30 seconds default
- Total timeout: from preset's `timeout` field

## Report Format

### Standard Report

```markdown
# Smoke Test Report: [preset]

## Configuration
- **URL**: [url]
- **Preset**: [preset]
- **Persona**: [name] ([id])
- **Date**: [timestamp]

## Results

| Step | Status | Duration | Notes |
|------|--------|----------|-------|
| [Task 1] | PASS | 2.3s | - |
| [Task 2] | PASS | 1.8s | - |
| [Task 3] | FAIL | 5.0s | Element not found |

## Summary
- **Overall**: FAIL
- **Steps Passed**: 2/3
- **Duration**: 9.1s

## Failure Details

### Step 3: [Task 3]
- **Error**: Element not found
- **Expected**: Submit button visible
- **Actual**: Page showed error message

![Failure Screenshot](screenshots/smoke-fail-step3.png)

## Recommendations
1. Check if [specific element] exists on the page
2. Verify [specific functionality]
```

### Quiet Mode Report

```
Smoke Test: login @ https://example.com
Persona: busy-executive

[1/5] Find login.......... PASS (1.2s)
[2/5] Navigate............ PASS (0.8s)
[3/5] Enter credentials... PASS (2.1s)
[4/5] Submit form......... FAIL (5.0s)
      Error: Form validation failed

Result: FAIL (3/5 steps)
Duration: 9.1s
```

## Custom Presets

Users can create custom presets in `.claude/smoke-presets/`:

```
.claude/
└── smoke-presets/
    ├── onboarding.json
    ├── checkout-guest.json
    └── admin-login.json
```

### Loading Order

1. Check `.claude/smoke-presets/{preset}.json` (project custom)
2. Check `skills/smoke-testing/presets/{preset}.json` (built-in)
3. Error if not found

## Integration with Other Commands

### After Smoke Test

```bash
# If smoke test passes, run detailed testing
/smoke-test --url https://example.com --preset checkout --quiet
# If passed:
/user-test --url https://example.com --persona comparison-shopper --tasks "complete purchase" --stripe
```

### CI/CD Pipeline

```bash
# Run all smoke tests
/smoke-test --url $DEPLOY_URL --preset login --quiet
/smoke-test --url $DEPLOY_URL --preset navigation --quiet
/smoke-test --url $DEPLOY_URL --preset search --quiet

# If all pass, run full suite
/user-test --url $DEPLOY_URL --personas "genz-digital-native,boomer-tech-averse" --quiet
```

## Best Practices

1. **Run smoke tests first**: Before detailed testing
2. **Use appropriate presets**: Match preset to site type
3. **Custom presets for critical flows**: Define your must-work paths
4. **CI/CD integration**: Use `--quiet` for automation
5. **Don't over-test**: Smoke tests should be fast (<2 min each)
