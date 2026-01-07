# Ralph Loop Integration

Use Ralph Wiggum's autonomous loop to run continuous, exploratory user testing sessions.

## What is Ralph Loop?

Ralph Wiggum is an autonomous testing technique where Claude continuously explores and tests your application without explicit task lists. When combined with user-testing personas, Ralph embodies the persona's behaviors and discovers issues organically.

## Quick Start

```bash
# Start Chrome session
claude --chrome

# Start Ralph loop with a persona
/ralph-wiggum:ralph-loop

# Then run user-test without --tasks
/user-test --url https://example.com --persona genz-digital-native
```

## How It Works

1. **Ralph Loop Active**: The `/ralph-wiggum:ralph-loop` command puts Claude into autonomous exploration mode
2. **Persona Embodiment**: `/user-test` loads the persona's behaviors, timing, and frustration triggers
3. **Organic Discovery**: Instead of following a task list, Ralph explores naturally as the persona would
4. **Continuous Testing**: Ralph keeps testing until stopped or a critical issue is found

## Usage Patterns

### Exploratory Testing

Let Ralph discover what a persona would naturally do:

```bash
/ralph-wiggum:ralph-loop
/user-test --url https://shop.example.com --persona impulse-buyer
```

Ralph as an impulse buyer will:
- Look for quick purchase options
- Get frustrated by long forms
- Abandon if checkout takes too long
- Find "buy now" buttons

### Multi-Persona Exploration

Run sequential persona sessions:

```bash
/ralph-wiggum:ralph-loop

# First persona
/user-test --url https://example.com --persona boomer-tech-averse
# Ralph explores as a cautious older user...

# Switch persona (same loop)
/user-test --url https://example.com --persona genz-digital-native
# Ralph now explores as an impatient Gen Z user...
```

### Accessibility Discovery

```bash
/ralph-wiggum:ralph-loop
/user-test --url https://example.com --persona screen-reader-user
```

Ralph navigates using screen reader patterns:
- Heading navigation
- Landmark jumps
- Tab order exploration
- ARIA label reading

### Security Fuzzing Loop

```bash
/ralph-wiggum:ralph-loop
/user-test --url https://example.com/signup --persona developer-critic --fuzz
```

Ralph continuously fuzzes forms while narrating like a security-minded developer.

## Commands Reference

| Command | Purpose |
|---------|---------|
| `/ralph-wiggum:ralph-loop` | Start autonomous loop |
| `/ralph-wiggum:cancel-ralph` | Stop the loop |
| `/ralph-wiggum:help` | Show Ralph Wiggum help |

## Best Practices

### DO

- **Start focused**: Give Ralph a specific URL to start from
- **Use appropriate personas**: Match persona to what you're testing
- **Monitor early sessions**: Watch Ralph's first explorations to calibrate
- **Combine with recording**: Use `--record` to capture what Ralph finds

### DON'T

- **Don't use with --tasks**: Let Ralph discover tasks naturally
- **Don't use --quiet**: Ralph's narration shows what the persona notices
- **Don't run unattended initially**: Watch first to ensure expected behavior

## Example Session

```
> /ralph-wiggum:ralph-loop
Ralph loop activated. Claude will autonomously explore and test.

> /user-test --url https://shop.example.com --persona boomer-tech-averse

[Harold explores the homepage]
"Let me see... there's a lot going on here. Where do I click to find products?"

[Notices navigation]
"Ah, there's a menu. Let me hover... Shop, About, Contact. Okay, Shop sounds right."

[Clicks Shop]
"Oh my, so many categories. Electronics, Clothing, Home... I wanted headphones.
Is that Electronics? Let me read carefully..."

[Finds issue]
"Wait, where did that popup come from? I didn't want to sign up for anything.
How do I close this? There's no X... very frustrating."

ISSUE FOUND: Modal has no visible close button for users who don't know ESC key
```

## Stopping Ralph

```bash
# Graceful stop
/ralph-wiggum:cancel-ralph

# Or just start a new conversation
/clear
```

## Combining with Other Features

### With Recording

```bash
/ralph-wiggum:ralph-loop
/user-test --url https://example.com --persona cognitive-adhd-user --record
```

Captures Ralph's entire exploration for later review.

### With Network Throttling

```bash
/ralph-wiggum:ralph-loop
/user-test --url https://example.com --persona bad-connection-user --network slow-3g
```

Ralph experiences the site as someone with poor connectivity would.

### With Viewport Testing

```bash
/ralph-wiggum:ralph-loop
/user-test --url https://example.com --persona genz-digital-native --viewport mobile
```

Ralph explores the mobile experience as a Gen Z user.

## When to Use Ralph Loop

| Scenario | Ralph Loop? | Why |
|----------|-------------|-----|
| Known user journey | No | Use `--tasks` instead |
| Exploratory testing | Yes | Discover unknown issues |
| Regression testing | No | Use `/critical-path` |
| New feature review | Yes | Fresh perspective |
| Accessibility audit | Maybe | Use `/wcag-audit` for compliance, Ralph for UX |
| Security testing | Yes | Ralph + `--fuzz` finds edge cases |

## Troubleshooting

### Ralph stops exploring

Ralph may pause when encountering:
- Login walls (provide test credentials)
- CAPTCHAs (skip or use test mode)
- Payment forms (use `--stripe` with test cards)

### Ralph gets stuck in a loop

If Ralph keeps clicking the same things:
```bash
/ralph-wiggum:cancel-ralph
```

Then restart with a different starting URL or persona.

### Ralph misses obvious issues

Some personas are more thorough than others:
- `boomer-tech-averse`: Reads everything, slow but thorough
- `genz-digital-native`: Fast, may skip details
- `developer-critic`: Technical issues, may miss UX
- `designer-critic`: Visual issues, deep UI analysis
