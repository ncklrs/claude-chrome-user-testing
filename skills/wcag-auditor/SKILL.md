# WCAG Auditor Skill

This skill provides WCAG 2.1 accessibility audit capabilities, including criteria definitions, check implementations, and scoring logic.

## Purpose

Systematically evaluate web pages against WCAG 2.1 Level A and AA success criteria to identify accessibility barriers and provide remediation guidance.

## WCAG 2.1 Overview

WCAG is organized around four principles (POUR):
- **Perceivable**: Information must be presentable to users
- **Operable**: Interface must be usable
- **Understandable**: Information and operation must be understandable
- **Robust**: Content must be interpretable by assistive technologies

## Conformance Levels

| Level | Description | Requirements |
|-------|-------------|--------------|
| A | Minimum | Essential accessibility |
| AA | Standard | Most common requirement |
| AAA | Enhanced | Highest accessibility |

This skill implements Level A and AA checks.

## Check Implementation Guide

### Using browser_evaluate for DOM Checks

Most WCAG checks can be performed via JavaScript evaluation:

```javascript
// Example: Check for images without alt text
(function() {
  const images = document.querySelectorAll('img');
  const violations = [];

  images.forEach(function(img) {
    if (!img.hasAttribute('alt')) {
      violations.push({
        element: img.outerHTML.substring(0, 100),
        selector: getSelector(img)
      });
    }
  });

  return { passed: violations.length === 0, violations: violations };
})();
```

### Using browser_snapshot for Accessibility Tree

The accessibility snapshot provides:
- ARIA roles and properties
- Accessible names
- Focusable elements
- Heading structure

Parse the snapshot to verify semantic structure.

### Using browser_press_key for Keyboard Tests

Tab through the page to verify:
- All interactive elements are reachable
- Focus order is logical
- No focus traps exist

## Criteria Reference

### Level A Criteria

#### 1.1.1 Non-text Content
**Requirement**: All non-text content has a text alternative.

**Check**:
```javascript
// Find images without alt
const imagesWithoutAlt = document.querySelectorAll('img:not([alt])');

// Find inputs type=image without alt
const imageInputsWithoutAlt = document.querySelectorAll('input[type="image"]:not([alt])');

// Find area elements without alt
const areasWithoutAlt = document.querySelectorAll('area:not([alt])');
```

**Pass if**: No violations found
**Severity**: Critical (for functional images), Minor (for decorative)

---

#### 1.3.1 Info and Relationships
**Requirement**: Information, structure, and relationships are programmatically determinable.

**Check**:
```javascript
// Check heading hierarchy
const headings = document.querySelectorAll('h1, h2, h3, h4, h5, h6');
let lastLevel = 0;
const violations = [];

headings.forEach(function(h) {
  const level = parseInt(h.tagName[1]);
  if (level > lastLevel + 1) {
    violations.push({ element: h, issue: 'Skipped heading level' });
  }
  lastLevel = level;
});

// Check form labels
const inputs = document.querySelectorAll('input, select, textarea');
inputs.forEach(function(input) {
  if (!input.labels || input.labels.length === 0) {
    if (!input.getAttribute('aria-label') && !input.getAttribute('aria-labelledby')) {
      violations.push({ element: input, issue: 'Missing label' });
    }
  }
});
```

**Severity**: Major

---

#### 2.1.1 Keyboard
**Requirement**: All functionality is available via keyboard.

**Check**: Tab through page and verify all interactive elements are reachable.

**Severity**: Critical

---

#### 2.1.2 No Keyboard Trap
**Requirement**: Focus can move away from any component.

**Check**: After focusing each element, verify Tab/Shift+Tab moves focus.

**Severity**: Critical

---

#### 2.4.1 Bypass Blocks
**Requirement**: Mechanism to bypass repeated content.

**Check**:
```javascript
// Look for skip links
const skipLink = document.querySelector('a[href^="#main"], a[href^="#content"], .skip-link');

// Look for landmark regions
const landmarks = document.querySelectorAll('main, [role="main"], nav, [role="navigation"]');

return { hasSkipLink: !!skipLink, hasLandmarks: landmarks.length > 0 };
```

**Severity**: Major

---

#### 2.4.2 Page Titled
**Requirement**: Web pages have titles describing topic or purpose.

**Check**:
```javascript
const title = document.title;
return {
  hasTitle: title && title.trim().length > 0,
  title: title,
  isDescriptive: title && title.length > 5 && !title.match(/^(untitled|home|page)$/i)
};
```

**Severity**: Minor

---

#### 3.1.1 Language of Page
**Requirement**: Default human language is programmatically determinable.

**Check**:
```javascript
const htmlLang = document.documentElement.getAttribute('lang');
const validLangs = ['en', 'es', 'fr', 'de', 'it', 'pt', 'zh', 'ja', 'ko', 'ar', 'ru'];
return {
  hasLang: !!htmlLang,
  lang: htmlLang,
  isValid: validLangs.some(function(l) { return htmlLang && htmlLang.startsWith(l); })
};
```

**Severity**: Minor

---

#### 3.3.2 Labels or Instructions
**Requirement**: Labels or instructions provided for user input.

