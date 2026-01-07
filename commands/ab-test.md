---
description: Compare two URL variants (A/B test) using persona-based user testing. Tests the same persona on both variants and generates a comparison report showing which performs better.
---

# A/B Testing Command

Compare two URL variants with the same persona to determine which version provides a better user experience. Useful for testing redesigns, feature flags, staging vs production, or any design variations.

## Arguments

Parse the following from `$ARGUMENTS`:

- `--url-a <url>` (required): Variant A URL (typically control/current version)
- `--url-b <url>` (required): Variant B URL (typically test/new version)
- `--persona <id>` (required unless --personas used): Single persona to test with
- `--personas <ids>` (optional): Comma-separated persona IDs for multi-persona A/B testing
- `--tasks <tasks>` (optional): Comma-separated tasks to perform on each variant
- `--gender <m|f|n>` (optional): Gender variant (male/female/neutral)
- `--quiet` (optional): Disable narration, show only comparison summary
- `--record` (optional): Record sessions as Playwright Traces
- `--record-path <path>` (optional): Custom output path for trace files (default: recordings/)

## Pre-Flight Checklist

Before beginning the test, verify:

1. **Chrome Integration**: Confirm Claude is running with `--chrome` flag
2. **Both URLs Accessible**: Navigate to both URLs and verify they load
3. **Persona Loading**: Load the specified persona configuration
4. **Variant Labels**: Identify Variant A as "Control" and Variant B as "Test"

## A/B Testing Flow

### Phase 1: Setup

```
"I'm going to test two versions of this site as [Persona Name].
- Variant A (Control): [URL A]
- Variant B (Test): [URL B]

Let me start with Variant A..."
```

### Phase 2: Test Variant A

1. Navigate to Variant A URL
2. Perform all specified tasks (or free exploration)
3. Collect:
   - Task completion status
   - Issues found (critical/major/minor)
   - Confusion events
   - Frustration trigger matches
   - Screenshots at key moments
4. Generate internal results for Variant A

### Phase 3: Transition

```
"Okay, I've finished testing Variant A. Now let me try Variant B and see how it compares..."
```

### Phase 4: Test Variant B

1. Navigate to Variant B URL
2. Perform the same tasks
3. Collect the same metrics
4. Generate internal results for Variant B

### Phase 5: Comparison Analysis

Compare results and determine winners:

#### Winner Logic

For each metric:
- **Issues**: Fewer is better (A wins if A < B)
- **Tasks Completed**: Higher is better (A wins if A > B)
- **Confusion Events**: Fewer is better
- **Frustration Triggers**: Fewer is better

#### Overall Winner

1. Count metric wins for each variant
2. Variant with more wins is overall winner
3. If tied, use task completion as tiebreaker
4. If still tied, declare "Tie"

### Phase 6: Generate Comparison Report

```markdown
# A/B Testing Comparison Report

## Test Configuration
- **Persona**: [Name] ([ID])
- **Variant A (Control)**: [URL A]
- **Variant B (Test)**: [URL B]
- **Tasks**: [tasks]
- **Date**: [timestamp]

## Results Summary

| Metric | Variant A | Variant B | Winner |
|--------|-----------|-----------|--------|
| Tasks Completed | X/Y | X/Y | A/B/Tie |
| Critical Issues | N | N | A/B/Tie |
| Major Issues | N | N | A/B/Tie |
| Minor Issues | N | N | A/B/Tie |
| Confusion Events | N | N | A/B/Tie |
| Frustration Triggers | N | N | A/B/Tie |

## Overall Winner: Variant [A/B]
[1-2 sentence summary explaining why this variant won]

## Detailed Comparison

### Variant A Issues
[List all issues found in Variant A]

### Variant B Issues
[List all issues found in Variant B]

### Common Issues (Both Variants)
[Issues present in both - these are baseline problems to fix regardless]

## Side-by-Side Screenshots

| Moment | Variant A | Variant B |
|--------|-----------|-----------|
| Landing Page | [screenshot] | [screenshot] |
| [Task 1] | [screenshot] | [screenshot] |
| Confusion Point | [screenshot] | [screenshot] |
| Task Complete | [screenshot] | [screenshot] |

## Persona Insights
[How this specific persona reacted differently to each variant]

## Recommendations
1. [Primary recommendation based on winner]
2. [Fix common issues in both variants]
3. [Variant-specific improvements]
```

## Multi-Persona A/B Testing

When `--personas` flag is used, test each persona on both variants:

### Execution Flow

