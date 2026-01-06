# User Testing Agent

A Claude Code plugin that simulates realistic user personas for UX testing. The agent embodies different user archetypes with authentic behaviors, timing patterns, and frustration triggers to identify usability issues before real users encounter them.

## Features

- **21 Personas** across 5 categories with male/female/neutral variants (63 total personalities)
- **Accessibility Testing** with screen reader, low vision, keyboard-only, and cognitive personas
- **Realistic Timing** based on research into how different users interact with technology
- **First-Person Narration** that stays in character throughout testing
- **Dark Pattern Detection** especially with the Millennial Tech-Skeptic persona
- **WCAG Compliance Testing** with accessibility-focused personas
- **Comprehensive Reports** with prioritized findings and recommendations
- **Chrome Integration** via `claude --chrome` for testing real websites

## Quick Start

### Prerequisites

1. Claude Code CLI (v2.0.73+)
2. Claude in Chrome extension (v1.0.36+)
3. Google Chrome browser
4. Paid Claude plan (Pro, Team, or Enterprise)

### Installation

```bash
# Option 1: Clone to personal plugins
git clone https://github.com/ncklrs/claude-chrome-user-testing.git ~/.claude/plugins/user-testing-agent

# Option 2: Add to project
git clone https://github.com/ncklrs/claude-chrome-user-testing.git .claude/plugins/user-testing-agent
```

Restart Claude Code to detect the plugin.

### Basic Usage

Start Claude with Chrome integration:
```bash
claude --chrome
```

Run a user test:
```
/user-test --url https://example.com --persona genz-digital-native
```

With specific tasks:
```
/user-test --url https://shop.example.com --persona boomer-tech-averse --tasks "find product, add to cart, checkout"
```

With gender variant:
```
/user-test --url https://app.example.com --persona screen-reader-user --gender f
```

## Available Personas

### Generational Personas (5)

Test how different age groups interact with technology.

| ID | Names | Tech | Patience | Best For |
|----|-------|------|----------|----------|
| `boomer-tech-averse` | Harold/Dorothy/Pat | 2/10 | 7/10 | Clarity, explicit instructions, accessibility |
| `boomer-tech-friendly` | Richard/Susan/Terry | 6/10 | 8/10 | Familiar patterns, navigation, customer service |
| `millennial-tech-skeptic` | Marcus/Jasmine/Jordan | 7/10 | 5/10 | Dark patterns, privacy, manipulation |
| `genz-digital-native` | Jayden/Zara/Alex | 9/10 | 3/10 | Speed, mobile, modern UX |
| `genalpha-tablet-kid` | Mason/Luna/Riley | 8/10 | 2/10 | Touch interfaces, visual design |

### Accessibility Personas (4)

Test WCAG compliance and inclusive design.

| ID | Names | Focus | Best For |
|----|-------|-------|----------|
| `screen-reader-user` | David/Maria/Sam | NVDA/JAWS/VoiceOver | ARIA, semantic HTML, focus order, alt text |
| `low-vision-user` | Robert/Patricia/Chris | Zoom, high contrast | Contrast ratios, text scaling, large targets |
| `keyboard-only-user` | Kevin/Angela/Jamie | Tab navigation | Focus indicators, keyboard traps, shortcuts |
| `cognitive-adhd-user` | Tyler/Brittany/Quinn | Attention, clarity | Cognitive load, distractions, save states |

### Professional Personas (4)

Test from expert perspectives.

| ID | Names | Role | Best For |
|----|-------|------|----------|
| `busy-executive` | Michael/Jennifer/Morgan | C-suite | Express checkout, efficiency, mobile |
| `developer-critic` | Brandon/Sarah/Taylor | Engineer | Console errors, performance, Lighthouse |
| `designer-critic` | Adrian/Mia/Avery | UX Designer | Visual hierarchy, spacing, consistency |
| `support-agent` | Carlos/Priya/Casey | Support | Help flows, self-service, documentation |

### Context Personas (4)

Test real-world usage conditions.

