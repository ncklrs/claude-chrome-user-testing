# Features Guide

Detailed documentation for all features.

## Table of Contents

- [Multi-Persona Testing](#multi-persona-testing)
- [Quiet Mode](#quiet-mode)
- [Session Recording](#session-recording)
- [A/B Testing](#ab-testing)
- [Smoke Test Presets](#smoke-test-presets)
- [Critical Path Testing](#critical-path-testing)
- [Link Checker](#link-checker)
- [Mobile Viewport Testing](#mobile-viewport-testing)
- [Network Throttling](#network-throttling)
- [JSON Report Export](#json-report-export)
- [GitHub PR Comments](#github-pr-comments)
- [WCAG Audit](#wcag-audit)
- [Cookie Consent Audit](#cookie-consent-audit)
- [Form Fuzzing](#form-fuzzing)
- [Stripe Checkout Testing](#stripe-checkout-testing)
- [Screenshot Annotations](#screenshot-annotations)
- [International Testing](#international-testing)

---

## Multi-Persona Testing

Test the same flow with multiple personas simultaneously.

```bash
/user-test --url https://example.com --personas "boomer-tech-averse,genz-digital-native,screen-reader-user" --tasks "sign up"
```

**Comparison report includes:**
- Results matrix across all personas
- Universal issues (found by all)
- Common issues (found by 2+)
- Persona-specific issues
- Recommendations prioritized by frequency

---

## Quiet Mode

Disable narration for CI/CD integration.

```bash
/user-test --url https://example.com --persona genz-digital-native --quiet
```

**Output:**
- No first-person commentary
- Annotated screenshots at key moments
- Summary report only
- Faster execution

---

## Session Recording

Record sessions as Playwright Traces for replay and debugging.

```bash
/user-test --url https://example.com --persona genz-digital-native --record
```

**Trace files include:**
- Screenshots at each action
- DOM snapshots at each step
- Network requests and responses
- Console logs
- Full timeline with timing

**View traces:** Drag and drop `.zip` file at [trace.playwright.dev](https://trace.playwright.dev)

**Custom path:**
```bash
/user-test --url https://example.com --persona boomer-tech-averse --record --record-path ./traces/
```

---

## A/B Testing

Compare two URL variants to determine which performs better.

```bash
/ab-test --url-a https://example.com --url-b https://staging.example.com --persona genz-digital-native --tasks "sign up"
```

**Output:**
```markdown
## Results Summary

| Metric | Variant A | Variant B | Winner |
|--------|-----------|-----------|--------|
| Tasks Completed | 2/3 | 3/3 | B |
| Critical Issues | 1 | 0 | B |
| Major Issues | 2 | 1 | B |

## Overall Winner: Variant B
```

**Multi-persona consensus:**
```bash
/ab-test --url-a https://prod.example.com --url-b https://canary.example.com --personas "genz-digital-native,boomer-tech-averse" --tasks "checkout"
```

---

## Smoke Test Presets

Quick validation with pre-built configurations.

| Preset | Default Persona | Tests |
|--------|-----------------|-------|
| `login` | busy-executive | Find login, enter credentials, submit, verify |
| `signup` | genz-digital-native | Find signup, complete form, submit, verify |
| `checkout` | impulse-buyer | Find product, add to cart, start checkout |
| `navigation` | boomer-tech-averse | Click main nav items, verify pages |
| `search` | power-user | Find search, enter term, filter, click result |

```bash
/smoke-test --url https://example.com --preset login
```

---

## Critical Path Testing

Test must-work journeys from project config.

**Config** (`.claude/critical-paths.json`):
```json
{
  "paths": [
    {
      "name": "purchase-flow",
      "persona": "impulse-buyer",
      "priority": "critical",
      "steps": [
        { "action": "navigate", "to": "/" },
        { "action": "task", "do": "find and select a product" },
        { "action": "verify", "element": "checkout form" }
      ]
    }
  ]
}
```

**Step types:** `navigate`, `task`, `verify`, `wait`, `input`

```bash
/critical-path --url https://example.com --fail-fast
```

---

## Link Checker

Find broken links during testing or standalone.

```bash
# Standalone
/check-links --url https://example.com --depth 2

# During testing
/user-test --url https://example.com --persona boomer-tech-averse --check-links
```

**Output:**
- Total links found vs working vs broken
- Broken link details (URL, page found on, status)
- Redirect warnings

---

## Mobile Viewport Testing

Test responsive designs with device viewports.

| Preset | Size | Device |
|--------|------|--------|
| `mobile` | 375x667 | iPhone 12/13 |
| `tablet` | 768x1024 | iPad |
| `desktop` | 1280x720 | Standard |

```bash
/user-test --url https://example.com --persona genz-digital-native --viewport mobile
```

---

## Network Throttling

Simulate slow or offline connections.

| Preset | Download | Latency |
|--------|----------|---------|
| `slow-3g` | 400 Kbps | 400ms |
| `fast-3g` | 1.6 Mbps | 100ms |
| `offline` | 0 | N/A |

```bash
/user-test --url https://example.com --persona bad-connection-user --network slow-3g
```

**Offline/PWA testing:**
```bash
/user-test --url https://pwa.example.com --persona genz-digital-native --network offline
```

---

## JSON Report Export

Machine-readable output for CI/CD.

```bash
/user-test --url https://example.com --persona genz-digital-native --output json --quiet
```

**Schema:**
```json
{
  "session": { "url": "...", "persona": {...}, "viewport": "...", "duration": 85 },
  "tasks": [{ "name": "...", "status": "completed", "duration": 45 }],
  "issues": { "critical": [], "major": [...], "minor": [] },
  "summary": { "tasksCompleted": "1/1", "overallStatus": "pass" }
}
```

---

## GitHub PR Comments

Auto-post results to pull requests.

```bash
/user-test --url https://staging.example.com --persona genz-digital-native --quiet --pr 123
```

**Requirements:**
- `gh` CLI authenticated
- In GitHub Actions: `GITHUB_TOKEN` available
- `pull-requests: write` permission

**GitHub Actions example:**
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

---

## WCAG Audit

Automated accessibility audit against WCAG 2.1.

```bash
/wcag-audit --url https://example.com --annotate
```

**Checks ~23 criteria including:**
- Alt text on images
- Color contrast (4.5:1 minimum)
- Keyboard accessibility
- Form labels and errors
- Focus visibility
- Semantic HTML

**Output:**
```
COMPLIANCE SCORE: 78% (C)
Level A:  85% (28/33 passed)
Level AA: 68% (15/22 passed)
```

---

## Cookie Consent Audit

GDPR and CCPA compliance checking.

```bash
/cookie-audit --url https://example.com
```

**GDPR Criteria (6):**
- Banner before cookies (Critical)
- No pre-checked boxes (Critical)
- Reject option prominence (Major)
- Granular consent (Major)
- Withdrawal mechanism (Major)
- Cookie policy link (Minor)

**CCPA Criteria (4):**
- "Do Not Sell" link (Critical)
- Opt-out mechanism (Critical)
- Privacy policy (Major)
- Rights disclosure (Minor)

**Output:**
```
COMPLIANCE SCORE: 75% (C)
GDPR:  67% (4/6 passed)
CCPA:  75% (3/4 passed)
```

---

## Form Fuzzing

Test forms for security vulnerabilities.

```bash
/user-test --url https://example.com/signup --persona developer-critic --fuzz
```

**Categories:**

| Category | Payloads | Tests |
|----------|----------|-------|
| `xss` | 15 | Cross-site scripting |
| `sqli` | 12 | SQL injection |
| `unicode` | 10 | Character encoding |
| `length` | 4 | Input length limits |
| `all` | 41 | All categories |

**XSS only:**
```bash
/user-test --url https://example.com/login --fuzz --fuzz-category xss
```

**Response classification:**
- Sanitized (Pass)
- Rejected (Pass)
- Executed (Critical)
- Error (Major)

---

## Stripe Checkout Testing

Test payment flows with official test cards.

```bash
/stripe-test --url https://shop.example.com/checkout --card success
```

**Or add to user test:**
```bash
/user-test --url https://shop.example.com --persona impulse-buyer --stripe --card decline
```

**Card scenarios:** `success`, `decline`, `insufficient`, `expired`, `cvc-fail`, `3ds-required`, `fraud-high`, `mastercard`, `amex`

**Report includes Payment Experience section:**
- Checkout type (hosted/embedded)
- Express checkout availability
- Payment friction points

---

## Screenshot Annotations

Visual markers on screenshots highlighting issues.

| Trigger | Type | Color |
|---------|------|-------|
| Confusion | Dashed box | Red |
| Error | Solid box | Red |
| Frustration | Highlight | Orange |
| Task Complete | Solid box | Green |

**Manual:**
```bash
/annotate --element "#submit-btn" --label "Hard to find" --color red
```

---

## International Testing

Test locale-specific requirements.

| Persona | Date | Currency | Special |
|---------|------|----------|---------|
| `german-business-user` | DD.MM.YYYY | EUR | GDPR, formal tone |
| `japanese-user` | YYYY/MM/DD | JPY | Family-name-first, IME |
| `arabic-rtl-user` | DD/MM/YYYY | SAR/AED | RTL layout |
| `brazilian-user` | DD/MM/YYYY | R$ | PIX/boleto payments |

```bash
/user-test --url https://example.de --persona german-business-user
/user-test --url https://example.sa --persona arabic-rtl-user
```

See [Personas](personas.md#international-personas-4) for full details.
