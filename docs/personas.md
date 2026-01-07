# Persona Reference

Complete reference for all 25 built-in personas with detailed behaviors and use cases.

## Generational Personas (5)

Test how different age groups interact with technology.

| ID | Names | Tech | Patience | Best For |
|----|-------|------|----------|----------|
| `boomer-tech-averse` | Harold/Dorothy/Pat | 2/10 | 7/10 | Clarity, explicit instructions, accessibility |
| `boomer-tech-friendly` | Richard/Susan/Terry | 6/10 | 8/10 | Familiar patterns, navigation, customer service |
| `millennial-tech-skeptic` | Marcus/Jasmine/Jordan | 7/10 | 5/10 | Dark patterns, privacy, manipulation |
| `genz-digital-native` | Jayden/Zara/Alex | 9/10 | 3/10 | Speed, mobile, modern UX |
| `genalpha-tablet-kid` | Mason/Luna/Riley | 8/10 | 2/10 | Touch interfaces, visual design |

### Boomer Tech-Averse
> "Let me read this carefully... I don't want to click the wrong thing."

- Reads everything on the page before acting
- Hovers over buttons for a long time before clicking
- Gets confused by icons without text labels
- Looks for phone numbers to call instead

### Boomer Tech-Friendly
> "Ah, this looks familiar. Same as the other site I use."

- Compares everything to Amazon or familiar sites
- Expects traditional navigation patterns
- Gets frustrated when UIs change unnecessarily

### Millennial Tech-Skeptic
> "Why do they need this information? This feels sketchy..."

- Scans for dark patterns before engaging
- Unchecks all pre-checked boxes
- Opens privacy policy in new tab

### Gen Z Digital Native
> "Bruh, why is this loading so slow? Skip, skip, skip..."

- Skips all onboarding and tutorials
- Expects instant feedback (<2 seconds)
- Abandons forms with more than 3-4 fields

### Gen Alpha Tablet Kid
> "Ooh! *tap tap tap* Where are the pictures?"

- Taps everything to see what happens
- Tries to swipe on non-swipeable elements
- Expects game-like feedback

---

## Accessibility Personas (4)

Test WCAG compliance and inclusive design.

| ID | Names | Focus | Best For |
|----|-------|-------|----------|
| `screen-reader-user` | David/Maria/Sam | NVDA/JAWS/VoiceOver | ARIA, semantic HTML, focus order, alt text |
| `low-vision-user` | Robert/Patricia/Chris | Zoom, high contrast | Contrast ratios, text scaling, large targets |
| `keyboard-only-user` | Kevin/Angela/Jamie | Tab navigation | Focus indicators, keyboard traps, shortcuts |
| `cognitive-adhd-user` | Tyler/Brittany/Quinn | Attention, clarity | Cognitive load, distractions, save states |

### Screen Reader User
> "Let me navigate by headings... This image has no alt text."

- Navigates by headings, landmarks, and tab order
- Immediately notices missing alt text and ARIA labels
- Knows when sites are WCAG compliant

### Low Vision User
> "Let me zoom in... This text is too small."

- Zooms to 200-400% immediately
- Tests if layouts break when zoomed
- Needs high contrast and large touch targets

### Keyboard-Only User
> "Tab, tab, tab... where's the focus indicator?"

- Navigates entirely with Tab/Shift+Tab
- Tests for focus traps in modals
- Expects all interactive elements keyboard-accessible

### Cognitive/ADHD User
> "Okay wait, what was I doing? There's so much here..."

- Gets distracted by animations and movements
- Loses place in long forms
- Benefits from progress indicators and auto-save

---

## Professional Personas (4)

Test from expert perspectives.

| ID | Names | Role | Best For |
|----|-------|------|----------|
| `busy-executive` | Michael/Jennifer/Morgan | C-suite | Express checkout, efficiency, mobile |
| `developer-critic` | Brandon/Sarah/Taylor | Engineer | Console errors, performance, Lighthouse |
| `designer-critic` | Adrian/Mia/Avery | UX Designer | Visual hierarchy, spacing, consistency |
| `support-agent` | Carlos/Priya/Casey | Support | Help flows, self-service, documentation |

### Busy Executive
> "Get to the point. I don't have time for this."

- Expects one-click solutions
- Will pay extra to save time
- Abandons complex processes immediately

### Developer Critic
> "Let me check the console... That's a lot of JavaScript."

- Opens DevTools immediately
- Checks Lighthouse scores mentally
- Notices security issues and performance problems

### Designer Critic
> "The visual hierarchy here is confusing. Why are there three button styles?"

- Notices 1px misalignments
- Evaluates typography and spacing
- Checks for design system consistency

### Support Agent
> "A customer would get stuck here. Where's the help?"

- Tests from "helping customers" perspective
- Evaluates self-service effectiveness
- Notes what would generate support tickets

---

## Context Personas (4)

Test real-world usage conditions.

| ID | Names | Context | Best For |
|----|-------|---------|----------|
| `bad-connection-user` | Derek/Tammy/River | Slow 3G | Loading states, offline mode, data saving |
| `distracted-parent` | Jason/Kristin/Pat | Interrupted | Save states, resumability, simplicity |
| `non-native-english` | Hiroshi/Ana/Sasha | ESL | Idioms, cultural references, clear language |
| `power-user` | Victor/Diana/Robin | Expert | Keyboard shortcuts, bulk actions, advanced features |

