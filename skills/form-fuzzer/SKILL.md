# Form Fuzzer Skill

Test form inputs with edge case payloads to find security vulnerabilities and encoding issues.

## When to Use

- Security testing forms for XSS/SQLi vulnerabilities
- Testing input validation and sanitization
- Finding unicode/encoding handling issues
- Checking length limit enforcement
- Pre-deployment security checks

## Flags

```
--fuzz                           Enable fuzzing mode
--fuzz-category <category>       Category: xss | sqli | unicode | length | all (default: all)
```

## Fuzzing Categories

| Category | Purpose | Payload Count |
|----------|---------|---------------|
| `xss` | Test XSS sanitization | 15 payloads |
| `sqli` | Test SQL injection prevention | 12 payloads |
| `unicode` | Test encoding handling | 10 payloads |
| `length` | Test length limits | 4 payloads |
| `all` | All categories combined | 41 payloads |

## Behavior

When `--fuzz` is active:

1. **Discovery**: Find all form inputs on the page
2. **Classification**: Identify input types (text, email, password, search, etc.)
3. **Injection**: For each input, try payloads from selected category
4. **Observation**: Check response for each payload:
   - **Sanitized**: Payload escaped/stripped (PASS)
   - **Rejected**: Clear error message (PASS)
   - **Executed**: XSS/SQLi worked (CRITICAL)
   - **Error**: Application error/crash (MAJOR)
   - **Stored**: Payload persists in DOM (CRITICAL)
5. **Reporting**: Generate security-focused report section

## Response Classification

### XSS Detection

```javascript
// Check if script executed
// Check if payload appears unescaped in DOM
// Check for alert/console activity
// Check if event handlers fire
```

### SQL Injection Detection

```javascript
// Check for database error messages
// Check for unusual response time (time-based)
// Check for data leakage
// Check for error 500 responses
```

### Encoding Issues

```javascript
// Check for garbled text
// Check for broken layout (RTL markers)
// Check for missing characters
// Check for incorrect rendering
```

### Length Handling

```javascript
// Check if input truncated
// Check for overflow errors
// Check for client-side crashes
// Check for server errors
```

## Report Section

When fuzzing is enabled, add this section to the test report:

```markdown
## Form Fuzz Testing Results

### Summary
- **Inputs Tested**: 5
- **Payloads Tried**: 205
- **Vulnerabilities Found**: 2

### Critical Findings

| Input | Category | Payload | Result |
|-------|----------|---------|--------|
| #name | XSS | `<script>alert(1)</script>` | Executed |
| #search | SQLi | `' OR '1'='1` | Error 500 |

### XSS Tests (3 inputs)

| Input | Sample Payload | Result |
|-------|----------------|--------|
| #email | `<script>alert(1)</script>` | Sanitized |
| #name | `<script>alert(1)</script>` | **Executed** |
| #bio | `<img onerror=alert(1)>` | Sanitized |

### SQL Injection Tests (2 inputs)

| Input | Sample Payload | Result |
|-------|----------------|--------|
| #search | `' OR '1'='1` | **Error 500** |
| #id | `1; DROP TABLE--` | Rejected |

### Unicode Tests (5 inputs)

| Input | Sample Payload | Result |
|-------|----------------|--------|
| #name | `cafe n beijing` | Rendered OK |
| #bio | RTL markers | Layout OK |

### Length Tests (3 inputs)

| Input | Length | Result |
|-------|--------|--------|
| #email | 1K chars | Truncated (OK) |
| #bio | 10K chars | Accepted |
| #bio | 100K chars | **Client crash** |

### Recommendations

1. **[Critical]** Sanitize `#name` field - XSS vulnerable
2. **[Critical]** Fix SQL error handling in `#search` - reveals database info
3. **[Major]** Add length validation to `#bio` - crashes on large input
```

## Integration with Personas

Fuzzing works alongside persona testing:

```bash
# Fuzz while testing as GenZ user
/user-test --url https://example.com/signup --persona genz-digital-native --fuzz

# Fuzz specific category
/user-test --url https://example.com/login --persona developer-critic --fuzz --fuzz-category xss
```

The persona still drives navigation and task completion, but form inputs are fuzzed when encountered.

## Input Selection

### Fuzz These Inputs

- `<input type="text">`
- `<input type="email">`
- `<input type="password">`
- `<input type="search">`
- `<input type="url">`
- `<input type="tel">`
- `<textarea>`
- `<input type="hidden">` (if accessible)
- Rich text editors (contenteditable)

### Skip These Inputs

- `<input type="checkbox">`
- `<input type="radio">`
- `<input type="file">`
- `<input type="date/time">`
- `<select>` dropdowns
- Disabled/readonly inputs

## Best Practices

1. **Start with `--fuzz-category xss`**: Most common web vulnerability
2. **Test staging, not production**: Fuzzing can cause side effects
3. **Combine with `--record`**: Capture evidence of vulnerabilities
4. **Use `--json` for CI/CD**: Parse fuzz results programmatically
5. **Test authenticated flows**: Many vulnerabilities behind login

## Limitations

1. **Client-side only**: Cannot detect server-side-only vulnerabilities
2. **No blind injection**: Time-based SQLi detection is limited
3. **May trigger WAF**: Fuzzing payloads can trigger security rules
4. **State changes**: Fuzzing may create test data in database
5. **Rate limiting**: May hit rate limits on aggressive testing

## Severity Classification

| Severity | Criteria | Example |
|----------|----------|---------|
| Critical | Code execution, data exposure | XSS executed, SQLi data leak |
| Major | Application errors, crashes | Error 500, client crash |
| Minor | Poor handling, no security impact | Garbled unicode, no truncation |
| Info | Observations, potential issues | Long response time, verbose errors |
