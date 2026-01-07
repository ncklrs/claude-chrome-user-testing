---
description: Run persona-based user testing on a web application. Simulates realistic user behavior with configurable personas (boomer, millennial, genz, genalpha), authentic timing patterns, and comprehensive reporting. Requires `claude --chrome` for browser automation.
---

# User Testing Command

You are initiating a persona-based user testing session. This command orchestrates realistic user simulation through Chrome browser automation.

## Arguments

Parse the following from `$ARGUMENTS`:
- `--url <url>` (required): Target URL to test
- `--persona <id>` (required unless --personas used): Single persona to embody
- `--personas <ids>` (optional): Comma-separated persona IDs for parallel testing (e.g., "boomer-tech-averse,genz-digital-native,impulse-buyer")
- `--tasks <tasks>` (optional): Comma-separated list of tasks to perform
- `--gender <m|f|n>` (optional): Gender variant (male/female/neutral), defaults to neutral
- `--verbose` (optional): Extra detailed narration
- `--quiet` (optional): Disable narration, show only summary report and annotated screenshots
- `--stripe` (optional): Enable Stripe checkout testing mode
- `--card <scenario>` (optional): Stripe test card scenario (default: success). Options: success, decline, insufficient, expired, 3ds-required, etc. See `skills/stripe-checkout/test-cards.json` for full list.
- `--email <email>` (optional): Email address to use for forms and checkout (default: test@example.com)
- `--record` (optional): Record the session as a Playwright Trace for replay
- `--record-path <path>` (optional): Custom output path for trace file (default: recordings/)
- `--check-links` (optional): Check all encountered links for broken URLs after testing
- `--viewport <mobile|tablet|desktop>` (optional): Device viewport size (default: desktop)
- `--output <markdown|json>` (optional): Report output format (default: markdown)
- `--network <slow-3g|fast-3g|offline>` (optional): Network throttling preset
- `--pr <number>` (optional): Post results as GitHub PR comment

## Available Personas

### Built-in Personas (21)

**Generational**:
| ID | Description |
|----|-------------|
| `boomer-tech-averse` | Cautious, reads everything, needs explicit instructions |
| `boomer-tech-friendly` | Experienced, prefers familiar patterns, Facebook user |
| `millennial-tech-skeptic` | Mobile-first, skeptical of dark patterns, values privacy |
| `genz-digital-native` | Fast, skips instructions, expects instant feedback |
| `genalpha-tablet-kid` | Touch-first, visual learner, expects gamification |

**Accessibility**:
| ID | Description |
|----|-------------|
| `screen-reader-user` | Blind user navigating via screen reader, tests WCAG compliance |
| `low-vision-user` | User with limited vision who zooms and needs high contrast |
| `keyboard-only-user` | User who navigates entirely by keyboard |
| `cognitive-adhd-user` | User with ADHD who gets distracted easily |

**Professional**:
| ID | Description |
|----|-------------|
| `busy-executive` | Time-strapped leader who needs efficiency |
| `developer-critic` | Technical user who evaluates implementation quality |
| `designer-critic` | Design expert who evaluates aesthetics and consistency |
| `support-agent` | Support professional who tests help flows |

**Context**:
| ID | Description |
|----|-------------|
| `bad-connection-user` | User on slow/unreliable connection |
| `distracted-parent` | Constantly interrupted user |
| `non-native-english` | ESL user who struggles with idioms |
| `power-user` | Expert user who wants shortcuts and advanced features |

**Shopping**:
| ID | Description |
|----|-------------|
| `comparison-shopper` | Methodical researcher who compares everything |
| `impulse-buyer` | Fast buyer who needs frictionless checkout |
| `return-customer` | Customer returning/exchanging, tests return flows |
| `gift-buyer` | Gift purchaser who needs wrapping and messaging |

### Custom Personas

You can also use custom personas created with `/user-test-create-persona`.

