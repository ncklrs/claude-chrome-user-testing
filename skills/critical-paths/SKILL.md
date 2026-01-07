# Critical Paths Skill

Test must-work user journeys defined in project configuration. Critical paths are essential flows that must always function correctly.

## What is a Critical Path?

A critical path is a user journey that:
- Must work for the site/app to be usable
- Represents core business functionality
- Would cause significant harm if broken
- Should be tested before every deployment

## Examples of Critical Paths

| Site Type | Critical Paths |
|-----------|----------------|
| E-commerce | Purchase flow, Account creation, Cart |
| SaaS | Signup, Login, Core feature usage |
| Content | Navigation, Search, Article viewing |
| Banking | Login, Transfer, Balance check |

## Path Definition Schema

### Full Schema

```json
{
  "paths": [
    {
      "name": "string (required)",
      "description": "string (required)",
      "persona": "persona-id (required)",
      "priority": "critical|high|medium|low (optional, default: high)",
      "timeout": "number in seconds (optional, default: 120)",
      "steps": [
        {
          "action": "navigate|task|verify|wait|input",
          "...action-specific fields"
        }
      ],
      "onFailure": "continue|stop (optional, default: continue)"
    }
  ]
}
```

### Step Actions

#### navigate
Navigate to a URL path.

```json
{
  "action": "navigate",
  "to": "/path",
  "waitFor": "load|networkidle (optional)"
}
```

#### task
Perform a task using the persona's behavior.

```json
{
  "action": "task",
  "do": "description of task to perform",
  "timeout": 30
}
```

#### verify
Verify something exists or is true.

```json
// Verify element exists
{ "action": "verify", "element": "description of element" }

// Verify text appears
{ "action": "verify", "text": "exact text to find" }

// Verify URL matches
{ "action": "verify", "url": "/expected-path" }

// Verify URL contains
{ "action": "verify", "urlContains": "confirmation" }
```

#### wait
Wait for a condition.

```json
// Wait for seconds
{ "action": "wait", "seconds": 2 }

// Wait for element
{ "action": "wait", "for": "element description" }

// Wait for navigation
{ "action": "wait", "for": "page load" }
```

#### input
Enter specific data into a field.

```json
{
  "action": "input",
  "field": "field description or selector",
  "value": "value to enter"
}
```

## Execution Logic

### Path Execution

```
function executePath(path, baseUrl):
  results = { steps: [], status: 'pending' }

  loadPersona(path.persona)
  startTime = now()

  for step in path.steps:
    stepResult = executeStep(step, baseUrl)
    results.steps.append(stepResult)

    if stepResult.status == 'fail':
      results.status = 'fail'
      results.failedAt = step
      captureScreenshot()
      if path.onFailure == 'stop':
        break
      else:
        return results

  results.status = 'pass'
  results.duration = now() - startTime
  return results
```

### Step Execution

```
function executeStep(step, baseUrl):
  switch step.action:
    case 'navigate':
      navigate(baseUrl + step.to)
      return { status: 'pass' }

    case 'task':
      success = performTaskAsPersona(step.do)
      return { status: success ? 'pass' : 'fail' }

    case 'verify':
      found = verifyCondition(step)
      return { status: found ? 'pass' : 'fail' }

    case 'wait':
      waitForCondition(step)
      return { status: 'pass' }

    case 'input':
      fillField(step.field, step.value)
      return { status: 'pass' }
```

## Priority Handling

### Execution Order

1. Critical paths first
2. High priority second
3. Medium priority third
4. Low priority last

### Failure Impact

| Priority | On Failure | Report Level |
|----------|------------|--------------|
| critical | Major alert | CRITICAL |
| high | Alert | ERROR |
| medium | Warning | WARNING |
| low | Note | INFO |

## Report Generation

### Data Structure

```javascript
const pathReport = {
  summary: {
    total: 3,
    passed: 2,
    failed: 1,
    skipped: 0
  },
  results: [
    {
      name: 'purchase-flow',
      status: 'pass',
      duration: 45,
      steps: [
        { action: 'navigate', status: 'pass', duration: 2 },
        { action: 'task', status: 'pass', duration: 15 },
        // ...
      ]
    },
    {
      name: 'password-reset',
      status: 'fail',
      duration: 15,
      failedAt: { step: 4, action: 'task', error: 'Button not clickable' },
      screenshot: 'path-password-reset-fail.png'
    }
  ],
  actionRequired: [
    {
      priority: 'high',
      path: 'password-reset',
      issue: 'Form submission broken',
      recommendation: 'Check submit button handler'
    }
  ]
};
```

### Report Sections

1. **Summary**: Pass/fail counts
2. **Results**: Each path's outcome
3. **Failures**: Detailed failure information
4. **Action Required**: Prioritized fix list
5. **Screenshots**: Evidence of failures

## Integration with CI/CD

### Fail-Fast Mode

When `--fail-fast` is enabled:
- Stop testing after first failure
- Report what was tested so far
- Mark remaining paths as skipped
- Useful for quick validation

### Exit Status Interpretation

```
All pass + no critical failures = SUCCESS
Any critical failure = FAILURE
Only non-critical failures = WARNING (could be configurable)
```

## Template Path Definitions

### E-commerce

```json
{
  "paths": [
    {
      "name": "guest-purchase",
      "description": "Guest checkout flow",
      "persona": "impulse-buyer",
      "priority": "critical",
      "steps": [
        { "action": "navigate", "to": "/" },
        { "action": "task", "do": "find a product" },
        { "action": "task", "do": "add to cart" },
        { "action": "task", "do": "go to cart" },
        { "action": "task", "do": "proceed to checkout" },
        { "action": "task", "do": "fill shipping info" },
        { "action": "verify", "element": "payment form" }
      ]
    },
    {
      "name": "product-search",
      "description": "Search for products",
      "persona": "comparison-shopper",
      "priority": "high",
      "steps": [
        { "action": "navigate", "to": "/" },
        { "action": "task", "do": "search for a product" },
        { "action": "verify", "element": "search results" },
        { "action": "task", "do": "click a result" },
        { "action": "verify", "element": "product details" }
      ]
    }
  ]
}
```

### SaaS Application

```json
{
  "paths": [
    {
      "name": "signup-to-dashboard",
      "description": "New user onboarding",
      "persona": "genz-digital-native",
      "priority": "critical",
      "steps": [
        { "action": "navigate", "to": "/signup" },
        { "action": "task", "do": "complete registration" },
        { "action": "verify", "urlContains": "dashboard" },
        { "action": "verify", "element": "welcome message" }
      ]
    },
    {
      "name": "login-flow",
      "description": "Existing user login",
      "persona": "busy-executive",
      "priority": "critical",
      "steps": [
        { "action": "navigate", "to": "/login" },
        { "action": "input", "field": "email", "value": "test@example.com" },
        { "action": "input", "field": "password", "value": "testpass123" },
        { "action": "task", "do": "submit login" },
        { "action": "verify", "element": "dashboard or main app" }
      ]
    }
  ]
}
```

## Best Practices

1. **Start small**: Define 2-3 critical paths first
2. **Be specific**: Clear step descriptions help the agent
3. **Match personas**: Use personas that would actually do this journey
4. **Update regularly**: Keep paths in sync with features
5. **Test the tests**: Run paths manually to verify they make sense
6. **Prioritize correctly**: Reserve "critical" for truly essential flows