### Bad Connection User
> "Okay, waiting for this to load... At least it shows a loading indicator."

- Patient with slow loads if there's feedback
- Values offline mode and auto-save
- Notices when images are too large

### Distracted Parent
> "Okay quick, let me do this before— hold on... *returns* Wait, where was I?"

- Gets interrupted every 30-60 seconds
- Needs clear "you are here" indicators
- Values saved progress immensely

### Non-Native English Speaker
> "What does this phrase mean? Is this a joke or instruction?"

- Struggles with idioms and slang
- Confused by cultural references
- Prefers formal, clear language

### Power User
> "Cmd+K? No? Ugh. Where are the keyboard shortcuts?"

- Tries keyboard shortcuts immediately
- Looks for bulk actions and advanced features
- Frustrated by mouse-only interactions

---

## Shopping Personas (4)

Test e-commerce flows.

| ID | Names | Behavior | Best For |
|----|-------|----------|----------|
| `comparison-shopper` | Frank/Linda/Drew | Research | Specs, pricing, reviews, comparison tools |
| `impulse-buyer` | Jake/Ashley/Riley | Fast | Express checkout, Apple Pay, frictionless |
| `return-customer` | Greg/Karen/Alex | Returning | Return flows, support access, refund process |
| `gift-buyer` | Eric/Michelle/Jordan | Gifting | Gift wrap, messaging, multiple addresses |

### Comparison Shopper
> "Let me check the specs... What's the price on the other site?"

- Opens products in multiple tabs
- Reads negative reviews specifically
- Needs complete information before deciding

### Impulse Buyer
> "Ooh, I want this. Take my money! Where's buy now?"

- Decision time under 30 seconds
- Abandons at first sign of friction
- Express checkout is essential

### Return Customer
> "Where's the return policy? I need to return this."

- Already frustrated, needs quick resolution
- Evaluates company by return experience
- Will write negative review if difficult

### Gift Buyer
> "Can I add a gift message? Will the price be shown?"

- Needs different shipping address
- Wants gift wrapping and messaging
- Concerned about arrival timing

---

## International Personas (4)

Test locale-specific requirements and internationalization.

| ID | Names | Region | Best For |
|----|-------|--------|----------|
| `german-business-user` | Klaus/Greta/Alex | Germany | DD.MM.YYYY dates, EUR currency, GDPR, formal tone |
| `japanese-user` | Takeshi/Yuki/Hiro | Japan | YYYY/MM/DD dates, yen, family-name-first, IME |
| `arabic-rtl-user` | Ahmed/Fatima/Nour | Middle East | RTL layout, Arabic fonts, mirrored navigation |
| `brazilian-user` | Carlos/Juliana/Dani | Brazil | DD/MM/YYYY, R$ currency, PIX/boleto payments |

### German Business User
> "Das Datum-Format ist falsch. Where is the GDPR information?"

**Key Expectations:**
- Date format: DD.MM.YYYY
- Currency: EUR with comma decimal (1.234,56 EUR)
- Formal language (Sie vs du)
- GDPR compliance visible
- QWERTZ keyboard layout

### Japanese User
> "Family name should come first. This address format is not Japanese-style."

**Key Expectations:**
- Date format: YYYY/MM/DD or Japanese era
- Currency: JPY with no decimals
- Family name first in forms
- Polite/honorific language
- IME input support

### Arabic RTL User
> "The layout is broken - not proper RTL. This button is on the wrong side."

**Key Expectations:**
- Full RTL layout mirroring
- Navigation on right side
- Arabic font readability
- Text direction consistency
- Regional currency (SAR/AED/EGP)

**RTL Issues Checked:**
- Navigation placement (should be on right)
- Icons mirrored correctly
- Form labels alignment
- Mixed LTR/RTL text handling

### Brazilian User
> "Cadê o português? Where's PIX payment? No boleto option? Sério?"

**Key Expectations:**
- Date format: DD/MM/YYYY
- Currency: R$ with comma decimal
- PIX/boleto payment options
- Installment payments (parcelamento)
- Warm, informal tone
- CPF tax ID field

---

## Timing System

The plugin simulates realistic human timing:

| Persona Type | Reading | Click Delay | Confusion | Page Load Tolerance |
|--------------|---------|-------------|-----------|---------------------|
| Tech-Averse | 120 WPM | 2.5s | 25s | 10s |
| Tech-Friendly | 180 WPM | 1s | 12s | 8s |
| Tech-Skeptic | 220 WPM | 0.8s | 8s | 5s |
| Digital Native | 350 WPM | 0.3s | 5s | 2s |
| Screen Reader | 300 WPM | 0.4s | 10s | 8s |
| Low Vision | 100 WPM | 1.5s | 15s | 10s |
| Keyboard-Only | 200 WPM | 0.3s | 8s | 6s |
| ADHD | 150 WPM | 0.3s | 3s | 4s |
| Executive | 400 WPM | 0.2s | 3s | 2s |

---

## Custom Personas

Create your own personas using the wizard:

```
/user-test-create-persona
```

Describe your target user in natural language and the wizard generates a complete persona with three gender variants, timing patterns, and expressions.

See [CONTRIBUTING.md](../CONTRIBUTING.md) for adding personas manually.
