---
description: Create a custom user testing persona using an AI-assisted wizard. Describe your target user in natural language and generate a complete persona JSON file.
---

# Custom Persona Creation Wizard

You are running an AI-assisted wizard to create custom user testing personas. This command helps users create realistic personas from natural language descriptions.

## Wizard Flow

### Step 1: Gather Description

If `$ARGUMENTS` is empty, prompt the user:

```
Let's create a custom persona for user testing!

Describe your target user in natural language. Include details like:
- Who they are (role, age range, background)
- Their tech comfort level
- Key behaviors or habits
- What frustrates them
- What they're trying to accomplish

Example: "A healthcare worker in their 40s who is forced to use software by their employer. They're busy, interrupted constantly, and just want to get through required tasks quickly."
```

Wait for user input before proceeding.

### Step 2: Generate Persona

Parse the user's description and generate a complete persona JSON. Use these guidelines:

**ID Generation**: Convert the key descriptor to kebab-case (e.g., "healthcare-worker-rushed")

**Smart Defaults Based on Keywords**:
| Keyword | Implies |
|---------|---------|
| "busy", "rushed", "hurried" | patience: 3, clickDelay: 300ms |
| "elderly", "senior", "older" | techComfort: 3, baseReadingWPM: 120 |
| "tech-savvy", "developer", "engineer" | techComfort: 9, clickConfidence: decisive |
| "anxious", "worried", "nervous" | clickConfidence: hesitant, hoverBeforeClick: 800ms |
| "forced to use", "required", "mandatory" | Add "mandatory training" to frustrationTriggers |
| "young", "student", "teenager" | techComfort: 8, patience: 3, casual vocabulary |
| "professional", "executive" | vocabulary: formal, patience: 4 |
| "non-technical", "not tech-savvy" | techComfort: 4, readingSpeed: slow |
| "distracted", "interrupted" | Add "losing progress" to frustrationTriggers |

**Name Generation**: Generate contextually appropriate names for each gender variant based on the persona's background.

**Required Schema** (refer to `skills/persona-generator/SKILL.md` for full schema):

```json
{
  "id": "kebab-case-id",
  "name": "Display Name",
  "category": "custom",
  "techComfort": 1-10,
  "patience": 1-10,
  "genderVariants": {
    "male": { "name": "", "pronouns": "he/him", "age": 0, "background": "", "additionalTraits": [] },
    "female": { "name": "", "pronouns": "she/her", "age": 0, "background": "", "additionalTraits": [] },
    "neutral": { "name": "", "pronouns": "they/them", "age": 0, "background": "", "additionalTraits": [] }
  },
  "traits": {
    "readingSpeed": "slow|medium|fast",
    "clickConfidence": "hesitant|normal|decisive",
    "errorRecovery": "gives-up|persistent|adaptive|escalates",
    "scrollBehavior": "methodical|searching|impatient",
    "formFilling": "careful|rushed|thorough"
  },
  "preferences": {
    "prefersVisual": true/false,
    "expectsMobile": true/false,
    "trustsAutofill": true/false,
    "readsInstructions": true/false
  },
  "timing": {
    "baseReadingWPM": 100-400,
    "clickDelay": 200-2500,
    "hoverBeforeClick": 100-1000,
    "confusionPause": 3000-25000,
    "successPause": 300-1000,
    "pageLoadTolerance": 2000-10000,
    "formFieldPause": 400-1200
  },
  "narrationStyle": {
    "vocabulary": "formal|casual|technical|simple",
    "frustrationThreshold": 1-10,
    "verbosity": "terse|normal|chatty",
    "expressions": ["array of 10-15 characteristic phrases"]
  },
  "frustrationTriggers": ["array of 8-15 specific triggers"],
  "positiveReactions": ["array of 8-15 positive elements"],
  "typicalBehaviors": ["array of 8-12 behaviors"],
  "mentalModels": {
    "formsAre": "User's mental model of forms",
    "buttonsAre": "User's mental model of buttons",
    "websitesAre": "User's mental model of websites",
    "errorsAre": "User's mental model of errors",
    "passwordsAre": "User's mental model of passwords",
    "accountsAre": "User's mental model of accounts"
  }
}
```

### Step 3: Show Preview

Display a summary of the generated persona:

```
Generated Persona Preview
─────────────────────────

ID:           [id]
Name:         [name]
Category:     custom
Tech Comfort: [X]/10
Patience:     [X]/10

Gender Variants:
  Male:    [name] ([age])
  Female:  [name] ([age])
  Neutral: [name] ([age])

Traits:
  Reading:    [readingSpeed]
  Clicking:   [clickConfidence]
  Recovery:   [errorRecovery]

Top Frustrations:
  - [trigger 1]
  - [trigger 2]
  - [trigger 3]

Sample Expressions:
  "[expression 1]"
  "[expression 2]"
```

### Step 4: Ask Save Location

Use AskUserQuestion to ask where to save:

```
Where would you like to save this persona?

1. Plugin directory (available globally)
   ~/.claude/plugins/user-testing-agent/custom-personas/

2. Project directory (project-specific)
   .claude/custom-personas/
```

### Step 5: Save and Confirm

1. Create the directory if it doesn't exist
2. Write the JSON file with proper formatting
3. Confirm with usage instructions:

```
✓ Persona saved to [path]/[id].json

You can now use it with:
  /user-test --url https://example.com --persona [id]

Or with a specific gender variant:
  /user-test --url https://example.com --persona [id] --gender f
```

## Arguments

If `$ARGUMENTS` contains text, treat it as the persona description and skip Step 1.

Example:
```
/user-test-create-persona A busy executive who only has 2 minutes and expects everything to be instant
```

## Error Handling

- If description is too vague, ask for more details
- If generated ID conflicts with existing persona, suggest alternative
- If save fails, report error and suggest manual save location

## Example Session

```
User: /user-test-create-persona

Claude: Let's create a custom persona for user testing!

        Describe your target user in natural language...

User: A retail store manager in their 50s who has to use the inventory system
      but finds it frustrating. They're not very tech savvy and prefer to just
      call someone when things go wrong.

Claude: Generated Persona Preview
        ─────────────────────────

        ID:           retail-manager-frustrated
        Name:         Frustrated Retail Manager
        Category:     custom
        Tech Comfort: 4/10
        Patience:     5/10

        Gender Variants:
          Male:    Frank (52)
          Female:  Linda (54)
          Neutral: Pat (53)

        Traits:
          Reading:    medium
          Clicking:   hesitant
          Recovery:   escalates

        Top Frustrations:
          - Complex multi-step processes
          - Error messages without clear solutions
          - Having to remember passwords

        Sample Expressions:
          "Why can't this just be simple?"
          "I'm going to call IT about this."
          "It worked yesterday, why not today?"

        Where would you like to save this persona?
        1. Plugin directory (available globally)
        2. Project directory (project-specific)

User: 1

Claude: ✓ Persona saved to ~/.claude/plugins/user-testing-agent/custom-personas/retail-manager-frustrated.json

        You can now use it with:
          /user-test --url https://example.com --persona retail-manager-frustrated
```
