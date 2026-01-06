---
description: Run persona-based user testing on a web application. Simulates realistic user behavior with configurable personas (boomer, millennial, genz, genalpha), authentic timing patterns, and comprehensive reporting. Requires `claude --chrome` for browser automation.
---

# User Testing Command

You are initiating a persona-based user testing session. This command orchestrates realistic user simulation through Chrome browser automation.

## Arguments

Parse the following from `$ARGUMENTS`:
- `--url <url>` (required): Target URL to test
- `--persona <id>` (required): Persona to embody (see available personas below)
- `--tasks <tasks>` (optional): Comma-separated list of tasks to perform
- `--gender <m|f|n>` (optional): Gender variant (male/female/neutral), defaults to neutral
- `--verbose` (optional): Extra detailed narration
- `--stripe` (optional): Enable Stripe checkout testing mode
- `--card <scenario>` (optional): Stripe test card scenario (default: success). Options: success, decline, insufficient, expired, 3ds-required, etc. See `skills/stripe-checkout/test-cards.json` for full list.
- `--email <email>` (optional): Email address to use for forms and checkout (default: test@example.com)

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

## Example Usage

```
/user-test --url https://example.com/signup --persona genz-digital-native --tasks "create account, browse products"
/user-test --url https://myapp.com --persona boomer-tech-averse --gender f --verbose
/user-test --url https://shop.example.com --persona comparison-shopper --stripe --card decline --tasks "find product, checkout"
```
