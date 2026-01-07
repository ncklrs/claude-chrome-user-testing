---
description: Check for broken links on a webpage. Crawls the page to find all links and validates each one, reporting 404s, redirects, and other issues.
---

# Check Links Command

Find broken links on a webpage. Validates all links found on the page and reports issues.

## Arguments

Parse the following from `$ARGUMENTS`:

- `--url <url>` (required): URL to check for broken links
- `--depth <n>` (optional): How many levels deep to crawl (default: 1, max: 3)
- `--internal-only` (optional): Only check links to the same domain
- `--external-only` (optional): Only check external links
- `--exclude <pattern>` (optional): Exclude URLs matching this pattern
- `--quiet` (optional): Summary only output

## How It Works

1. **Navigate** to the target URL
2. **Extract** all links from the page (`<a href>`, etc.)
3. **Categorize** links as internal or external
4. **Validate** each link by making a HEAD request
5. **Report** broken links, redirects, and issues

## Link Categories

| Category | Description | How Checked |
|----------|-------------|-------------|
| Internal | Same domain | HEAD request |
| External | Different domain | HEAD request (if not `--internal-only`) |
| Anchor | Same page `#section` | Verify element exists |
| mailto: | Email links | Format validation only |
| tel: | Phone links | Format validation only |
| javascript: | Script links | Flagged as warning |

## Validation Results

| Status | Meaning | Severity |
|--------|---------|----------|
| 200 | OK | Good |
| 301/302 | Redirect | Warning |
| 404 | Not Found | Error |
| 403 | Forbidden | Warning |
| 500 | Server Error | Error |
| Timeout | No response | Error |

## Report Format

### Standard Report

```markdown
# Link Check Report

## Summary
- **URL Checked**: [url]
- **Links Found**: 47
- **Working**: 44 (94%)
- **Broken**: 2 (4%)
- **Redirects**: 3
- **Skipped**: 1 (external)

## Broken Links

| Link | Found On | Status | Error |
|------|----------|--------|-------|
| /products/old-item | /shop | 404 | Not Found |
| /team/john-doe | /about | 404 | Not Found |

## Redirects (Consider Updating)

| Link | Redirects To | Status |
|------|--------------|--------|
| /blog | /news | 301 Permanent |
| /old-page | /new-page | 302 Temporary |
| http://example.com | https://example.com | 301 Permanent |

## Warnings

| Issue | Link | Notes |
|-------|------|-------|
| javascript: link | onclick handler | /nav button |
| Empty href | # | /footer link |
| Slow response | /large-page | 3.2s response time |

## All Links (by page)

### / (Home)
- [x] /about
- [x] /products
- [ ] /old-feature (404)
- [x] /contact

### /about
- [x] /team
- [ ] /team/john-doe (404)
```

### Quiet Mode

```
Link Check: https://example.com

Checking 47 links...

Broken (2):
  /products/old-item -> 404
  /team/john-doe -> 404

Redirects (3):
  /blog -> /news (301)
  /old-page -> /new-page (302)

Summary: 44/47 working (94%)
```

## Depth Crawling

### Depth 1 (Default)
Check only links on the initial page.

### Depth 2
Check links on the initial page, then check links found on those pages.

### Depth 3 (Maximum)
Three levels deep. Warning: Can be slow on large sites.

```
/
├── /about (depth 1)
│   ├── /team (depth 2)
│   │   └── /team/john (depth 3)
│   └── /history (depth 2)
├── /products (depth 1)
│   └── /products/item-1 (depth 2)
└── /contact (depth 1)
```

## Example Usage

### Basic link check
```
/check-links --url https://example.com
```

### Check internal links only
```
/check-links --url https://example.com --internal-only
```

### Deep crawl
```
/check-links --url https://example.com --depth 2
```

### Exclude certain paths
```
/check-links --url https://example.com --exclude "/admin/*"
```

### CI/CD mode
```
/check-links --url https://example.com --internal-only --quiet
```

## Integration with User Testing

Add `--check-links` to any `/user-test` command:

```
/user-test --url https://example.com --persona genz-digital-native --check-links
```

This will:
1. Run the normal user test
2. Collect all links encountered during testing
3. Add a "Link Health" section to the report

## Error Handling

### Timeout
```
Link timeout: /slow-page
  No response within 10 seconds
  Marked as broken
```

### SSL Issues
```
SSL Error: https://expired-cert.example.com
  Certificate has expired
  Marked as error
```

### Rate Limiting
```
Rate limited: 429 Too Many Requests
  Waiting 5 seconds before retry...
  Retry succeeded: 200 OK
```

## Best Practices

1. **Start with depth 1**: Deep crawls can be slow
2. **Use `--internal-only`**: External sites may rate limit
3. **Run regularly**: Links break over time
4. **Fix 404s first**: Then address redirects
5. **Update redirects**: Replace old links with new destinations
