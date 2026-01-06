# Persona Generator Skill

This skill provides the schema and generation logic for creating custom user testing personas.

## Purpose

Generate complete, realistic user personas from natural language descriptions. The generated personas are compatible with the `/user-test` command and follow the same schema as built-in personas.

## Complete Persona Schema

```json
{
  "id": "string",
  "name": "string",
  "category": "custom",
  "techComfort": "number (1-10)",
  "patience": "number (1-10)",

  "genderVariants": {
    "male": {
      "name": "string",
      "pronouns": "he/him",
      "age": "number",
      "background": "string (2-3 sentences)",
      "additionalTraits": ["string array (2-4 items)"]
    },
    "female": {
      "name": "string",
      "pronouns": "she/her",
      "age": "number",
      "background": "string (2-3 sentences)",
      "additionalTraits": ["string array (2-4 items)"]
    },
    "neutral": {
      "name": "string",
      "pronouns": "they/them",
      "age": "number",
      "background": "string (2-3 sentences)",
      "additionalTraits": ["string array (2-4 items)"]
    }
  },

  "traits": {
    "readingSpeed": "slow | medium | fast",
    "clickConfidence": "hesitant | normal | decisive",
    "errorRecovery": "gives-up | persistent | adaptive | escalates",
    "scrollBehavior": "methodical | searching | impatient",
    "formFilling": "careful | rushed | thorough"
  },

  "preferences": {
    "prefersVisual": "boolean",
    "expectsMobile": "boolean",
    "trustsAutofill": "boolean",
    "readsInstructions": "boolean"
  },

  "timing": {
    "baseReadingWPM": "number (100-400)",
    "clickDelay": "number (200-2500 ms)",
    "hoverBeforeClick": "number (100-1000 ms)",
    "confusionPause": "number (3000-25000 ms)",
    "successPause": "number (300-1000 ms)",
    "pageLoadTolerance": "number (2000-10000 ms)",
    "formFieldPause": "number (400-1200 ms)"
  },

  "narrationStyle": {
    "vocabulary": "formal | casual | technical | simple",
    "frustrationThreshold": "number (1-10)",
    "verbosity": "terse | normal | chatty",
    "expressions": ["string array (10-15 characteristic phrases)"]
  },

  "frustrationTriggers": ["string array (8-15 specific triggers)"],
  "positiveReactions": ["string array (8-15 positive elements)"],
  "typicalBehaviors": ["string array (8-12 typical behaviors)"],

  "mentalModels": {
    "formsAre": "string (how user perceives forms)",
    "buttonsAre": "string (how user perceives buttons)",
    "websitesAre": "string (how user perceives websites)",
    "errorsAre": "string (how user perceives errors)",
    "passwordsAre": "string (how user perceives passwords)",
    "accountsAre": "string (how user perceives accounts)"
  }
}
```

## Trait Correlations

Use these correlations when generating personas to ensure consistency:

### Tech Comfort Level

| Level | Reading | Clicking | Recovery | Timing |
|-------|---------|----------|----------|--------|
| 1-3 (Low) | slow | hesitant | gives-up/escalates | WPM: 100-150, clickDelay: 1500-2500 |
| 4-6 (Medium) | medium | normal | persistent | WPM: 150-250, clickDelay: 600-1500 |
| 7-10 (High) | fast | decisive | adaptive | WPM: 250-400, clickDelay: 200-600 |

### Patience Level

| Level | Confusion Pause | Page Load Tolerance | Frustration Threshold |
|-------|-----------------|---------------------|----------------------|
| 1-3 (Low) | 3000-5000ms | 2000-3000ms | 3-4 |
| 4-6 (Medium) | 8000-12000ms | 5000-7000ms | 5-6 |
| 7-10 (High) | 15000-25000ms | 8000-10000ms | 7-9 |

## Name Generation Guidelines

Generate names appropriate to the persona's background:

- **Professional/Corporate**: Traditional names (Michael, Jennifer, David, Sarah)
- **Healthcare**: Diverse professional names (Marcus, Priya, Elena, James)
- **Retail/Service**: Common working-class names (Frank, Linda, Mike, Teresa)
- **Student/Young**: Modern names (Jayden, Zara, Alex, Madison)
- **Senior/Elderly**: Classic names (Harold, Dorothy, Richard, Barbara)
- **Technical**: Mix of traditional and international (Raj, Sarah, Wei, Brandon)

Neutral names should be genuinely gender-neutral: Jordan, Alex, Taylor, Casey, Riley, Morgan, Quinn, Avery.

## Expression Guidelines

Expressions should:
- Be in first person
- Reflect the persona's vocabulary level
- Include emotional reactions
- Mix questions, statements, and exclamations
- Include common filler phrases

### By Vocabulary Type

**Formal**:
- "I don't quite understand what this is asking."
- "Could someone please clarify this for me?"
- "This seems unnecessarily complicated."

**Casual**:
- "Okay so... where do I click?"
- "Ugh, seriously?"
- "That's kind of confusing."

**Technical**:
- "The form validation seems off here."
- "Why isn't this responsive?"
- "This should be more intuitive."