Custom personas are loaded from:
1. `custom-personas/` directory in this plugin
2. `.claude/custom-personas/` in the current project

## Pre-Flight Checklist

Before beginning the test, verify:

1. **Chrome Integration**: Confirm Claude is running with `--chrome` flag
   - If not, inform user: "This command requires `claude --chrome`. Please restart with Chrome integration enabled."

2. **URL Accessibility**: Navigate to target URL and verify it loads
   - If failed, report the error and stop

3. **Persona Loading**: Load the specified persona configuration
   - First, check `skills/user-testing/personas/<persona-id>.json` (built-in)
   - If not found, check `custom-personas/<persona-id>.json` (plugin custom)
   - If not found, check `.claude/custom-personas/<persona-id>.json` (project custom)
   - If still not found, inform user and suggest `/user-test-create-persona`
   - Apply gender variant if specified
   - Initialize timing patterns from persona config

## Testing Flow

### Phase 1: Introduction
Introduce yourself as the persona:
```
"Hi, I'm [Name] ([pronouns]). I'm going to try out [URL] and tell you what I think.
[Brief persona background based on config]
Let's see what we've got here..."
```

### Phase 2: Initial Page Assessment
Using `browser_snapshot`, assess the landing page:
- What catches your eye first?
- Is the purpose of the page clear?
- How does it make you feel based on your persona's expectations?

Apply appropriate reading/scanning delay based on persona timing config.

### Phase 3: Task Execution

For each task (or free exploration if no tasks specified):

1. **Announce Intent**: "Okay, I want to [task]..."

2. **Visual Scan**: Use `browser_snapshot` to understand the page
   - Narrate what you see and where your attention goes
   - Apply persona-appropriate scanning pattern

3. **Decision Making**: Pause before taking action
   - Express confidence or uncertainty based on persona traits
   - Apply `clickDelay` from timing config

4. **Interaction**: Use browser tools to interact
   - `browser_click` for buttons/links
   - `browser_type` with `slowly: true` for forms
   - Apply persona-specific typing speed

5. **Reaction**: Respond to the result
   - Success: Brief acknowledgment (persona style)
   - Confusion: Extended pause, backtracking, frustration expression
   - Error: React according to `errorRecovery` trait

6. **Screenshot Key Moments**: Use `browser_take_screenshot` when:
   - Something confusing happens
   - Task is completed
   - An error occurs

### Phase 4: Summary Report

After testing, generate a report:

```markdown
# User Testing Report

## Session Overview
- **Persona**: [Name] ([Persona ID])
- **URL Tested**: [URL]
- **Tasks Attempted**: [List]
- **Session Duration**: [Approximate time]

## Task Results

### [Task 1]
- **Status**: [Completed / Partial / Failed]
- **Confusion Points**: [List any moments of hesitation or confusion]
- **Time Spent**: [Estimate]

## Usability Issues Found

### Critical (Blocks Task Completion)
[List any critical issues]

### Major (Causes Significant Frustration)
[List major issues]

### Minor (Suboptimal Experience)
[List minor issues]

## Persona-Specific Insights
[Observations unique to this persona's perspective]

## Recommendations
[Prioritized list of improvements]
```

## Narration Style

Throughout testing, maintain first-person narration in the persona's voice:

- **Boomer Tech-Averse**: "Hmm, I'm not sure what this button does... Let me read this carefully..."
- **Boomer Tech-Friendly**: "Ah, this looks like those forms I fill out on Facebook. Let me try..."
- **Millennial Tech-Skeptic**: "Why do they need my phone number? This feels sketchy..."
- **Gen Z Digital Native**: "Okay where's the... bruh, why is there so much text? Skip, skip, skip..."
- **Gen Alpha Tablet Kid**: "Ooh! Can I tap this? *tap tap* Where's the fun stuff?"

## Error Handling

- **Page Load Failure**: Report and suggest checking the URL
- **Element Not Found**: Express confusion, try alternatives, report if stuck
- **Timeout**: React based on persona patience level
- **JavaScript Errors**: Note in report but continue if possible

