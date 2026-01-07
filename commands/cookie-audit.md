---
description: Run a GDPR/CCPA cookie consent compliance audit on a web page. Checks banner behavior, consent mechanisms, and generates an actionable compliance report.
---

# Cookie Audit Command

Perform a comprehensive cookie consent compliance audit to identify GDPR and CCPA violations and generate an actionable report.

## Arguments

Parse the following from `$ARGUMENTS`:

- `--url <url>` (required): URL to audit
- `--regulation <gdpr|ccpa|both>` (optional): Target regulation (default: both)
- `--format <format>` (optional): Output format - summary | detailed | json (default: detailed)
- `--save <filename>` (optional): Save report to file
- `--annotate` (optional): Take annotated screenshots of violations

## Prerequisites

This command requires:
1. Claude running with `--chrome` flag
2. Playwright MCP tools available

## Audit Flow

### Step 1: Initialize

1. Clear browser cookies/storage to simulate fresh visit
2. Navigate to the target URL using `browser_navigate`
3. Record what cookies are set BEFORE any user interaction
4. Take initial snapshot with `browser_snapshot`
5. Look for cookie consent banner

### Step 2: Pre-Consent Cookie Check

Before interacting with any consent mechanism:

```
Use browser_run_code to check cookies:
async (page) => {
  const cookies = await page.context().cookies();
  return cookies.map(c => ({
    name: c.name,
    domain: c.domain,
    category: classifyCookie(c.name) // analytics, marketing, necessary
  }));
}
```

Record any non-essential cookies set before consent.

### Step 3: Run GDPR Checks (if --regulation gdpr or both)

Check these GDPR cookie consent requirements:

#### GDPR-1: Banner Before Cookies (Critical)

```
Check: Non-essential cookies should not be set before consent
Pass: Only necessary cookies exist on initial load
Fail: Analytics/marketing cookies present before any interaction
```
Severity: Critical

#### GDPR-2: No Pre-Checked Boxes (Critical)

```
Check consent form for pre-checked options:
- Marketing cookies checkbox
- Analytics cookies checkbox
- Personalization checkbox

Use browser_snapshot to find:
- Checkboxes in consent banner
- Default checked state
```
Severity: Critical

#### GDPR-3: Reject Option Prominence (Major)

```
Check reject/decline option is as easy as accept:
- "Reject All" button exists
- Similar size and placement as "Accept All"
- No hidden or small link for rejection
- Not behind settings/preferences
```
Severity: Major

#### GDPR-4: Granular Consent (Major)

```
Check granular consent options available:
- Can choose cookie categories individually
- Categories clearly explained
- Not just "Accept All" or nothing
```
Severity: Major

#### GDPR-5: Withdrawal Mechanism (Major)

```
Check consent can be withdrawn:
- Settings/preferences accessible later
- Can change consent after initial choice
- Usually footer link or settings page
```
Severity: Major

#### GDPR-6: Cookie Policy Link (Minor)

```
Check for accessible cookie policy:
- Link in banner or easily found
- Explains cookie usage
- Lists cookie types and purposes
```
Severity: Minor

### Step 4: Run CCPA Checks (if --regulation ccpa or both)

Check these CCPA privacy requirements:

#### CCPA-1: Do Not Sell Link (Critical)

```
Check for "Do Not Sell My Personal Information" link:
- Present on page (usually footer)
- Clearly labeled
- Actually functional
```
Severity: Critical

#### CCPA-2: Opt-Out Mechanism (Critical)

```
Check opt-out process works:
- Clicking link leads to opt-out
- No account required to opt-out
- Process completes without barriers
```
Severity: Critical

#### CCPA-3: Privacy Policy (Major)

```
Check privacy policy accessibility:
- Privacy policy link present
- Describes data collection practices
- Explains consumer rights
```
Severity: Major

#### CCPA-4: Consumer Rights Disclosure (Minor)

```
Check disclosure of rights:
- Right to know what data collected
- Right to delete personal information
- Right to opt-out of sale
```
Severity: Minor

### Step 5: Calculate Score

```
GDPR Score = (Passed GDPR Criteria / Total GDPR Criteria) × 100
CCPA Score = (Passed CCPA Criteria / Total CCPA Criteria) × 100
Overall Score = (All Passed / All Tested) × 100

Grade:
  90-100% = A (Excellent compliance)
  80-89%  = B (Good compliance)
  70-79%  = C (Needs improvement)
  60-69%  = D (Poor compliance)
  <60%    = F (Non-compliant)
```

### Step 6: Generate Report

#### Summary Format (--format summary)
```
Cookie Consent Compliance Report
════════════════════════════════

URL: [url]
Date: [timestamp]
Regulation: [regulation]

COMPLIANCE SCORE: [score]% ([grade])
────────────────────────────────────
GDPR:  [gdpr_score]% ([gdpr_passed]/[gdpr_total])
CCPA:  [ccpa_score]% ([ccpa_passed]/[ccpa_total])

ISSUES BY SEVERITY
────────────────────────────────────
Critical: [count]
Major:    [count]
Minor:    [count]

TOP ISSUES
────────────────────────────────────
1. [issue description]
2. [issue description]
3. [issue description]
```