```
For each persona in personas:
  1. Test Variant A with persona
  2. Test Variant B with persona
  3. Determine persona's preference
Aggregate all preferences for consensus
```

### Multi-Persona Report Format

```markdown
# Multi-Persona A/B Comparison Report

## Configuration
- **Variant A (Control)**: [URL A]
- **Variant B (Test)**: [URL B]
- **Tasks**: [tasks]
- **Personas Tested**: [count]

## Results Matrix

| Persona | A Tasks | B Tasks | A Issues | B Issues | Prefers |
|---------|---------|---------|----------|----------|---------|
| genz-digital-native | 3/3 | 3/3 | 2 | 1 | B |
| boomer-tech-averse | 2/3 | 3/3 | 4 | 2 | B |
| impulse-buyer | 3/3 | 3/3 | 1 | 1 | Tie |

## Consensus: Variant [A/B] preferred by [X/Y] personas

## Universal Findings

### Issues in Both Variants (All Personas)
[Issues every persona found in both variants]

### Variant A Only Issues
[Issues found only in Variant A]

### Variant B Only Issues
[Issues found only in Variant B]

## Per-Persona Breakdown

### genz-digital-native
- **Prefers**: Variant B
- **Reason**: [Why this persona preferred B]
- **Key Difference**: [What made the difference]

### boomer-tech-averse
- **Prefers**: Variant B
- **Reason**: [Why this persona preferred B]
- **Key Difference**: [What made the difference]

## Recommendations
1. [Deploy Variant B because X/Y personas preferred it]
2. [Address common issues before deployment]
3. [Consider persona-specific improvements]
```

## Quiet Mode

When `--quiet` flag is used:

```
Testing Variant A (https://example.com)...
[Screenshot: a-landing.png]
[Screenshot: a-task-complete.png]

Testing Variant B (https://staging.example.com)...
[Screenshot: b-landing.png]
[Screenshot: b-task-complete.png]

# A/B Comparison Summary

**Winner: Variant B**

| Metric | A | B | Winner |
|--------|---|---|--------|
| Tasks | 2/3 | 3/3 | B |
| Critical | 1 | 0 | B |
| Major | 2 | 1 | B |
| Minor | 1 | 1 | Tie |

Variant B wins with fewer issues and higher task completion.

## Key Differences
- Variant B: Clearer navigation reduced confusion
- Variant B: Faster load time improved patience
- Variant A: Form had validation issues
```

## Session Recording

When `--record` flag is used, create separate traces for each variant:

```
recordings/
├── ab-test-genz-digital-native-variant-a-2025-01-06-143022.zip
├── ab-test-genz-digital-native-variant-b-2025-01-06-143156.zip
```

## Example Usage

### Basic A/B Test
```
/ab-test --url-a https://example.com --url-b https://staging.example.com --persona genz-digital-native --tasks "sign up, browse products"
```

### Compare Redesign
```
/ab-test --url-a https://shop.example.com --url-b https://shop-redesign.example.com --persona boomer-tech-averse --tasks "find product, add to cart, checkout"
```

### Multi-Persona Comparison
```
/ab-test --url-a https://prod.example.com --url-b https://canary.example.com --personas "genz-digital-native,boomer-tech-averse,impulse-buyer" --tasks "complete purchase"
```

### CI/CD Integration
```
/ab-test --url-a https://example.com --url-b https://staging.example.com --persona genz-digital-native --quiet --tasks "sign up"
```

### With Recording
```
/ab-test --url-a https://example.com --url-b https://beta.example.com --persona comparison-shopper --record --tasks "compare products"
```

## Error Handling

### URL Load Failure
```
"I couldn't load [Variant A/B] at [URL].
Error: [error message]

Please verify the URL is accessible and try again."
```

### Different Site Structures
```
"Note: The two variants have significantly different page structures.
This may affect direct comparison of task completion.
I'll do my best to complete the same tasks on both."
```

### Timeout on One Variant
```
"Variant [A/B] timed out during [task].
This is a significant usability issue - real users would likely abandon.

Marking this as a critical issue for Variant [A/B]."
```

## Best Practices

1. **Same Tasks**: Always test identical tasks on both variants for fair comparison
2. **Fresh State**: Each variant test should start from a clean state (no cookies/cache from the other)
3. **Multiple Personas**: Use `--personas` for comprehensive testing across user types
4. **Record Sessions**: Use `--record` to debug unexpected results
5. **Meaningful Differences**: A/B test when variants have meaningful differences to compare
