# Command Reference

Complete reference for all available commands.

## /user-test

Main user testing command with all options.

```
/user-test --url <url> --persona <id> [options]

Required:
  --url <url>           Target URL to test
  --persona <id>        Single persona (or use --personas)

Optional:
  --personas <ids>      Comma-separated IDs for parallel testing
  --tasks <tasks>       Comma-separated tasks to perform
  --gender <m|f|n>      Gender variant (male/female/neutral)
  --email <email>       Email for forms (default: test@example.com)
  --verbose             Extra detailed narration
  --quiet               Summary only, no narration (CI/CD friendly)
  --stripe              Enable Stripe checkout testing
  --card <scenario>     Stripe test card (default: success)
  --record              Record session as Playwright Trace
  --record-path <path>  Custom trace output path (default: recordings/)
  --check-links         Check all links encountered during testing
  --viewport <size>     Device viewport: mobile | tablet | desktop
  --output <format>     Report format: markdown | json
  --network <preset>    Network throttling: slow-3g | fast-3g | offline
  --pr <number>         Post results as GitHub PR comment
  --fuzz                Enable form fuzzing for security testing
  --fuzz-category <cat> Fuzz category: xss | sqli | unicode | length | all
```

**Examples:**
```bash
# Basic test
/user-test --url https://example.com --persona genz-digital-native

# With tasks
/user-test --url https://shop.example.com --persona boomer-tech-averse --tasks "find product, checkout"

# Multi-persona parallel
/user-test --url https://example.com --personas "boomer-tech-averse,genz-digital-native" --quiet

# Mobile + slow network
/user-test --url https://example.com --persona impulse-buyer --viewport mobile --network slow-3g

# Security fuzzing
/user-test --url https://example.com/signup --persona developer-critic --fuzz --fuzz-category xss

# CI/CD with PR comment
/user-test --url https://staging.example.com --persona genz-digital-native --quiet --pr 123
```

---

## /stripe-test

Dedicated Stripe checkout testing.

```
/stripe-test --url <url> [options]

Required:
  --url <url>           Checkout page URL

Optional:
  --persona <id>        Persona to use (default: impulse-buyer)
  --personas <ids>      Comma-separated IDs for parallel testing
  --gender <m|f|n>      Gender variant
  --card <scenario>     Test card scenario (default: success)
  --email <email>       Email for checkout
  --tasks <tasks>       Pre-checkout tasks
  --quiet               Summary only, no narration
  --record              Record session as Playwright Trace
```

**Test Card Scenarios:**

| Scenario | Card | Description |
|----------|------|-------------|
| `success` | 4242...4242 | Payment succeeds |
| `decline` | 4000...0002 | Generic decline |
| `insufficient` | 4000...9995 | Insufficient funds |
| `expired` | 4000...0069 | Expired card |
| `cvc-fail` | 4000...0127 | CVC check fails |
| `3ds-required` | 4000...3155 | 3D Secure required |
| `3ds-optional` | 4000...3220 | 3D Secure optional |
| `fraud-high` | 4100...0019 | High fraud risk |
| `mastercard` | 5555...4444 | Mastercard |
| `amex` | 3782...0005 | American Express |

---

## /ab-test

Compare two URL variants with the same persona.

```
/ab-test --url-a <url> --url-b <url> --persona <id> [options]

Required:
  --url-a <url>         Variant A URL (control)
  --url-b <url>         Variant B URL (test)
  --persona <id>        Persona to test with (or use --personas)

Optional:
  --personas <ids>      Test multiple personas on both variants
  --tasks <tasks>       Comma-separated tasks to perform
  --gender <m|f|n>      Gender variant
  --quiet               Summary only, no narration
  --record              Record sessions as Playwright Traces
```

**Example:**
```bash
/ab-test --url-a https://example.com --url-b https://staging.example.com --persona genz-digital-native --tasks "sign up"
```

---

## /smoke-test

Quick validation of common flows with presets.

```
/smoke-test --url <url> --preset <preset> [options]

Required:
  --url <url>           Target URL to test
  --preset <preset>     Test preset to run

Available Presets:
  login                 Validate authentication flow
  signup                Test registration process
  checkout              Verify purchase flow
  navigation            Test main navigation structure
  search                Validate search functionality

Optional:
  --persona <id>        Override default persona
  --email <email>       Email for forms
  --quiet               Summary only, no narration
  --record              Record session as Playwright Trace
```

**Examples:**
```bash
/smoke-test --url https://example.com --preset login
/smoke-test --url https://example.com --preset checkout --persona boomer-tech-averse
```

---

## /critical-path

Test must-work user journeys from project config.

```
/critical-path --url <url> [options]

Required:
  --url <url>           Base URL for testing

Optional:
  --path <name>         Run specific path only (default: all)
  --persona <id>        Override default persona for all paths
  --quiet               Summary only output
  --fail-fast           Stop on first failure
  --record              Record sessions as Playwright Traces
```

**Configuration** (`.claude/critical-paths.json`):
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
        { "action": "task", "do": "add to cart" },
        { "action": "verify", "element": "checkout form" }
      ]
    }
  ]
}
```

---

## /check-links

Find broken links on a page.

```
/check-links --url <url> [options]

Required:
  --url <url>           URL to check

Optional:
  --depth <n>           How deep to crawl (default: 1, max: 3)
  --internal-only       Only check internal links
  --exclude <pattern>   Exclude URLs matching pattern
  --quiet               Summary only output
```

---

## /wcag-audit

WCAG 2.1 accessibility audit.

```
/wcag-audit --url <url> [options]

Required:
  --url <url>           URL to audit

Optional:
  --level <a|aa>        Conformance level (default: aa)
  --format <format>     Output: summary | detailed | json
  --save <filename>     Save report to file
  --annotate            Screenshot violations
```

**Output:**
```
COMPLIANCE SCORE: 78% (C)
────────────────────────
Level A:  85% (28/33 passed)
Level AA: 68% (15/22 passed)
```

---

## /cookie-audit

GDPR and CCPA cookie consent compliance audit.

```
/cookie-audit --url <url> [options]

Required:
  --url <url>           URL to audit

Optional:
  --regulation <type>   Target: gdpr | ccpa | both (default: both)
  --format <format>     Output: summary | detailed | json
  --save <filename>     Save report to file
  --annotate            Screenshot violations
```

**GDPR Criteria:**
- GDPR-1: Banner before cookies (Critical)
- GDPR-2: No pre-checked boxes (Critical)
- GDPR-3: Reject option prominence (Major)
- GDPR-4: Granular consent (Major)
- GDPR-5: Withdrawal mechanism (Major)
- GDPR-6: Cookie policy link (Minor)

**CCPA Criteria:**
- CCPA-1: "Do Not Sell" link (Critical)
- CCPA-2: Opt-out mechanism (Critical)
- CCPA-3: Privacy policy (Major)
- CCPA-4: Rights disclosure (Minor)

---

## /annotate

Manual screenshot annotation.

```
/annotate --element <selector> [options]

Required:
  --element <selector>  CSS selector for element

Optional:
  --label <text>        Annotation label
  --type <type>         box | highlight | callout
  --color <color>       red | orange | green | blue
  --style <style>       solid | dashed
```

---

## /user-test-create-persona

Interactive wizard for custom persona creation.

No arguments - launches interactive wizard that:
1. Accepts natural language description
2. Generates complete persona with 3 gender variants
3. Creates timing patterns and expressions
4. Saves to plugin or project directory

**Example prompt:**
> "A healthcare worker in their 40s who is forced to use the software by their employer. They're busy, interrupted constantly, and just want to get through required tasks quickly."
