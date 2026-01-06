---
description: Run a WCAG 2.1 accessibility audit on a web page. Checks Level A and AA compliance criteria, generates a score, and provides detailed remediation guidance.
---

# WCAG Audit Command

Perform a comprehensive WCAG 2.1 accessibility audit on a web page to identify compliance issues and generate an actionable report.

## Arguments

Parse the following from `$ARGUMENTS`:

- `--url <url>` (required): URL to audit
- `--level <a|aa>` (optional): Conformance level to check (default: aa, which includes a)
- `--format <format>` (optional): Output format - summary | detailed | json (default: detailed)
- `--save <filename>` (optional): Save report to file
- `--annotate` (optional): Take annotated screenshots of violations

## Prerequisites

This command requires:
1. Claude running with `--chrome` flag
2. Playwright MCP tools available

## Audit Flow

### Step 1: Initialize

1. Navigate to the target URL using `browser_navigate`
2. Wait for page to fully load
3. Take initial accessibility snapshot with `browser_snapshot`
4. Initialize counters for passed/failed criteria

### Step 2: Run Level A Checks

Check these essential WCAG 2.1 Level A criteria:

#### 1.1.1 Non-text Content
Check all images have appropriate alt text:
```
Use browser_evaluate to find:
- Images without alt attribute
- Images with empty alt on non-decorative images
- Background images conveying information without text alternative
```
Severity: Critical if functional images lack alt text

#### 1.3.1 Info and Relationships
Check semantic HTML usage:
```
Use browser_snapshot to verify:
- Proper heading hierarchy (h1 → h2 → h3)
- Lists use ul/ol/li elements
- Tables have proper th/caption
- Form fields have associated labels
```
Severity: Major

#### 1.3.2 Meaningful Sequence
Check reading order:
```
Use browser_evaluate to compare:
- Visual order vs DOM order
- CSS doesn't create confusing reordering
```
Severity: Major

#### 2.1.1 Keyboard Accessible
Check all interactive elements work with keyboard:
```
Use browser_press_key to Tab through page:
- Can reach all interactive elements
- Buttons, links, form fields are focusable
- Custom widgets have keyboard support
```
Severity: Critical

#### 2.1.2 No Keyboard Trap
Verify focus can move freely:
```
Tab through interactive elements:
- Focus doesn't get stuck in any element
- Can always Tab/Shift+Tab to move
- Escape closes modals/popups
```
Severity: Critical

#### 2.4.1 Bypass Blocks
Check for skip navigation:
```
Use browser_evaluate to find:
- Skip to main content link
- Proper landmark regions (nav, main, footer)
```
Severity: Major

#### 2.4.2 Page Titled
Check document title:
```
Use browser_evaluate:
- document.title exists and is descriptive
- Title reflects page content
```
Severity: Minor

#### 2.4.4 Link Purpose
Check link text is meaningful:
```
Use browser_snapshot to find:
- Links with text "click here", "read more" alone
- Links with no accessible name
- Adjacent links to same destination
```
Severity: Major

#### 3.1.1 Language of Page
Check lang attribute:
```
Use browser_evaluate:
- html element has lang attribute
- lang value is valid (e.g., "en", "es")
```
Severity: Minor

#### 3.3.1 Error Identification
Check form error handling:
```
Trigger form validation if possible:
- Error messages describe the issue
- Errors identify the field with problem
```
Severity: Major

#### 3.3.2 Labels or Instructions
Check form field labels:
```
Use browser_evaluate to check:
- All inputs have associated labels
- Labels are visible (not just placeholders)
- Required fields are indicated
```
Severity: Critical

#### 4.1.1 Parsing
Check for HTML errors:
```
Use browser_evaluate:
- No duplicate IDs
- Elements properly nested
- Required attributes present
```
Severity: Minor

#### 4.1.2 Name, Role, Value
Check ARIA usage:
```
Use browser_snapshot to verify:
- ARIA roles used correctly
- Required ARIA attributes present
- ARIA states update appropriately
```
Severity: Critical

### Step 3: Run Level AA Checks (if --level aa)

#### 1.4.3 Contrast (Minimum)
Check text contrast ratios:
```
Use browser_evaluate to calculate:
- Normal text: 4.5:1 minimum
- Large text (18pt+): 3:1 minimum
- Check foreground/background colors
```
Severity: Major

Contrast calculation formula:
```
L1 = lighter color luminance
L2 = darker color luminance
Ratio = (L1 + 0.05) / (L2 + 0.05)
```

