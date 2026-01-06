---
name: user-testing
description: |
  Comprehensive persona-based user testing skill for web applications. Simulates how real users from different demographics interact with interfaces, including realistic timing, behavioral patterns, and frustration triggers.

  Use when:
  - Testing user interfaces before release
  - Validating UX flows from diverse perspectives
  - Conducting accessibility reviews
  - Optimizing onboarding or checkout experiences
  - Getting feedback on form design
---

# User Testing Skill

## Overview

This skill enables persona-based user testing that simulates how real users interact with web applications. It combines behavioral psychology, UX research methodologies, and browser automation to provide authentic testing experiences.

## When to Use This Skill

- **Pre-release testing**: Test new features before they ship
- **UX validation**: Verify that flows work for different user types
- **Accessibility review**: Check clarity and usability across skill levels
- **Form optimization**: Test form completion rates and friction points
- **Onboarding analysis**: Evaluate first-time user experience
- **Checkout testing**: Identify cart abandonment risks

## Persona System

### Available Personas

| ID | Generation | Tech Comfort | Patience | Key Trait |
|----|------------|--------------|----------|-----------|
| `boomer-tech-averse` | Boomer | 2/10 | 7/10 | Needs explicit guidance |
| `boomer-tech-friendly` | Boomer | 6/10 | 8/10 | Prefers familiar patterns |
| `millennial-tech-skeptic` | Millennial | 7/10 | 5/10 | Skeptical of manipulation |
| `genz-digital-native` | Gen Z | 9/10 | 3/10 | Expects instant results |
| `genalpha-tablet-kid` | Gen Alpha | 8/10 | 2/10 | Touch-first, visual |

### Gender Variants

Each persona has three gender variants with different names and subtle trait adjustments:
- **Male**: Slightly different communication style
- **Female**: Slightly different priorities and observations
- **Neutral**: Balanced default variant

### Persona Structure

Personas are defined in JSON files with this schema:

```json
{
  "id": "persona-id",
  "name": "Display Name",
  "generation": "boomer|millennial|genz|genalpha",
  "techComfort": 1-10,
  "patience": 1-10,
  "genderVariants": {
    "male": { "name": "...", "pronouns": "he/him", "traits": [...] },
    "female": { "name": "...", "pronouns": "she/her", "traits": [...] },
    "neutral": { "name": "...", "pronouns": "they/them", "traits": [...] }
  },
  "traits": {
    "readingSpeed": "slow|medium|fast",
    "clickConfidence": "hesitant|normal|decisive",
    "errorRecovery": "confused|methodical|adaptive",
    "scrollBehavior": "careful|normal|impatient",
    "formFilling": "cautious|normal|rushed"
  },
  "timing": {
    "baseReadingWPM": 100-400,
    "clickDelay": 300-3000,
    "hoverBeforeClick": 200-1500,
    "confusionPause": 5000-30000,
    "successPause": 300-2000,
    "pageLoadTolerance": 2000-10000
  },
  "narrationStyle": {
    "vocabulary": "simple|moderate|technical|casual",
    "frustrationThreshold": 1-10,
    "verbosity": "quiet|normal|chatty",
    "expressions": ["...", "..."]
  },
  "frustrationTriggers": ["...", "..."],
  "positiveReactions": ["...", "..."],
  "typicalBehaviors": ["...", "..."]
}
```

## Timing System

### Reading Time Calculation

```
base_time = (word_count / persona.timing.baseReadingWPM) * 60 * 1000  // ms
adjusted_time = base_time * complexity_modifier
final_time = adjusted_time * random(0.8, 1.2)  // Add variance
```

**Complexity Modifiers:**
- Simple text: 1.0x
- Technical jargon: 1.5x (varies by persona)
- Legal/privacy text: 2.0x for skeptics, 0.5x for impatient users

### Interaction Delays

