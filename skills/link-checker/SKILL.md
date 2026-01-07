# Link Checker Skill

Find and validate links on web pages. Reports broken links, redirects, and other issues.

## When to Use

- Before releases to catch broken links
- After content updates
- During user testing (with `--check-links` flag)
- Regular site maintenance

## Link Extraction

### Using Browser Snapshot

Use `browser_snapshot` to get page accessibility tree, then extract links:

```javascript
// Links to extract
const linkSelectors = [
  'a[href]',           // Standard links
  'area[href]',        // Image map links
  'link[href]',        // Stylesheet/resource links (optional)
];
```

### Link Types to Identify

| Type | Pattern | Action |
|------|---------|--------|
| Internal | Same domain | Check via HEAD |
| External | Different domain | Check via HEAD (unless excluded) |
| Anchor | `#section` | Verify element exists |
| mailto: | `mailto:*` | Validate format |
| tel: | `tel:*` | Validate format |
| javascript: | `javascript:*` | Flag as warning |
| data: | `data:*` | Skip |

### Extracting via browser_evaluate

```javascript
async (page) => {
  const links = await page.evaluate(() => {
    const anchors = document.querySelectorAll('a[href]');
    return Array.from(anchors).map(a => ({
      href: a.href,
      text: a.textContent.trim().slice(0, 50),
      location: a.closest('[id]')?.id || 'page'
    }));
  });
  return links;
}
```

## Link Validation

### HEAD Request Method

For each link, make a HEAD request to check status:

```javascript
async (page) => {
  const response = await page.request.head(url, {
    timeout: 10000,
    ignoreHTTPSErrors: false
  });
  return {
    status: response.status(),
    headers: response.headers()
  };
}
```

### Status Interpretation

| Status Code | Category | Action |
|-------------|----------|--------|
| 200 | Success | Mark as working |
| 201-299 | Success | Mark as working |
| 301 | Permanent Redirect | Warn - update link |
| 302 | Temporary Redirect | Note redirect |
| 303, 307, 308 | Redirects | Note redirect |
| 400 | Bad Request | Error |
| 401, 403 | Auth Required | Warning |
| 404 | Not Found | Error - broken link |
| 405 | Method Not Allowed | Retry with GET |
| 429 | Rate Limited | Wait and retry |
| 500-599 | Server Error | Error |
| Timeout | No Response | Error |

### Handling Redirects

Track redirect chains:

```javascript
const checkLink = async (url) => {
  const redirects = [];
  let currentUrl = url;
  let maxRedirects = 5;

  while (maxRedirects > 0) {
    const response = await fetch(currentUrl, {
      method: 'HEAD',
      redirect: 'manual'
    });

    if (response.status >= 300 && response.status < 400) {
      const location = response.headers.get('location');
      redirects.push({ from: currentUrl, to: location, status: response.status });
      currentUrl = new URL(location, currentUrl).href;
      maxRedirects--;
    } else {
      return { finalStatus: response.status, redirects };
    }
  }

  return { error: 'Too many redirects', redirects };
};
```

## Depth Crawling

### Algorithm

```
function crawl(startUrl, maxDepth):
  visited = Set()
  toVisit = [(startUrl, 0)]  // (url, depth)
  results = []

  while toVisit not empty:
    (url, depth) = toVisit.pop()

    if url in visited or depth > maxDepth:
      continue

    visited.add(url)
    pageLinks = extractLinks(url)
    results.extend(validateLinks(pageLinks))

    if depth < maxDepth:
      for link in pageLinks:
        if isInternal(link) and link not in visited:
          toVisit.append((link, depth + 1))

  return results
```

### Performance Considerations

| Depth | Typical Links | Time |
|-------|---------------|------|
| 1 | 20-100 | 10-30s |
| 2 | 100-500 | 1-5 min |
| 3 | 500-2000+ | 5-20 min |

**Tips**:
- Default to depth 1
- Use `--internal-only` to reduce scope
- Parallelize requests where possible

## Report Generation

### Data Structure

```javascript
const linkReport = {
  url: 'https://example.com',
  checkedAt: '2025-01-06T14:30:00Z',
  summary: {
    total: 47,
    working: 44,
    broken: 2,
    redirects: 3,
    skipped: 1
  },
  broken: [
    { url: '/old-page', foundOn: '/', status: 404 },
  ],
  redirects: [
    { url: '/blog', redirectsTo: '/news', status: 301 },
  ],
  warnings: [
    { type: 'javascript-link', url: 'javascript:void(0)', foundOn: '/nav' },
  ],
  byPage: {
    '/': { checked: 15, broken: 1 },
    '/about': { checked: 8, broken: 1 },
  }
};
```

### Report Sections

1. **Summary**: Quick overview of link health
2. **Broken Links**: 404s and errors (highest priority)
3. **Redirects**: Links that should be updated
4. **Warnings**: javascript: links, slow responses, etc.
5. **By Page**: Where issues were found

## Integration with User Testing

When `--check-links` is added to `/user-test`:

### During Test

1. As persona navigates, collect all unique links seen
2. Don't interrupt persona flow for link checking
3. Store links for post-test validation

### After Test

1. Validate all collected links
2. Add "Link Health" section to report
3. Note any broken links encountered during tasks

### Report Section

```markdown
## Link Health

### Summary
- **Links Encountered**: 32
- **Working**: 30 (94%)
- **Broken**: 2 (6%)

### Broken Links Found During Testing

| Link | Encountered During | Status |
|------|-------------------|--------|
| /products/sale | Browsing products | 404 |
| /help/faq | Looking for help | 404 |

### Impact on User Experience
The persona encountered 2 broken links during their journey,
which could cause confusion and frustration.
```

## Best Practices

1. **Check internal links first**: External sites may block or rate limit
2. **Respect robots.txt**: For external sites
3. **Handle timeouts gracefully**: Some servers are slow
4. **Track over time**: Links break gradually
5. **Fix redirects**: Replace with final destination
6. **Don't hammer servers**: Add delays between requests
