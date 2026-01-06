# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0] - 2025-01-06

### Added
- **Custom Persona Creation Wizard** (`/user-test-create-persona`)
  - AI-assisted generation from natural language descriptions
  - Automatic smart defaults based on description keywords
  - Three gender variants generated automatically
  - Realistic timing patterns, expressions, and behaviors
  - Save to plugin directory or project directory

- **Persona Generator Skill** (`skills/persona-generator/SKILL.md`)
  - Complete schema reference
  - Trait correlation guidelines
  - Name generation by persona type
  - Validation rules

- **Custom Personas Support**
  - Custom personas directory (`custom-personas/`)
  - Project-level custom personas (`.claude/custom-personas/`)
  - Automatic lookup in `/user-test` command

### Changed
- Updated `/user-test` command to search for custom personas
- Added all 21 personas to command documentation

## [1.1.0] - 2025-01-06

### Added
- **Accessibility Personas** (4 new)
  - `screen-reader-user` - Tests WCAG compliance, ARIA usage, semantic HTML
  - `low-vision-user` - Tests contrast, zoom behavior, text scaling
  - `keyboard-only-user` - Tests tab navigation, focus indicators, keyboard traps
  - `cognitive-adhd-user` - Tests cognitive load, distractions, clear instructions

- **Professional Personas** (4 new)
  - `busy-executive` - Tests efficiency, quick wins, mobile use
  - `developer-critic` - Tests technical quality, error messages, API docs
  - `designer-critic` - Tests aesthetics, consistency, spacing
  - `support-agent` - Tests help flows, documentation, escalation

- **Context Personas** (4 new)
  - `bad-connection-user` - Tests slow loading, offline states, data saving
  - `distracted-parent` - Tests interruption recovery, save states, simplicity
  - `non-native-english` - Tests jargon, idioms, translation-friendly copy
  - `power-user` - Tests shortcuts, efficiency, advanced features

- **Shopping Personas** (4 new)
  - `comparison-shopper` - Tests pricing clarity, specs, comparison tools
  - `impulse-buyer` - Tests quick checkout, trust signals, urgency
  - `return-customer` - Tests return flows, support access, policy clarity
  - `gift-buyer` - Tests gift options, multiple addresses, gift messaging

- Marketplace improvements
  - MIT License
  - Contributing guidelines
  - Changelog
  - .gitignore

## [1.0.0] - 2025-01-06

### Added
- Initial release with 5 core generational personas
  - `boomer-tech-averse` - Harold/Dorothy/Pat
  - `boomer-tech-friendly` - Richard/Susan/Terry
  - `millennial-tech-skeptic` - Marcus/Jasmine/Jordan
  - `genz-digital-native` - Jayden/Zara/Alex
  - `genalpha-tablet-kid` - Mason/Luna/Riley

- Core features
  - Realistic timing based on demographic research
  - First-person narration that stays in character
  - Dark pattern detection (Millennial persona)
  - Comprehensive reports with prioritized findings
  - Chrome integration via `claude --chrome`

- Plugin structure
  - `/user-test` command
  - `user-tester` agent
  - `user-testing` skill
  - Timing configuration
  - Report templates

### Technical
- Gender variants (male/female/neutral) for all personas
- Configurable frustration triggers and positive reactions
- Mental models for authentic persona behavior
- Expression libraries for natural narration
