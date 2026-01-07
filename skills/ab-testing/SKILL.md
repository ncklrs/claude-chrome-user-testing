# A/B Testing Skill

Compare two URL variants with the same persona to determine which provides a better user experience. This skill guides the comparison logic and report generation.

## When to Use

- Comparing a redesign against current production
- Testing feature flag variations
- Evaluating staging vs production differences
- Comparing different UX approaches

## Core Concept

```
Multi-Persona Testing:  Same URL    + Different Personas → Compare user types
A/B Testing:            Same Persona + Different URLs    → Compare design variants
```

## Comparison Metrics

Track these metrics for each variant:

| Metric | Better When | Comparison |
|--------|-------------|------------|
| Tasks Completed | Higher | X/Y vs X/Y |
| Critical Issues | Lower | Count vs Count |
| Major Issues | Lower | Count vs Count |
| Minor Issues | Lower | Count vs Count |
| Confusion Events | Lower | Count vs Count |
| Frustration Triggers | Lower | Count vs Count |

## Winner Determination

### Per-Metric Winner

```javascript
function getMetricWinner(metricName, valueA, valueB) {
  // For issues/confusion/frustration: lower is better
  if (['criticalIssues', 'majorIssues', 'minorIssues',
       'confusionEvents', 'frustrationTriggers'].includes(metricName)) {
    if (valueA < valueB) return 'A';
    if (valueB < valueA) return 'B';
    return 'Tie';
  }

  // For task completion: higher is better
  if (metricName === 'tasksCompleted') {
    if (valueA > valueB) return 'A';
    if (valueB > valueA) return 'B';
    return 'Tie';
  }

  return 'Tie';
}
```

### Overall Winner

```javascript
function getOverallWinner(results) {
  let aWins = 0;
  let bWins = 0;

  for (const metric of results.metrics) {
    if (metric.winner === 'A') aWins++;
    if (metric.winner === 'B') bWins++;
  }

  if (aWins > bWins) return 'A';
  if (bWins > aWins) return 'B';

  // Tiebreaker: task completion
  const taskA = results.variantA.tasksCompleted;
  const taskB = results.variantB.tasksCompleted;
  if (taskA > taskB) return 'A';
  if (taskB > taskA) return 'B';

  return 'Tie';
}
```

## Issue Categorization

### Variant-Specific Issues

Issues found only in one variant:

```markdown
### Variant A Only Issues
- Navigation menu hidden on mobile
- Form validation error messages unclear

### Variant B Only Issues
- New feature tooltip blocks content
- Loading spinner too subtle
```

### Common Issues

Issues found in both variants (baseline problems):

```markdown
### Common Issues (Both Variants)
- Footer links have low contrast
- No keyboard focus indicators
- Missing alt text on hero image
```

Common issues should be fixed regardless of which variant wins.

## Screenshot Strategy

Capture comparable screenshots at the same moments:

| Moment | Variant A | Variant B |
|--------|-----------|-----------|
| Landing Page | First impression | First impression |
| Navigation | Menu interaction | Menu interaction |
| Form Start | Beginning task | Beginning task |
| Confusion | If/when confused | If/when confused |
| Error | If error occurs | If error occurs |
| Task Complete | Success state | Success state |

### Screenshot Naming Convention

```
ab-test-{persona}-variant-{a|b}-{moment}-{timestamp}.png

Examples:
ab-test-genz-digital-native-variant-a-landing-143022.png
ab-test-genz-digital-native-variant-b-landing-143024.png
ab-test-genz-digital-native-variant-a-confusion-143156.png
```

## Multi-Persona A/B Testing

When testing multiple personas on both variants:

### Execution Order

```
Option 1: Persona-first (recommended)
For each persona:
  Test Variant A
  Test Variant B
  Record preference

Option 2: Variant-first
Test all personas on Variant A
Test all personas on Variant B
Compare results
```

Persona-first is recommended because it keeps each persona's experience fresh and comparable.

### Consensus Calculation

```javascript
function getConsensus(personaResults) {
  let aPrefs = 0;
  let bPrefs = 0;
  let ties = 0;

  for (const result of personaResults) {
    if (result.prefers === 'A') aPrefs++;
    else if (result.prefers === 'B') bPrefs++;
    else ties++;
  }

  const total = personaResults.length;

  if (aPrefs > bPrefs) {
    return `Variant A preferred by ${aPrefs}/${total} personas`;
  } else if (bPrefs > aPrefs) {
    return `Variant B preferred by ${bPrefs}/${total} personas`;
  } else {
    return `Split decision: ${aPrefs} prefer A, ${bPrefs} prefer B, ${ties} tied`;
  }
}
```