**Check**:
```javascript
const formFields = document.querySelectorAll('input:not([type="hidden"]), select, textarea');
const violations = [];

formFields.forEach(function(field) {
  const hasLabel = field.labels && field.labels.length > 0;
  const hasAriaLabel = field.getAttribute('aria-label');
  const hasAriaLabelledby = field.getAttribute('aria-labelledby');
  const hasTitle = field.getAttribute('title');

  if (!hasLabel && !hasAriaLabel && !hasAriaLabelledby && !hasTitle) {
    violations.push({ element: field, issue: 'No accessible label' });
  }
});
```

**Severity**: Critical

---

#### 4.1.1 Parsing
**Requirement**: No duplicate IDs, elements properly nested.

**Check**:
```javascript
const allIds = document.querySelectorAll('[id]');
const idMap = {};
const duplicates = [];

allIds.forEach(function(el) {
  const id = el.id;
  if (idMap[id]) {
    duplicates.push(id);
  }
  idMap[id] = true;
});

return { hasDuplicates: duplicates.length > 0, duplicates: duplicates };
```

**Severity**: Minor

---

#### 4.1.2 Name, Role, Value
**Requirement**: UI components have accessible name, role, and state.

**Check via browser_snapshot**: Verify custom widgets have appropriate ARIA.

**Severity**: Critical

---

### Level AA Criteria

#### 1.4.3 Contrast (Minimum)
**Requirement**: Text has contrast ratio of at least 4.5:1 (3:1 for large text).

**Check**:
```javascript
function getLuminance(r, g, b) {
  var a = [r, g, b].map(function(v) {
    v /= 255;
    return v <= 0.03928 ? v / 12.92 : Math.pow((v + 0.055) / 1.055, 2.4);
  });
  return a[0] * 0.2126 + a[1] * 0.7152 + a[2] * 0.0722;
}

function getContrastRatio(l1, l2) {
  var lighter = Math.max(l1, l2);
  var darker = Math.min(l1, l2);
  return (lighter + 0.05) / (darker + 0.05);
}

// Check text elements
var textElements = document.querySelectorAll('p, span, a, h1, h2, h3, h4, h5, h6, li, td, th, label');
var violations = [];

textElements.forEach(function(el) {
  var style = window.getComputedStyle(el);
  var color = style.color;
  var bgColor = style.backgroundColor;
  // ... calculate contrast
});
```

**Severity**: Major

---

#### 1.4.11 Non-text Contrast
**Requirement**: UI components have 3:1 contrast ratio.

**Check**: Verify button borders, input borders, focus indicators have sufficient contrast.

**Severity**: Major

---

#### 2.4.7 Focus Visible
**Requirement**: Keyboard focus indicator is visible.

**Check**: Tab through elements and verify focus styles are visible.

**Severity**: Major

---

## Scoring Logic

### Calculate Scores

```javascript
function calculateScore(results) {
  var levelA = results.filter(function(r) { return r.level === 'A'; });
  var levelAA = results.filter(function(r) { return r.level === 'AA'; });

  var aScore = (levelA.filter(function(r) { return r.passed; }).length / levelA.length) * 100;
  var aaScore = (levelAA.filter(function(r) { return r.passed; }).length / levelAA.length) * 100;
  var overall = (results.filter(function(r) { return r.passed; }).length / results.length) * 100;

  return {
    levelA: Math.round(aScore),
    levelAA: Math.round(aaScore),
    overall: Math.round(overall),
    grade: getGrade(overall)
  };
}

function getGrade(score) {
  if (score >= 90) return 'A';
  if (score >= 80) return 'B';
  if (score >= 70) return 'C';
  if (score >= 60) return 'D';
  return 'F';
}
```

## Remediation Guidance

### Common Fixes

| Issue | Fix |
|-------|-----|
| Missing alt text | Add descriptive alt attribute |
| Missing form labels | Associate label with for attribute |
| Low contrast | Increase color difference |
| No skip link | Add "Skip to main content" link |
| Missing lang | Add lang attribute to html element |
| Keyboard trap | Ensure Tab/Escape can exit |
| No focus visible | Add :focus styles with outline |

### Code Examples

**Adding alt text**:
```html
<!-- Before -->
<img src="chart.png">

<!-- After -->
<img src="chart.png" alt="Sales chart showing 20% growth in Q4">
```

**Adding form label**:
```html
<!-- Before -->
<input type="email" placeholder="Email">

<!-- After -->
<label for="email">Email address</label>
<input type="email" id="email">
```

**Adding skip link**:
```html
<a href="#main-content" class="skip-link">Skip to main content</a>
<!-- ... header/nav ... -->
<main id="main-content">
```

**Improving contrast**:
```css
/* Before: 2.5:1 ratio */
.text { color: #999; background: #fff; }

/* After: 4.6:1 ratio */
.text { color: #666; background: #fff; }
```

## Integration with Screenshot Annotator

When violations are found, annotate them:

1. For each violation with identifiable element
2. Get element position
3. Add red box annotation with criterion ID as label
4. Take screenshot
5. Include in report

## Report Templates

See `commands/wcag-audit.md` for full report format templates.