| ID | Names | Context | Best For |
|----|-------|---------|----------|
| `bad-connection-user` | Derek/Tammy/River | Slow 3G | Loading states, offline mode, data saving |
| `distracted-parent` | Jason/Kristin/Pat | Interrupted | Save states, resumability, simplicity |
| `non-native-english` | Hiroshi/Ana/Sasha | ESL | Idioms, cultural references, clear language |
| `power-user` | Victor/Diana/Robin | Expert | Keyboard shortcuts, bulk actions, advanced features |

### Shopping Personas (4)

Test e-commerce flows.

| ID | Names | Behavior | Best For |
|----|-------|----------|----------|
| `comparison-shopper` | Frank/Linda/Drew | Research | Specs, pricing, reviews, comparison tools |
| `impulse-buyer` | Jake/Ashley/Riley | Fast | Express checkout, Apple Pay, frictionless |
| `return-customer` | Greg/Karen/Alex | Returning | Return flows, support access, refund process |
| `gift-buyer` | Eric/Michelle/Jordan | Gifting | Gift wrap, messaging, multiple addresses |

## Persona Details

### Generational

#### Boomer Tech-Averse
> "Let me read this carefully... I don't want to click the wrong thing."

- Reads everything on the page before acting
- Hovers over buttons for a long time before clicking
- Gets confused by icons without text labels
- Looks for phone numbers to call instead

#### Boomer Tech-Friendly
> "Ah, this looks familiar. Same as the other site I use."

- Compares everything to Amazon or familiar sites
- Expects traditional navigation patterns
- Gets frustrated when UIs change unnecessarily

#### Millennial Tech-Skeptic
> "Why do they need this information? This feels sketchy..."

- Scans for dark patterns before engaging
- Unchecks all pre-checked boxes
- Opens privacy policy in new tab

#### Gen Z Digital Native
> "Bruh, why is this loading so slow? Skip, skip, skip..."

- Skips all onboarding and tutorials
- Expects instant feedback (<2 seconds)
- Abandons forms with more than 3-4 fields

#### Gen Alpha Tablet Kid
> "Ooh! *tap tap tap* Where are the pictures?"

- Taps everything to see what happens
- Tries to swipe on non-swipeable elements
- Expects game-like feedback

### Accessibility

#### Screen Reader User
> "Let me navigate by headings... This image has no alt text."

- Navigates by headings, landmarks, and tab order
- Immediately notices missing alt text and ARIA labels
- Knows when sites are WCAG compliant

#### Low Vision User
> "Let me zoom in... This text is too small."

- Zooms to 200-400% immediately
- Tests if layouts break when zoomed
- Needs high contrast and large touch targets

#### Keyboard-Only User
> "Tab, tab, tab... where's the focus indicator?"

- Navigates entirely with Tab/Shift+Tab
- Tests for focus traps in modals
- Expects all interactive elements keyboard-accessible

#### Cognitive/ADHD User
> "Okay wait, what was I doing? There's so much here..."

- Gets distracted by animations and movements
- Loses place in long forms
- Benefits from progress indicators and auto-save

### Professional

#### Busy Executive
> "Get to the point. I don't have time for this."

- Expects one-click solutions
- Will pay extra to save time
- Abandons complex processes immediately

#### Developer Critic
> "Let me check the console... That's a lot of JavaScript."

- Opens DevTools immediately
- Checks Lighthouse scores mentally
- Notices security issues and performance problems

#### Designer Critic
> "The visual hierarchy here is confusing. Why are there three button styles?"

- Notices 1px misalignments
- Evaluates typography and spacing
- Checks for design system consistency

#### Support Agent
> "A customer would get stuck here. Where's the help?"

- Tests from "helping customers" perspective
- Evaluates self-service effectiveness
- Notes what would generate support tickets

### Context

#### Bad Connection User
> "Okay, waiting for this to load... At least it shows a loading indicator."

- Patient with slow loads if there's feedback
- Values offline mode and auto-save
- Notices when images are too large