**Simple**:
- "I don't know."
- "What does this mean?"
- "Help?"

## Frustration Trigger Categories

Include triggers from multiple categories:

1. **Technical Issues**: slow loads, errors, crashes
2. **Design Problems**: confusing layout, small text, poor contrast
3. **Process Issues**: too many steps, repeated information, no progress save
4. **Trust Issues**: unclear pricing, hidden fees, spam concerns
5. **Accessibility**: hard to read, no keyboard support, poor mobile experience

## Validation Rules

Before saving a persona, validate:

1. `id` is kebab-case and unique
2. `techComfort` and `patience` are 1-10
3. All three gender variants are complete
4. Timing values are within reasonable ranges
5. At least 8 items in frustrationTriggers, positiveReactions, typicalBehaviors
6. At least 10 expressions in narrationStyle
7. All mentalModels keys are present

## Example Generated Persona

Input: "A busy nurse who has to use the hospital's patient management system between patients. She's in her 30s, tech-capable but frustrated by slow systems."

```json
{
  "id": "busy-nurse-rushed",
  "name": "Rushed Hospital Nurse",
  "category": "custom",
  "techComfort": 6,
  "patience": 3,
  "genderVariants": {
    "male": {
      "name": "Marcus",
      "pronouns": "he/him",
      "age": 34,
      "background": "ER nurse with 8 years experience. Uses the patient system dozens of times per shift between emergencies. Knows the shortcuts but gets frustrated when the system is slow.",
      "additionalTraits": ["works 12-hour shifts", "prefers efficiency over features", "will work around broken features"]
    },
    "female": {
      "name": "Elena",
      "pronouns": "she/her",
      "age": 32,
      "background": "ICU nurse who needs quick access to patient records. Has reported usability issues multiple times but nothing changes. Has developed workarounds for common problems.",
      "additionalTraits": ["advocates for better systems", "trains new staff", "knows all the bugs"]
    },
    "neutral": {
      "name": "Jordan",
      "pronouns": "they/them",
      "age": 35,
      "background": "Traveling nurse who has used many different hospital systems. Can adapt quickly but knows what good systems look like. Frustrated by obvious usability issues.",
      "additionalTraits": ["compares to other systems", "quick to identify problems", "pragmatic about limitations"]
    }
  },
  "traits": {
    "readingSpeed": "fast",
    "clickConfidence": "decisive",
    "errorRecovery": "adaptive",
    "scrollBehavior": "impatient",
    "formFilling": "rushed"
  },
  "preferences": {
    "prefersVisual": false,
    "expectsMobile": false,
    "trustsAutofill": true,
    "readsInstructions": false
  },
  "timing": {
    "baseReadingWPM": 280,
    "clickDelay": 400,
    "hoverBeforeClick": 150,
    "confusionPause": 4000,
    "successPause": 200,
    "pageLoadTolerance": 3000,
    "formFieldPause": 400
  },
  "narrationStyle": {
    "vocabulary": "casual",
    "frustrationThreshold": 4,
    "verbosity": "terse",
    "expressions": [
      "Come on, come on...",
      "I don't have time for this.",
      "Why is this so slow?",
      "Just let me in.",
      "Where's the patient info?",
      "This system, I swear.",
      "Finally.",
      "Oh great, it's loading again.",
      "I know where this is.",
      "Why do I have to click three times?",
      "My patient is waiting.",
      "Can't this just remember my login?",
      "The old system was faster.",
      "Okay, where did it go?",
      "I'll just refresh."
    ]
  },
  "frustrationTriggers": [
    "slow page loads",
    "session timeouts during tasks",
    "too many clicks to reach common functions",
    "required fields that aren't relevant",
    "system not remembering preferences",
    "confirmation dialogs for routine actions",
    "having to log in repeatedly",
    "search that doesn't find obvious matches",
    "mobile-unfriendly design on tablets",
    "updates that change familiar workflows"
  ],
  "positiveReactions": [
    "keyboard shortcuts for common actions",
    "quick search functionality",
    "recently accessed items",
    "persistent login sessions",
    "autosave on forms",
    "clear visual hierarchy",
    "fast load times",
    "bulk actions",
    "minimal clicks to complete tasks",
    "remembers last used settings"
  ],
  "typicalBehaviors": [
    "Clicks quickly without reading labels",
    "Uses keyboard shortcuts when available",
    "Opens multiple tabs to work faster",
    "Ignores optional fields entirely",
    "Refreshes when anything seems slow",
    "Has memorized common workflows",
    "Works around known system issues",
    "Multitasks between system and patients",
    "Prefers desktop over mobile at work",
    "Will escalate to IT only as last resort"
  ],
  "mentalModels": {
    "formsAre": "Obstacles between me and patient care",
    "buttonsAre": "Should do what they say instantly",
    "websitesAre": "Tools that should work as fast as I think",
    "errorsAre": "The system's fault, not mine",
    "passwordsAre": "A nuisance - why can't it remember me?",
    "accountsAre": "Required but should stay logged in"
  }
}
```