#### 1.4.4 Resize Text
Check text scaling:
```
Use browser_evaluate to zoom to 200%:
- Text remains readable
- No content/functionality lost
- No horizontal scrolling for text
```
Severity: Major

#### 1.4.10 Reflow
Check responsive behavior:
```
Use browser_resize to 320px width:
- No horizontal scrollbar
- Content reflows properly
- All content accessible
```
Severity: Major

#### 1.4.11 Non-text Contrast
Check UI component contrast:
```
Use browser_evaluate:
- Form input borders: 3:1 minimum
- Button boundaries: 3:1 minimum
- Focus indicators: 3:1 minimum
```
Severity: Major

#### 2.4.6 Headings and Labels
Check descriptive headings:
```
Use browser_snapshot:
- Headings describe content
- Form labels are clear
- No generic headings like "Section 1"
```
Severity: Minor

#### 2.4.7 Focus Visible
Check focus indicators:
```
Tab through page and verify:
- All focused elements have visible indicator
- Focus style is clear (not just browser default)
- Sufficient contrast on focus
```
Severity: Major

### Step 4: Calculate Score

```
Level A Score = (Passed A Criteria / Total A Criteria) × 100
Level AA Score = (Passed AA Criteria / Total AA Criteria) × 100
Overall Score = (All Passed / All Tested) × 100

Grade:
  90-100% = A (Excellent)
  80-89%  = B (Good)
  70-79%  = C (Needs Improvement)
  60-69%  = D (Poor)
  <60%    = F (Failing)
```

### Step 5: Generate Report

#### Summary Format (--format summary)
```
WCAG 2.1 Audit Report
═════════════════════

URL: [url]
Date: [timestamp]
Level: [level]

COMPLIANCE SCORE: [score]% ([grade])
────────────────────────────────────
Level A:  [a_score]% ([a_passed]/[a_total])
Level AA: [aa_score]% ([aa_passed]/[aa_total])

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
```
DETAILED FINDINGS
═════════════════════

CRITICAL ISSUES
───────────────────────────────

[criterion] [FAIL]
  Severity: Critical
  Found: [description]

  Locations:
  - [element] ([location hint])

  Fix: [remediation guidance]
  Example: [code example]

───────────────────────────────

[Continue for all issues...]

PASSED CRITERIA
───────────────────────────────
✓ [criterion] - [description]
[...]

RECOMMENDATIONS
───────────────────────────────
1. [recommendation] (Priority: [High/Medium/Low])
[...]
```

#### JSON Format (--format json)
```json
{
  "url": "[url]",
  "timestamp": "[iso date]",
  "level": "[a|aa]",
  "score": {
    "overall": [number],
    "grade": "[letter]",
    "levelA": { "score": [number], "passed": [n], "total": [n] },
    "levelAA": { "score": [number], "passed": [n], "total": [n] }
  },
  "issues": [
    {
      "criterion": "[wcag id]",
      "title": "[criterion title]",
      "level": "[A|AA]",
      "severity": "[critical|major|minor]",
      "status": "[pass|fail]",
      "description": "[what was found]",
      "locations": ["[element selectors]"],
      "remediation": "[how to fix]"
    }
  ]
}
```

### Step 6: Annotate (if --annotate)

For each critical/major violation, use the screenshot annotator to highlight:
```
1. Create overlay
2. Add red box around violating elements
3. Take screenshot: wcag-[criterion]-violation.png
4. Remove overlay
```

## Example Usage

### Basic Audit
```
/wcag-audit --url https://example.com
```

### Level A Only
```
/wcag-audit --url https://example.com --level a
```

### Summary Output
```
/wcag-audit --url https://example.com --format summary
```

### Save Report
```
/wcag-audit --url https://example.com --save accessibility-report.md
```

### With Violation Screenshots
```
/wcag-audit --url https://example.com --annotate
```

## Error Handling

### URL Not Accessible
```
Could not load [url]: [error]
Please check the URL is correct and accessible.
```

### Audit Timeout
```
Audit timed out after [X] seconds.
Partial results:
[show completed checks]
```

### Browser Not Available
```
Chrome browser not available.
Run Claude with: claude --chrome
```

## Severity Definitions

| Severity | Impact | User Effect |
|----------|--------|-------------|
| Critical | Blocks access | Cannot use feature at all |
| Major | Significantly impairs | Very difficult to use |
| Minor | Inconvenience | Usable but suboptimal |

## Integration

### Reference in Persona Testing
After running WCAG audit:
```
"WCAG audit found 3 critical issues that would affect screen reader users..."
```

### With Screenshot Annotations
Violations can be visually highlighted in screenshots for clearer reporting.