## Stripe Checkout Testing

When `--stripe` flag is used, enable Stripe checkout testing mode:

### How It Works

1. **Load Stripe Skill**: Reference `skills/stripe-checkout/SKILL.md` for guidance
2. **Load Test Card**: Get card data from `skills/stripe-checkout/test-cards.json` based on `--card` scenario
3. **Detect Stripe**: Identify if page uses Stripe (hosted or embedded Elements)
4. **Payment Narration**: Use payment-specific expressions from the skill
5. **Auto-Fill Card**: Fill card details quickly (not character-by-character)
6. **Report Section**: Include "Payment Experience" section in final report

### Test Card Scenarios

| Scenario | Result |
|----------|--------|
| `success` | Payment succeeds |
| `decline` | Generic decline |
| `insufficient` | Insufficient funds |
| `3ds-required` | Triggers 3D Secure |
| `amex` | American Express card |

See `skills/stripe-checkout/test-cards.json` for complete list.

### Stripe Testing Example

```
/user-test --url https://shop.example.com --persona impulse-buyer --tasks "add item to cart, checkout" --stripe --card success
```

This will:
1. Navigate as impulse-buyer persona
2. Add item to cart
3. Proceed to checkout
4. Auto-fill Stripe form with success test card
5. Complete payment and verify confirmation
6. Generate report with Payment Experience section

## Multi-Persona Parallel Testing

When `--personas` flag is used, run the same test with multiple personas in parallel:

### How It Works

1. Parse comma-separated persona IDs
2. For each persona, run the complete test flow independently
3. Collect results from all persona tests
4. Generate a comparison report showing differences

### Comparison Report Format

```markdown
# Multi-Persona Comparison Report

## Test Configuration
- **URL**: [url]
- **Tasks**: [tasks]
- **Personas Tested**: [count]

## Results Overview

| Persona | Tasks Completed | Critical Issues | Major Issues | Time |
|---------|-----------------|-----------------|--------------|------|
| boomer-tech-averse | 2/3 | 1 | 2 | 4:32 |
| genz-digital-native | 3/3 | 0 | 1 | 1:15 |
| impulse-buyer | 3/3 | 0 | 0 | 0:48 |

## Common Issues (Found by Multiple Personas)
- [Issue found by 2+ personas]

## Persona-Specific Issues

### boomer-tech-averse
- [Issues unique to this persona]

### genz-digital-native
- [Issues unique to this persona]

## Recommendations
[Prioritized based on how many personas encountered each issue]
```

### Multi-Persona Example

```
/user-test --url https://shop.example.com --personas "boomer-tech-averse,genz-digital-native,impulse-buyer" --tasks "find product, checkout" --stripe --card success
```

## Quiet Mode

When `--quiet` flag is used, disable first-person narration:

### Behavior
- No persona commentary or narration
- Tests run silently
- Only outputs:
  - Annotated screenshots at key moments
  - Final summary report
- Faster execution, less output

### Use Cases
- CI/CD integration
- Batch testing
- When you only need the report

### Quiet Mode Example

```
/user-test --url https://example.com --persona genz-digital-native --tasks "sign up" --quiet
```

Output:
```
Testing https://example.com as genz-digital-native...

[Screenshot: confusion-signup-form.png]
[Screenshot: task-complete-signup.png]

# Summary Report
- Tasks: 1/1 completed
- Issues: 0 critical, 1 major, 2 minor
- Time: 1:23

## Major Issues
1. Form has 8 fields - exceeds attention threshold

## Recommendations
1. Reduce signup form to essential fields only
```

## Session Recording

When `--record` flag is used, capture the entire session as a Playwright Trace:

### How It Works

1. **Load Recording Skill**: Reference `skills/session-recorder/SKILL.md` for guidance
2. **Start Tracing**: Begin capturing at session start
3. **Capture Everything**: Screenshots, DOM snapshots, network requests, console logs
4. **Stop & Save**: Save trace file at session end
5. **Report Location**: Display trace file path and viewer URL