| Action | Tech-Averse | Tech-Friendly | Tech-Skeptic | Digital Native | Tablet Kid |
|--------|-------------|---------------|--------------|----------------|------------|
| Click | 2000-3000ms | 800-1200ms | 600-1000ms | 200-400ms | 300-600ms |
| Form field | 1500ms | 800ms | 600ms | 200ms | 400ms |
| Decision | 5000ms | 2000ms | 3000ms | 500ms | 1000ms |
| Confusion | 20000ms | 10000ms | 8000ms | 5000ms | 3000ms |

### Confusion Simulation

When a persona encounters confusing UI:

1. **Initial Pause**: Extended delay (5-30 seconds based on patience)
2. **Visual Scanning**: Look around the page for clues
3. **Verbal Expression**: Narrate confusion in character
4. **Recovery Attempt**: Try alternative approaches
5. **Potential Abandonment**: If frustration threshold exceeded

## Report Format

### Real-Time Narration

During testing, output continuous first-person narration:

```
[10:32:15] [Gen Z - Jayden] [NAVIGATE]
"Okay let's see what this is about..."

[10:32:18] [Gen Z - Jayden] [SCAN]
"Lot of text... skip... skip... where's the sign up button?"

[10:32:20] [Gen Z - Jayden] [CLICK]
"There it is. Let's go."

[10:32:25] [Gen Z - Jayden] [FRUSTRATION]
"Bruh, why do they need my phone number?? This is sus."
```

### Summary Report Structure

```markdown
# User Testing Report

## Session Overview
- **Persona**: [Name] ([ID])
- **Gender Variant**: [Male/Female/Neutral]
- **URL Tested**: [URL]
- **Tasks Attempted**: [List]
- **Overall Success**: [X/Y tasks completed]

## Executive Summary
[1-2 paragraph overview]

## Task Analysis

### Task 1: [Description]
- **Status**: Completed / Partial / Failed
- **Time**: [Duration]
- **Confusion Points**:
  - [Timestamp]: [What caused confusion]
- **Screenshots**: [Links]

## Usability Issues

### Critical (Task Blockers)
1. **[Issue]**
   - Personas affected: [List]
   - Evidence: [Screenshot/description]
   - Recommendation: [Fix]

### Major (High Frustration)
...

### Minor (Suboptimal)
...

## Persona-Specific Insights
[Observations unique to this persona's worldview]

## Prioritized Recommendations
| Priority | Issue | Recommendation | Affected Personas |
|----------|-------|----------------|-------------------|
| P0 | ... | ... | All |
| P1 | ... | ... | Tech-averse |
```

## Usage Examples

### Basic Test
```
/user-test --url https://example.com --persona genz-digital-native
```

### With Specific Tasks
```
/user-test --url https://shop.example.com --persona boomer-tech-averse --tasks "find product, add to cart, checkout"
```

### With Gender Variant
```
/user-test --url https://app.example.com --persona millennial-tech-skeptic --gender f
```

### Verbose Mode
```
/user-test --url https://example.com --persona genalpha-tablet-kid --verbose
```

## Best Practices

### For Accurate Testing

1. **Test Multiple Personas**: Different users find different issues
2. **Include Edge Cases**: Tech-averse users reveal clarity issues
3. **Document Everything**: Screenshots capture evidence
4. **Compare Results**: Cross-persona patterns reveal universal issues

### For Actionable Results

1. **Prioritize by Severity**: Critical > Major > Minor
2. **Note Affected Personas**: Some issues only affect certain users
3. **Provide Recommendations**: Don't just report problems
4. **Include Evidence**: Screenshots make issues undeniable

## Integration with Chrome

This skill requires `claude --chrome` for browser automation:

```bash
# Start Claude with Chrome integration
claude --chrome

# Then run user tests
/user-test --url https://your-app.com --persona genz-digital-native
```

The skill uses Playwright MCP tools:
- `browser_navigate` - Page navigation
- `browser_snapshot` - Accessibility tree analysis
- `browser_click` - Element interaction
- `browser_type` - Text input
- `browser_take_screenshot` - Evidence capture
