# Cookie Auditor Skill

Audit web pages for GDPR and CCPA cookie consent compliance.

## When to Use

- Verifying GDPR compliance before EU launch
- Checking CCPA compliance for California users
- Pre-deployment privacy compliance checks
- Auditing third-party vendors' consent implementation
- CI/CD compliance gates

## Audit Approach

### Phase 1: Fresh Session Setup

Start with a clean browser state:

```javascript
// Via browser_run_code
async (page) => {
  // Clear all cookies
  await page.context().clearCookies();

  // Clear local storage
  await page.evaluate(() => {
    localStorage.clear();
    sessionStorage.clear();
  });

  return 'Session cleared';
}
```

### Phase 2: Pre-Consent Cookie Capture

Before any interaction, record what cookies are set:

```javascript
async (page) => {
  const cookies = await page.context().cookies();
  return cookies.map(c => ({
    name: c.name,
    domain: c.domain,
    path: c.path,
    expires: c.expires,
    httpOnly: c.httpOnly,
    secure: c.secure,
    sameSite: c.sameSite
  }));
}
```

### Phase 3: Cookie Classification

Classify cookies by purpose:

| Pattern | Category | Essential |
|---------|----------|-----------|
| `session`, `csrf`, `auth` | Necessary | Yes |
| `_ga`, `_gid`, `analytics` | Analytics | No |
| `_fbp`, `_gcl`, `ads` | Marketing | No |
| `prefs`, `theme`, `lang` | Personalization | No |

### Phase 4: Banner Detection

Look for cookie consent elements:

```javascript
// Common selectors for cookie banners
const bannerSelectors = [
  '[class*="cookie"]',
  '[id*="cookie"]',
  '[class*="consent"]',
  '[id*="consent"]',
  '[class*="gdpr"]',
  '[class*="privacy-banner"]',
  '[aria-label*="cookie"]',
  '[role="dialog"][class*="modal"]'
];
```

### Phase 5: Consent Mechanism Analysis

Check consent UI for compliance:

**Accept Button Detection:**
```javascript
const acceptSelectors = [
  'button:has-text("Accept")',
  'button:has-text("Allow")',
  'button:has-text("Agree")',
  'button:has-text("OK")',
  '[class*="accept"]'
];
```

**Reject Button Detection:**
```javascript
const rejectSelectors = [
  'button:has-text("Reject")',
  'button:has-text("Decline")',
  'button:has-text("Deny")',
  'a:has-text("Reject")',
  '[class*="reject"]',
  '[class*="decline"]'
];
```

**Preference Detection:**
```javascript
const preferenceSelectors = [
  'button:has-text("Settings")',
  'button:has-text("Preferences")',
  'button:has-text("Manage")',
  'button:has-text("Customize")'
];
```

## GDPR Criteria Reference

Load criteria from `criteria.json`:

| ID | Criterion | Severity | Check Method |
|----|-----------|----------|--------------|
| GDPR-1 | Banner before cookies | Critical | Pre-consent cookie check |
| GDPR-2 | No pre-checked boxes | Critical | Checkbox state analysis |
| GDPR-3 | Reject option prominent | Major | Button comparison |
| GDPR-4 | Granular consent | Major | Category options check |
| GDPR-5 | Withdrawal mechanism | Major | Settings accessibility |
| GDPR-6 | Cookie policy link | Minor | Link presence check |

## CCPA Criteria Reference

| ID | Criterion | Severity | Check Method |
|----|-----------|----------|--------------|
| CCPA-1 | Do Not Sell link | Critical | Footer link search |
| CCPA-2 | Opt-out mechanism | Critical | Functional test |
| CCPA-3 | Privacy policy | Major | Link presence check |
| CCPA-4 | Rights disclosure | Minor | Content analysis |

## Detection Methods

### Pre-Checked Box Detection

