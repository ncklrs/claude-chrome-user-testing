# GitHub PR Comments Skill

Post user testing results as GitHub PR comments for CI/CD integration.

## When to Use

- Running tests in CI/CD pipelines
- Automated testing on PR preview deployments
- Sharing test results with reviewers
- Tracking UX regressions across PRs

## Prerequisites

- `gh` CLI installed and authenticated
- In GitHub Actions: `GITHUB_TOKEN` is automatically available
- PR write permissions (`pull-requests: write`)

## Flag

```
--pr <number>     Post results as comment on PR #<number>
```

## Comment Format

### Structure

```markdown
<!-- user-test-results:[persona-id] -->
## User Test Results: [Persona Name]

**Status:** [badge] | **Tested:** [timestamp]

| Metric | Value |
|--------|-------|
| URL | `https://...` |
| Persona | [name] ([id]) |
| Viewport | [viewport] |
| Network | [network] |
| Duration | [duration]s |

### Summary
- Tasks: X/Y completed
- Critical Issues: N
- Major Issues: N
- Minor Issues: N

<details>
<summary>Issues Found (N)</summary>

#### Critical
...

#### Major
...

#### Minor
...

</details>

<details>
<summary>Screenshots</summary>

[Links to screenshots/trace if --record used]

</details>

---
*Tested with [user-testing-agent](https://github.com/ncklrs/claude-chrome-user-testing)*
```

### Status Badges

| Status | Badge | When |
|--------|-------|------|
| Pass | `PASS` | All tasks completed, no critical issues |
| Fail | `FAIL` | Critical issues found or tasks failed |
| Issues | `ISSUES` | Tasks completed but major/minor issues exist |

## Posting Logic

### Simple Post

```bash
gh pr comment $PR_NUMBER --body "$COMMENT_BODY"
```

### Update Existing (Recommended)

Prevents comment spam on re-runs by updating existing comment:

```bash
# Marker includes persona ID to allow multiple persona comments
MARKER="<!-- user-test-results:$PERSONA_ID -->"

# Check for existing comment with this marker
EXISTING=$(gh pr view $PR_NUMBER --json comments \
  --jq ".comments[] | select(.body | contains(\"$MARKER\")) | .databaseId" \
  | head -1)

if [ -n "$EXISTING" ]; then
  # Update existing comment
  gh api repos/{owner}/{repo}/issues/comments/$EXISTING \
    -X PATCH \
    -f body="$COMMENT_BODY"
  echo "Updated existing comment"
else
  # Create new comment
  gh pr comment $PR_NUMBER --body "$COMMENT_BODY"
  echo "Created new comment"
fi
```

### Multi-Persona Support

When running `--personas` with `--pr`:
- Each persona gets its own comment (separate markers)
- Or: Single combined comment with all results

## GitHub Actions Integration

### Basic Workflow

```yaml
name: User Testing
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  user-test:
    runs-on: ubuntu-latest
    permissions:
      pull-requests: write
    steps:
      - uses: actions/checkout@v4

      - name: Deploy Preview
        id: deploy
        run: |
          # Your preview deployment logic
          echo "url=https://preview-${{ github.event.pull_request.number }}.example.com" >> $GITHUB_OUTPUT

      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code

      - name: Run User Tests
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude --chrome "/user-test \
            --url ${{ steps.deploy.outputs.url }} \
            --persona genz-digital-native \
            --quiet \
            --pr ${{ github.event.pull_request.number }}"
```

### With Recording

```yaml
      - name: Run User Tests with Recording
        run: |
          claude --chrome "/user-test \
            --url ${{ steps.deploy.outputs.url }} \
            --persona boomer-tech-averse \
            --quiet \
            --record \
            --pr ${{ github.event.pull_request.number }}"

      - name: Upload Trace
        uses: actions/upload-artifact@v4
        with:
          name: playwright-trace
          path: "*.trace.zip"
```

### Multi-Persona Testing

```yaml
      - name: Run Multi-Persona Tests
        run: |
          claude --chrome "/user-test \
            --url ${{ steps.deploy.outputs.url }} \
            --personas 'boomer-tech-averse,genz-digital-native,screen-reader-user' \
            --quiet \
            --pr ${{ github.event.pull_request.number }}"
```

## Error Handling

### gh CLI Not Found

```
Error: gh CLI not installed
Skipping PR comment (results still output to stdout)
```

### Authentication Failed

```
Error: gh CLI not authenticated
Run 'gh auth login' or ensure GITHUB_TOKEN is set
Skipping PR comment (results still output to stdout)
```

### PR Not Found

```
Error: PR #123 not found
Verify PR number and repository access
Skipping PR comment (results still output to stdout)
```

## Best Practices

1. **Always use `--quiet`**: Reduces noise, focuses on results
2. **Combine with `--record`**: Provides trace files for debugging
3. **Use specific personas**: Match persona to PR type (accessibility PR = screen-reader-user)
4. **Update vs spam**: The marker system prevents duplicate comments
5. **Include viewport/network**: Mobile PRs should test mobile viewport

## Limitations

1. **No inline images**: GitHub doesn't support image uploads via API - link to artifacts instead
2. **Comment size limit**: ~65KB max - use collapsible sections
3. **Rate limits**: 5000 requests/hour with token
4. **Requires gh CLI**: Standard in GitHub Actions, may need install elsewhere