#### Distracted Parent
> "Okay quick, let me do this before— hold on... *returns* Wait, where was I?"

- Gets interrupted every 30-60 seconds
- Needs clear "you are here" indicators
- Values saved progress immensely

#### Non-Native English Speaker
> "What does this phrase mean? Is this a joke or instruction?"

- Struggles with idioms and slang
- Confused by cultural references
- Prefers formal, clear language

#### Power User
> "Cmd+K? No? Ugh. Where are the keyboard shortcuts?"

- Tries keyboard shortcuts immediately
- Looks for bulk actions and advanced features
- Frustrated by mouse-only interactions

### Shopping

#### Comparison Shopper
> "Let me check the specs... What's the price on the other site?"

- Opens products in multiple tabs
- Reads negative reviews specifically
- Needs complete information before deciding

#### Impulse Buyer
> "Ooh, I want this. Take my money! Where's buy now?"

- Decision time under 30 seconds
- Abandons at first sign of friction
- Express checkout is essential

#### Return Customer
> "Where's the return policy? I need to return this."

- Already frustrated, needs quick resolution
- Evaluates company by return experience
- Will write negative review if difficult

#### Gift Buyer
> "Can I add a gift message? Will the price be shown?"

- Needs different shipping address
- Wants gift wrapping and messaging
- Concerned about arrival timing

## Report Format

After testing, the agent generates a comprehensive report:

1. **Executive Summary** - Quick overview and recommendation
2. **Task Results** - Success/failure status for each task
3. **Issues Found** - Categorized as Critical/Major/Minor
4. **Persona Insights** - What this specific user type struggled with
5. **Recommendations** - Prioritized action items

## Advanced Usage

### Test Accessibility

```
/user-test --url https://example.com --persona screen-reader-user --tasks "navigate to contact form, submit inquiry"
```

### Test E-commerce Flow

```
/user-test --url https://shop.example.com --persona impulse-buyer --tasks "find product, checkout"
```

### Test Under Bad Conditions

```
/user-test --url https://example.com --persona bad-connection-user --tasks "complete signup"
```

### Compare Multiple Personas

Run the same flow with different personas:
```
/user-test --url https://example.com --persona boomer-tech-averse --tasks "sign up"
/user-test --url https://example.com --persona genz-digital-native --tasks "sign up"
/user-test --url https://example.com --persona screen-reader-user --tasks "sign up"
```

## Plugin Structure

```
user-testing-agent/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest
├── commands/
│   └── user-test.md             # /user-test command
├── agents/
│   └── user-tester/
│       └── AGENT.md             # Core testing agent
├── skills/
│   └── user-testing/
│       ├── SKILL.md             # Skill definition
│       ├── personas/            # 21 persona JSON files
│       ├── behaviors/           # Timing configuration
│       └── templates/           # Report templates
├── CONTRIBUTING.md              # How to add personas
├── CHANGELOG.md                 # Version history
├── LICENSE                      # MIT
└── README.md
```

## How It Works

1. **Persona Loading**: The agent loads the selected persona's behavioral configuration
2. **Character Embodiment**: The agent adopts the persona's mindset, vocabulary, and expectations
3. **Timed Interactions**: All actions follow realistic timing based on persona traits
4. **Continuous Narration**: First-person commentary throughout testing
5. **Issue Detection**: Automatic identification of UX problems
6. **Report Generation**: Structured findings with recommendations

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

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines on adding new personas.

Quick steps:
1. Create a new JSON file in `skills/user-testing/personas/`
2. Follow the schema from existing personas
3. Add entry to `personas/index.json`
4. Test with various websites
5. Submit a pull request

## Future Roadmap

- [ ] CI/CD integration with headless mode
- [ ] A/B testing comparison reports
- [ ] Custom persona creation wizard
- [ ] Screenshot annotation
- [ ] Session recording export
- [ ] Multi-persona parallel testing
- [ ] WCAG audit mode with compliance scoring

## License

MIT - See [LICENSE](LICENSE)