```javascript
async (page) => {
  const checkboxes = await page.$$('input[type="checkbox"]');
  const preChecked = [];

  for (const checkbox of checkboxes) {
    const isChecked = await checkbox.isChecked();
    const label = await checkbox.evaluate(el => {
      const label = el.closest('label') ||
                    document.querySelector(`label[for="${el.id}"]`);
      return label?.textContent || '';
    });

    if (isChecked && !isNecessary(label)) {
      preChecked.push({ label, checked: true });
    }
  }

  return preChecked;
}
```

### Button Prominence Comparison

```javascript
async (page) => {
  const accept = await page.$('button:has-text("Accept")');
  const reject = await page.$('button:has-text("Reject")');

  if (!accept || !reject) return { prominent: false, reason: 'Missing button' };

  const acceptBox = await accept.boundingBox();
  const rejectBox = await reject.boundingBox();

  // Compare sizes
  const acceptArea = acceptBox.width * acceptBox.height;
  const rejectArea = rejectBox.width * rejectBox.height;

  // Reject should be at least 50% the size of accept
  return {
    prominent: rejectArea >= acceptArea * 0.5,
    acceptSize: acceptArea,
    rejectSize: rejectArea
  };
}
```

### CCPA Link Detection

```javascript
async (page) => {
  const ccpaPatterns = [
    'Do Not Sell',
    'Do Not Share',
    'Opt-Out',
    'Your Privacy Choices',
    'Your California Privacy Rights'
  ];

  for (const pattern of ccpaPatterns) {
    const link = await page.$(`a:has-text("${pattern}")`);
    if (link) {
      return { found: true, text: pattern };
    }
  }

  return { found: false };
}
```

## Scoring

```javascript
function calculateScore(results) {
  const weights = {
    critical: 3,
    major: 2,
    minor: 1
  };

  let maxScore = 0;
  let actualScore = 0;

  for (const result of results) {
    const weight = weights[result.severity];
    maxScore += weight;
    if (result.status === 'pass') {
      actualScore += weight;
    }
  }

  const percentage = Math.round((actualScore / maxScore) * 100);
  const grade =
    percentage >= 90 ? 'A' :
    percentage >= 80 ? 'B' :
    percentage >= 70 ? 'C' :
    percentage >= 60 ? 'D' : 'F';

  return { percentage, grade };
}
```

## Report Generation

### Issue Format

```markdown
### FAIL: [ID] - [Title]
- **Severity**: [Critical/Major/Minor]
- **Finding**: [What was observed]
- **Location**: [Where on the page]
- **Fix**: [Remediation guidance]
```

### Pass Format

```markdown
### PASS: [ID] - [Title]
- **Finding**: [What was observed that indicates compliance]
```

## Common Violations

### 1. Analytics Before Consent

**Pattern**: Google Analytics (_ga, _gid) cookies set on page load
**Detection**: Check cookies before any user interaction
**Fix**: Defer analytics until after consent

### 2. Pre-Checked Marketing

**Pattern**: "Marketing cookies" checkbox checked by default
**Detection**: Inspect checkbox state in banner
**Fix**: Default all non-essential to unchecked

### 3. Hidden Reject Option

**Pattern**: "Reject" is a small link, "Accept" is a large button
**Detection**: Compare element sizes
**Fix**: Make both options equally prominent

### 4. Missing CCPA Link

**Pattern**: No "Do Not Sell" link on California-targeted site
**Detection**: Search footer/privacy areas
**Fix**: Add compliant opt-out link

## Best Practices

1. **Test Fresh Sessions**: Always clear cookies before audit
2. **Geo-Simulation**: Some banners only show to EU/CA visitors
3. **Mobile Check**: Banner behavior may differ on mobile
4. **Deep Pages**: Check if consent persists across navigation
5. **Screenshots**: Document violations with annotated screenshots

## Limitations

1. **Geo-Targeting**: Banner may not appear without EU/CA IP
2. **A/B Tests**: Different users may see different consent UIs
3. **Dynamic Loading**: Some consent tools load asynchronously
4. **Third-Party Tools**: Complex consent managers may need special handling
5. **Legal Interpretation**: This tool checks technical compliance, not legal advice