#### Detailed Format (--format detailed)
Include summary plus:
```markdown
# Cookie Consent Audit Report

## Summary
- **URL**: [url]
- **Date**: [timestamp]
- **Regulation**: [regulation]
- **Score**: [score]% ([grade])
- **Critical Issues**: [count]
- **Major Issues**: [count]
- **Minor Issues**: [count]

## Pre-Consent Cookies Found

| Cookie | Domain | Category | Compliant |
|--------|--------|----------|-----------|
| _ga | .example.com | Analytics | No - set before consent |
| session | example.com | Necessary | Yes |

## GDPR Compliance ([gdpr_passed]/[gdpr_total])

### FAIL: GDPR-2 - Pre-checked consent boxes
- **Severity**: Critical
- **Finding**: Marketing cookies checkbox is pre-checked
- **Location**: Cookie consent banner
- **Fix**: Uncheck all non-essential categories by default

### PASS: GDPR-6 - Cookie policy link
- **Finding**: Cookie policy link present in footer

## CCPA Compliance ([ccpa_passed]/[ccpa_total])

### FAIL: CCPA-1 - Missing "Do Not Sell" link
- **Severity**: Critical
- **Finding**: No "Do Not Sell My Personal Information" link found
- **Fix**: Add CCPA-compliant opt-out link to footer

## Recommendations

1. **[Critical]** Remove pre-checked consent boxes for GDPR compliance
2. **[Critical]** Add "Do Not Sell My Personal Information" link for CCPA
3. **[Major]** Add prominent "Reject All" button next to "Accept All"
```

#### JSON Format (--format json)
```json
{
  "url": "[url]",
  "timestamp": "[iso date]",
  "regulation": "[gdpr|ccpa|both]",
  "score": {
    "overall": 75,
    "grade": "C",
    "gdpr": { "score": 67, "passed": 4, "total": 6 },
    "ccpa": { "score": 75, "passed": 3, "total": 4 }
  },
  "preConsentCookies": [
    {
      "name": "_ga",
      "domain": ".example.com",
      "category": "analytics",
      "compliant": false
    }
  ],
  "issues": [
    {
      "id": "GDPR-2",
      "title": "No Pre-Checked Boxes",
      "regulation": "GDPR",
      "severity": "critical",
      "status": "fail",
      "finding": "Marketing cookies checkbox is pre-checked",
      "location": "Cookie consent banner",
      "remediation": "Uncheck all non-essential categories by default"
    }
  ],
  "passed": [
    {
      "id": "GDPR-6",
      "title": "Cookie Policy Link",
      "regulation": "GDPR"
    }
  ]
}
```

### Step 7: Annotate (if --annotate)

For each critical/major violation, use the screenshot annotator:
```
1. Create overlay
2. Add red box around issue (pre-checked box, missing link, etc.)
3. Take screenshot: cookie-[criterion]-violation.png
4. Remove overlay
```

## Example Usage

### Basic Audit (Both Regulations)
```
/cookie-audit --url https://example.com
```

### GDPR Only
```
/cookie-audit --url https://example.com --regulation gdpr
```

### CCPA Only
```
/cookie-audit --url https://example.com --regulation ccpa
```

### Summary Output
```
/cookie-audit --url https://example.com --format summary
```

### Save Report
```
/cookie-audit --url https://example.com --save cookie-compliance-report.md
```

### With Violation Screenshots
```
/cookie-audit --url https://example.com --annotate
```

### JSON for CI/CD
```
/cookie-audit --url https://example.com --format json
```

## Error Handling

### No Cookie Banner Found
```
Note: No cookie consent banner detected.
This may indicate:
- Banner only shown to EU/CA visitors (geo-based)
- Banner already dismissed (check fresh session)
- Site doesn't use cookie consent (potential violation)

Continuing with available checks...
```

### URL Not Accessible
```
Could not load [url]: [error]
Please check the URL is correct and accessible.
```

### Browser Not Available
```
Chrome browser not available.
Run Claude with: claude --chrome
```

## Severity Definitions

| Severity | Legal Risk | User Impact |
|----------|------------|-------------|
| Critical | Potential fines | Consent not properly obtained |
| Major | Compliance gaps | User choice limited |
| Minor | Best practice | Transparency issues |

## Cookie Categories

For classification:

| Category | Examples | Essential? |
|----------|----------|------------|
| Necessary | Session, CSRF tokens, auth | Yes |
| Analytics | Google Analytics, Mixpanel | No |
| Marketing | Facebook Pixel, ad trackers | No |
| Personalization | Theme, language, A/B test | No |

## Integration

### With User Testing

Can be combined with persona testing:
```
/user-test --url https://example.com --persona german-business-user
```
The German business user persona will naturally check for GDPR compliance as part of their expectations.

### CI/CD Pipeline

```yaml
- name: Cookie Compliance Check
  run: |
    claude --chrome "/cookie-audit \
      --url ${{ env.PREVIEW_URL }} \
      --format json \
      --save cookie-audit.json"

    # Fail if critical issues
    if grep -q '"severity": "critical"' cookie-audit.json; then
      exit 1
    fi
```