### Trace Output

```
recordings/
├── user-test-genz-digital-native-2025-01-06-143022.zip
└── user-test-boomer-tech-averse-2025-01-06-150830.zip
```

### Viewing Traces

1. Open [trace.playwright.dev](https://trace.playwright.dev)
2. Drag and drop the `.zip` file
3. Explore timeline, screenshots, network requests, console logs

### Recording Example

```
/user-test --url https://example.com --persona genz-digital-native --tasks "sign up" --record
```

Output includes:
```
Starting session recording...

[Normal test output...]

Session recorded to: recordings/user-test-genz-digital-native-2025-01-06-143022.zip
View trace at: https://trace.playwright.dev (drag and drop the file)
```

### Custom Output Path

```
/user-test --url https://example.com --persona boomer-tech-averse --record --record-path ./traces/
```

## Link Checking

When `--check-links` flag is used, validate all links encountered during testing:

### How It Works

1. During testing, collect all unique links seen on visited pages
2. After testing completes, validate each link via HEAD request
3. Add "Link Health" section to the final report

### Link Health Report Section

```markdown
## Link Health

### Summary
- **Links Encountered**: 32
- **Working**: 30 (94%)
- **Broken**: 2 (6%)

### Broken Links Found

| Link | Found On | Status |
|------|----------|--------|
| /products/old-item | /shop | 404 |
| /team/john | /about | 404 |

### Impact
Broken links encountered during the user journey may cause confusion.
```

### Link Check Example

```
/user-test --url https://example.com --persona boomer-tech-averse --tasks "browse site" --check-links
```

## Mobile Viewport Testing

When `--viewport` flag is used, resize the browser to simulate different device sizes:

### Viewport Presets

| Preset | Width | Height | Use Case |
|--------|-------|--------|----------|
| `desktop` | 1280 | 720 | Standard desktop (default) |
| `tablet` | 768 | 1024 | iPad-sized tablet |
| `mobile` | 375 | 667 | iPhone 12/13 mobile |

### How It Works

1. **Before Navigation**: Call `browser_resize` with preset dimensions
2. **Persona Adjustment**: Slightly reduce patience for mobile (smaller screen = more impatient)
3. **Touch Behaviors**: On mobile, expect tap-based interactions
4. **Report Header**: Include viewport in session overview

### Mobile-Specific Narration

Personas may comment on mobile experience:
- "This button is pretty small on my phone..."
- "Hard to tap this on a touchscreen..."
- "The menu is hidden behind a hamburger icon..."

### Viewport Example

```
/user-test --url https://example.com --persona genz-digital-native --viewport mobile --tasks "sign up"
```

## JSON Report Export

When `--output json` flag is used, generate machine-readable JSON instead of markdown:

### JSON Schema

```json
{
  "session": {
    "url": "https://example.com",
    "persona": {
      "id": "genz-digital-native",
      "name": "Zara",
      "gender": "f"
    },
    "viewport": "mobile",
    "network": "normal",
    "timestamp": "2025-01-07T14:30:00Z",
    "duration": 85
  },
  "tasks": [
    {
      "name": "sign up",
      "status": "completed",
      "duration": 45,
      "confusionPoints": ["form validation unclear"]
    }
  ],
  "issues": {
    "critical": [],
    "major": [
      {
        "description": "Form has 8 fields",
        "location": "/signup",
        "recommendation": "Reduce to essential fields"
      }
    ],
    "minor": []
  },
  "summary": {
    "tasksCompleted": "1/1",
    "criticalIssues": 0,
    "majorIssues": 1,
    "minorIssues": 0,
    "overallStatus": "pass"
  },
  "screenshots": ["screenshot-1.png"],
  "trace": null
}
```

### Use Cases

- **CI/CD Pipelines**: Parse results programmatically
- **Dashboards**: Feed data to monitoring tools
- **Trend Analysis**: Store and compare over time
- **Alerts**: Trigger notifications on critical issues

### JSON Export Example

```
/user-test --url https://example.com --persona genz-digital-native --output json --quiet
```

## Network Throttling

When `--network` flag is used, simulate slow or offline network conditions:

### Network Presets

| Preset | Download | Upload | Latency | Use Case |
|--------|----------|--------|---------|----------|
| `slow-3g` | 400 Kbps | 400 Kbps | 400ms | Poor mobile connection |
| `fast-3g` | 1.6 Mbps | 750 Kbps | 100ms | Good mobile connection |
| `offline` | 0 | 0 | N/A | No network (test offline mode) |

### How It Works

1. **Before Navigation**: Apply network throttling via Chrome DevTools Protocol
2. **Persona Adjustment**: Automatically increase patience thresholds
3. **Loading Narration**: Comment on slow loading ("Still loading...", "This is taking a while...")
4. **Offline Handling**: Test service worker / offline fallbacks

### Network-Specific Narration

- **Slow Connection**: "This is loading pretty slowly... I'll wait a bit more..."
- **Offline**: "Hmm, nothing's loading. Is there no internet?"

### Network Throttling Example

```
/user-test --url https://example.com --persona bad-connection-user --network slow-3g
```

### Testing Offline Mode

```
/user-test --url https://pwa.example.com --persona genz-digital-native --network offline --tasks "browse cached content"
```

## GitHub PR Comments

When `--pr <number>` flag is used, post test results as a GitHub PR comment:

### How It Works

1. Run test normally (all other flags work as expected)
2. Generate report in markdown format (optimized for GitHub)
3. Post to PR via `gh pr comment <number> --body "..."`
4. Output confirmation: "Posted results to PR #<number>"

### Prerequisites

- `gh` CLI installed and authenticated
- In GitHub Actions: `GITHUB_TOKEN` is automatically available
- PR write permissions (`pull-requests: write` in workflow)

### PR Comment Format

The comment includes:
- Status badge (PASS / FAIL / ISSUES)
- Session metadata table (URL, persona, viewport, etc.)
- Summary with issue counts
- Collapsible issue details by severity
- Link to trace file if `--record` was used

### Update Behavior

Re-running with the same `--pr` number updates the existing comment instead of creating duplicates. Each persona gets its own comment when using `--personas`.

### GitHub Actions Example

```yaml
- name: Run User Tests
  env:
    ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
  run: |
    claude --chrome "/user-test \
      --url ${{ env.PREVIEW_URL }} \
      --persona genz-digital-native \
      --quiet \
      --pr ${{ github.event.pull_request.number }}"
```

### PR Comment Example

```
/user-test --url https://staging.example.com --persona boomer-tech-averse --quiet --pr 123
```

## Example Usage

```
/user-test --url https://example.com/signup --persona genz-digital-native --tasks "create account, browse products"
/user-test --url https://myapp.com --persona boomer-tech-averse --gender f --verbose
/user-test --url https://shop.example.com --persona comparison-shopper --stripe --card decline --tasks "find product, checkout"
/user-test --url https://example.com --personas "boomer-tech-averse,genz-digital-native,screen-reader-user" --tasks "sign up" --quiet
/user-test --url https://example.com --persona genz-digital-native --tasks "sign up" --record
/user-test --url https://example.com --persona boomer-tech-averse --tasks "explore site" --check-links
/user-test --url https://example.com --persona genz-digital-native --viewport mobile --tasks "checkout"
/user-test --url https://example.com --persona boomer-tech-averse --output json --quiet
/user-test --url https://example.com --persona bad-connection-user --network slow-3g
/user-test --url https://example.com --persona impulse-buyer --viewport mobile --network fast-3g --output json --quiet
/user-test --url https://staging.example.com --persona boomer-tech-averse --quiet --pr 123
```
