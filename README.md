# User Testing Agent

A Claude Code plugin that simulates realistic user personas for UX testing. The agent embodies different user archetypes (Boomers, Millennials, Gen Z, Gen Alpha) with authentic behaviors, timing patterns, and frustration triggers to identify usability issues before real users encounter them.

## Features

- **5 Core Personas** with male/female/neutral variants (15 total personalities)
- **Realistic Timing** based on research into how different demographics interact with technology
- **First-Person Narration** that stays in character throughout testing
- **Dark Pattern Detection** especially with the Millennial Tech-Skeptic persona
- **Comprehensive Reports** with prioritized findings and recommendations
- **Chrome Integration** via `claude --chrome` for testing real websites

## Quick Start

### Prerequisites

1. Claude Code CLI (v2.0.73+)
2. Claude in Chrome extension (v1.0.36+)
3. Google Chrome browser
4. Paid Claude plan (Pro, Team, or Enterprise)

### Installation

1. Clone or download this plugin to your plugins directory:
   ```bash
   # Option 1: Clone to personal plugins
   git clone <repo-url> ~/.claude/plugins/user-testing-agent

   # Option 2: Add to project
   git clone <repo-url> .claude/plugins/user-testing-agent
   ```

2. Restart Claude Code to detect the plugin

3. Verify installation:
   ```bash
   claude
   /help  # Should show user-test command
   ```

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
/user-test --url https://app.example.com --persona millennial-tech-skeptic --gender f
```

## Available Personas

| ID | Name | Tech Comfort | Patience | Best For Testing |
|----|------|--------------|----------|------------------|
| `boomer-tech-averse` | Harold/Dorothy/Pat | 2/10 | 7/10 | Clarity, accessibility, explicit instructions |
| `boomer-tech-friendly` | Richard/Susan/Terry | 6/10 | 8/10 | Familiar patterns, navigation, customer service |
| `millennial-tech-skeptic` | Marcus/Jasmine/Jordan | 7/10 | 5/10 | Dark patterns, privacy, manipulation |
| `genz-digital-native` | Jayden/Zara/Alex | 9/10 | 3/10 | Speed, mobile, modern UX expectations |
| `genalpha-tablet-kid` | Mason/Luna/Riley | 8/10 | 2/10 | Touch interfaces, visual design, simplicity |

## Persona Details

### Boomer Tech-Averse
> "Let me read this carefully... I don't want to click the wrong thing."

- Reads everything on the page before acting
- Hovers over buttons for a long time before clicking
- Gets confused by icons without text labels
- Looks for phone numbers to call instead
- Types very slowly and carefully

**Best for testing**: Form clarity, error messages, navigation simplicity, explicit instructions

### Boomer Tech-Friendly
> "Ah, this looks familiar. Same as the other site I use."

- Compares everything to Amazon or familiar sites
- Expects traditional navigation patterns
- Gets frustrated when UIs change unnecessarily
- Reads reviews before purchasing
- May share interesting finds on Facebook

**Best for testing**: Standard patterns, navigation, checkout flows, customer service access

### Millennial Tech-Skeptic
> "Why do they need this information? This feels sketchy..."

- Scans for dark patterns before engaging
- Immediately dismisses pop-ups without reading
- Unchecks all pre-checked boxes
- Opens privacy policy in new tab
- Uses incognito for price comparisons

**Best for testing**: Dark patterns, privacy concerns, manipulative copy, hidden fees

### Gen Z Digital Native
> "Bruh, why is this loading so slow? Skip, skip, skip..."

- Skips all onboarding and tutorials
- Expects instant feedback (<2 seconds)
- Abandons forms with more than 3-4 fields
- Expects social login options
- Will leave negative review if frustrated

**Best for testing**: Performance, mobile experience, modern patterns, social features

### Gen Alpha Tablet Kid
> "Ooh! *tap tap tap* Where are the pictures?"

- Taps everything to see what happens
- Tries to swipe on non-swipeable elements
- Looks for big colorful buttons
- Ignores all text, looks for images
- Expects game-like feedback

**Best for testing**: Touch interfaces, visual hierarchy, simplicity, engagement

## Report Format

After testing, the agent generates a comprehensive report including:

1. **Executive Summary** - Quick overview and recommendation
2. **Task Results** - Success/failure status for each task
3. **Issues Found** - Categorized as Critical/Major/Minor
4. **Persona Insights** - What this specific user type struggled with
5. **Recommendations** - Prioritized action items

Example issue format:
```markdown
### Critical Issues

#### 1. Checkout button invisible on mobile

- **Location**: Cart page, below fold
- **Personas Affected**: All, especially Gen Z and Gen Alpha
- **Description**: Primary CTA requires scrolling to find
- **Evidence**: [Screenshot]
- **Recommendation**: Make checkout sticky or move above fold
```

## Advanced Usage

### Testing Multiple Personas

Run the same flow with different personas to compare experiences:
```
/user-test --url https://example.com --persona boomer-tech-averse --tasks "sign up"
/user-test --url https://example.com --persona genz-digital-native --tasks "sign up"
```

### Verbose Mode

Get extra detailed narration:
```
/user-test --url https://example.com --persona millennial-tech-skeptic --verbose
```

### Custom Tasks

Specify exactly what to test:
```
/user-test --url https://shop.example.com --persona boomer-tech-averse --tasks "search for shoes, filter by size, add to cart, apply coupon, checkout as guest"
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
│       ├── personas/            # Persona JSON files
│       ├── behaviors/           # Timing configuration
│       └── templates/           # Report templates
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

| Action | Tech-Averse | Tech-Friendly | Tech-Skeptic | Digital Native | Tablet Kid |
|--------|-------------|---------------|--------------|----------------|------------|
| Reading | 120 WPM | 180 WPM | 220 WPM | 350 WPM | 80 WPM |
| Click delay | 2.5s | 1s | 0.8s | 0.3s | 0.4s |
| Confusion pause | 25s | 12s | 8s | 5s | 3s |
| Page load tolerance | 10s | 8s | 5s | 2s | 3s |

## Contributing

To add a new persona:

1. Create a new JSON file in `skills/user-testing/personas/`
2. Follow the schema from existing personas
3. Add entry to `personas/index.json`
4. Test with various websites

## Future Roadmap

- [ ] CI/CD integration with headless mode
- [ ] A/B testing comparison reports
- [ ] Custom persona creation wizard
- [ ] Screenshot annotation
- [ ] Session recording export
- [ ] Multi-persona parallel testing

## License

MIT