## Narration Guidelines

### Starting A/B Test

```
"I'm going to compare two versions of this site. Let me start with Variant A..."
```

### Transitioning Between Variants

```
"Okay, I've finished testing Variant A. Now let me try Variant B with fresh eyes..."
```

### Noting Differences

```
"Interesting - this is different from Variant A. The navigation is much clearer here."
"Hmm, I preferred how Variant A handled this form. This version feels cluttered."
```

### Concluding Comparison

```
"After testing both versions, Variant B was definitely easier to use.
The clearer navigation and faster load times made a big difference."
```

## Quiet Mode Output

When `--quiet` is active:

```
A/B Test: [persona-id]
Variant A: [url-a]
Variant B: [url-b]

Testing Variant A...
[Screenshot: variant-a-landing.png]
[Screenshot: variant-a-complete.png]

Testing Variant B...
[Screenshot: variant-b-landing.png]
[Screenshot: variant-b-complete.png]

# Results

| Metric | A | B | Winner |
|--------|---|---|--------|
| Tasks | 2/3 | 3/3 | B |
| Critical | 1 | 0 | B |
| Major | 2 | 1 | B |

**Winner: Variant B** (3 metric wins vs 0)

Key differences:
- B: Clearer call-to-action buttons
- B: Faster page load
- A: Form validation broke on submit
```

## Session Recording

When recording A/B tests, create separate trace files:

```
recordings/
├── ab-test-genz-digital-native-variant-a-2025-01-06-143022.zip
├── ab-test-genz-digital-native-variant-b-2025-01-06-143156.zip
```

This allows reviewing each variant's test independently at trace.playwright.dev.

## Report Templates

### Single Persona A/B Report

```markdown
# A/B Testing Comparison Report

## Test Configuration
- **Persona**: {{PERSONA_NAME}} ({{PERSONA_ID}})
- **Variant A (Control)**: {{URL_A}}
- **Variant B (Test)**: {{URL_B}}
- **Tasks**: {{TASKS}}
- **Date**: {{DATE}}

## Results Summary

| Metric | Variant A | Variant B | Winner |
|--------|-----------|-----------|--------|
| Tasks Completed | {{A_TASKS}} | {{B_TASKS}} | {{TASKS_WINNER}} |
| Critical Issues | {{A_CRITICAL}} | {{B_CRITICAL}} | {{CRITICAL_WINNER}} |
| Major Issues | {{A_MAJOR}} | {{B_MAJOR}} | {{MAJOR_WINNER}} |
| Minor Issues | {{A_MINOR}} | {{B_MINOR}} | {{MINOR_WINNER}} |

## Overall Winner: Variant {{WINNER}}
{{WINNER_EXPLANATION}}

## Variant A Issues
{{#A_ISSUES}}
- {{ISSUE}}
{{/A_ISSUES}}

## Variant B Issues
{{#B_ISSUES}}
- {{ISSUE}}
{{/B_ISSUES}}

## Common Issues
{{#COMMON_ISSUES}}
- {{ISSUE}}
{{/COMMON_ISSUES}}

## Recommendations
{{#RECOMMENDATIONS}}
1. {{RECOMMENDATION}}
{{/RECOMMENDATIONS}}
```

### Multi-Persona A/B Report

```markdown
# Multi-Persona A/B Comparison

## Configuration
- **Variant A**: {{URL_A}}
- **Variant B**: {{URL_B}}
- **Personas Tested**: {{PERSONA_COUNT}}

## Results Matrix

| Persona | A Tasks | B Tasks | A Issues | B Issues | Prefers |
|---------|---------|---------|----------|----------|---------|
{{#PERSONA_RESULTS}}
| {{PERSONA_ID}} | {{A_TASKS}} | {{B_TASKS}} | {{A_ISSUES}} | {{B_ISSUES}} | {{PREFERS}} |
{{/PERSONA_RESULTS}}

## Consensus: {{CONSENSUS}}

{{#PERSONA_BREAKDOWNS}}
### {{PERSONA_ID}}
- **Prefers**: {{PREFERS}}
- **Reason**: {{REASON}}
{{/PERSONA_BREAKDOWNS}}
```

## Best Practices

1. **Test Same Tasks**: Ensure identical tasks are tested on both variants
2. **Clear Browser State**: Start each variant test with fresh state
3. **Capture Comparable Screenshots**: Take screenshots at same moments
4. **Note Subjective Differences**: Some preferences can't be quantified
5. **Consider All Personas**: Different users may prefer different variants
6. **Fix Common Issues First**: Issues in both variants need fixing regardless
7. **Document Assumptions**: Note any differences in variant structure
