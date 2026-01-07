# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.11.0] - 2025-01-07

### Added
- **GitHub PR Comments** (`--pr` flag)
  - Auto-post test results as GitHub PR comments
  - Uses `gh` CLI for authentication (works with GitHub Actions `GITHUB_TOKEN`)
  - Status badges (PASS / FAIL / ISSUES)
  - Collapsible issue details by severity
  - Update existing comments instead of creating duplicates
  - Multi-persona support with separate comments per persona
  - GitHub Actions workflow example

- **GitHub PR Skill** (`skills/github-pr/`)
  - PR comment formatting and posting guidance
  - Update vs create logic with HTML comment markers
  - Error handling for missing `gh` CLI

### Changed
- Updated all applicable commands with `--pr` flag:
  - `/user-test` - Full support
  - `/smoke-test` - Full support
  - `/critical-path` - Full support
  - `/ab-test` - Full support
- User-tester agent updated with GitHub PR Comments section
- Best practices expanded with PR comment guidelines
- README expanded with PR integration documentation
- Plugin structure updated to include github-pr skill
- Roadmap updated with completed PR comments feature

## [1.10.0] - 2025-01-07

### Added
- **Mobile Viewport Testing** (`--viewport` flag)
  - Test on mobile (375x667), tablet (768x1024), or desktop (1280x720) viewports
  - Uses `browser_resize` tool for viewport configuration
  - Mobile-specific narration ("Let me tap this...", "This button is small on my phone...")
  - Automatically adjusts persona patience for mobile interactions
  - Viewport included in report session overview

- **JSON Report Export** (`--output json` flag)
  - Machine-readable JSON output for CI/CD pipelines
  - Includes session metadata, task results, issues, and summary
  - Compatible with `--quiet` mode
  - Enables dashboard integration and trend analysis
  - Full schema with persona, viewport, network, screenshots, and trace paths

- **Network Throttling** (`--network` flag)
  - Simulate slow-3g (400 Kbps, 400ms latency), fast-3g (1.6 Mbps, 100ms), or offline
  - Uses Chrome DevTools Protocol for realistic throttling
  - Automatically adjusts persona patience thresholds
  - Network-specific narration for slow loading and offline states
  - Test offline/PWA functionality with `--network offline`

- **Network Throttling Skill** (`skills/network-throttling/`)
  - CDP implementation for network emulation
  - Preset configurations and throughput calculations
  - Patience adjustment guidelines
  - Combined testing patterns (mobile + slow network)

### Changed
- Updated all commands with `--viewport`, `--output`, and `--network` flags:
  - `/user-test` - All three flags
  - `/smoke-test` - All three flags
  - `/critical-path` - All three flags
  - `/ab-test` - All three flags
  - `/check-links` - `--output` flag only
- User-tester agent updated with viewport setup, network throttling, and JSON output sections
- Best practices expanded with mobile, JSON, and network testing guidelines
- README expanded with all three feature documentation
- Plugin structure updated to include network-throttling skill
- Roadmap updated with completed features

## [1.9.0] - 2025-01-07

### Added
- **Smoke Test Presets** (`/smoke-test` command)
  - Quick validation of common flows with pre-built configurations
  - 5 built-in presets: login, signup, checkout, navigation, search
  - Each preset has default persona optimized for that flow
  - Override persona with `--persona` flag
  - Full CI/CD support with `--quiet` and `--record` flags

- **Critical Path Testing** (`/critical-path` command)
  - Test must-work user journeys defined in `.claude/critical-paths.json`
  - JSON schema with step types: navigate, task, verify, wait, input
  - Priority levels: critical, high, medium, low
  - Run specific paths or all paths
  - Fail-fast mode for CI/CD (`--fail-fast`)
  - Detailed reports with pass/fail per step

- **Link Checker** (`/check-links` command + `--check-links` flag)
  - Standalone link validation with `/check-links`
  - Integrated testing with `--check-links` on `/user-test`
  - Depth crawling (1-3 levels)
  - Internal-only mode to skip external links
  - Exclusion patterns
  - Reports broken links, redirects, and impact

- **Smoke Testing Skill** (`skills/smoke-testing/`)
  - Preset execution logic
  - 5 preset JSON configurations
  - Success criteria validation

- **Critical Paths Skill** (`skills/critical-paths/`)
  - Path execution algorithm
  - Step type handling
  - Report generation

- **Link Checker Skill** (`skills/link-checker/`)
  - Link extraction from pages
  - HEAD request validation
  - Status code interpretation
  - Redirect chain tracking

### Changed
- Updated user-tester agent with smoke test, critical path, and link checking flows
- README expanded with all three quick wins documentation
- Plugin structure updated with new commands and skills
- Roadmap updated with completed quick wins

## [1.8.0] - 2025-01-06

### Added
- **A/B Testing Comparison** (`/ab-test` command)
  - Compare two URL variants with the same persona
  - Test Variant A (control) vs Variant B (test)
  - Results matrix with winner determination per metric
  - Overall winner with explanation
  - Side-by-side screenshot comparison
  - Variant-specific and common issue categorization
  - Multi-persona A/B testing with consensus reports
  - CI/CD friendly with `--quiet` flag
  - Session recording with separate traces per variant

- **A/B Testing Skill** (`skills/ab-testing/`)
  - Winner determination logic
  - Comparison report templates
  - Multi-persona consensus calculation
  - Screenshot strategy for side-by-side comparison

### Changed
- Updated user-tester agent with A/B testing flow and narration
- README expanded with A/B testing documentation
- Plugin structure updated to include ab-testing skill
- **Roadmap complete!** All 8 planned features now implemented

## [1.7.0] - 2025-01-06

### Added
- **Session Recording** (`--record` flag)
  - Record testing sessions as Playwright Trace files
  - Captures screenshots, DOM snapshots, network requests, console logs
  - View traces interactively at trace.playwright.dev
  - Custom output path with `--record-path` flag
  - Works with single persona, multi-persona, and quiet modes
  - Trace files are self-contained and shareable

- **Session Recorder Skill** (`skills/session-recorder/`)
  - Implementation guidance for Playwright tracing
  - Trace file naming conventions
  - Integration with existing test flows

### Changed
- Updated user-tester agent with recording start/stop logic
- README expanded with session recording documentation
- Plugin structure updated to include session-recorder skill
- Future roadmap updated with completed session recording

## [1.6.0] - 2025-01-06

### Added
- **Multi-Persona Parallel Testing** (`--personas` flag)
  - Test same flow with multiple personas simultaneously
  - Comma-separated persona IDs (e.g., `--personas "boomer-tech-averse,genz-digital-native"`)
  - Comparison report showing universal vs persona-specific issues
  - Results matrix with completion rates and issue counts
  - Recommendations prioritized by frequency across personas

- **Quiet Mode** (`--quiet` flag)
  - Disable first-person narration for CI/CD integration
  - Summary-only output with annotated screenshots
  - Faster execution without timing delays
  - Works with both single and multi-persona testing

### Changed
- Updated user-tester agent to support quiet and parallel modes
- README expanded with new feature documentation
- Future roadmap updated with completed features

## [1.5.1] - 2025-01-06

### Added
- `--email` flag for `/user-test` and `/stripe-test` commands
  - Specify custom email for forms and checkout
  - Useful for testing email receipts and confirmations

## [1.5.0] - 2025-01-06

### Added
- **Stripe Checkout Testing** (`/stripe-test` command)
  - Test payment flows with any persona using official Stripe test cards
  - 18 test card scenarios: success, decline, insufficient funds, 3DS, fraud, etc.
  - Auto-fill card details for fast checkout testing
  - Detects Stripe Hosted Checkout and embedded Elements
  - Payment-specific persona narration

- **Stripe Flag on /user-test** (`--stripe --card <scenario>`)
  - Add Stripe testing to any user test session
  - Integrates with existing persona workflows

- **Stripe Checkout Skill** (`skills/stripe-checkout/`)
  - Complete test card data (`test-cards.json`)
  - Form detection and filling guidance
  - 3D Secure handling
  - Payment Experience report section

### Changed
- Updated user-tester agent with Stripe-specific narration
- Reports include Payment Experience section when using Stripe testing

## [1.4.0] - 2025-01-06

### Added
- **WCAG Audit Mode** (`/wcag-audit` command)
  - Automated accessibility audits against WCAG 2.1 criteria
  - Level A and AA compliance checking (~23 success criteria)
  - Compliance scoring with letter grades (A-F)
  - Multiple output formats: summary, detailed, JSON
  - Remediation guidance with code examples
  - Optional screenshot annotations for violations

- **WCAG Auditor Skill** (`skills/wcag-auditor/`)
  - Complete WCAG 2.1 criteria reference
  - Check implementations using Playwright browser tools
  - Scoring logic and grade calculation
  - Severity classification (Critical/Major/Minor)

- **Criteria Database** (`skills/wcag-auditor/criteria.json`)
  - Machine-readable WCAG criteria definitions
  - Selectors for automated checking
  - Remediation guidance per criterion

### Changed
- Updated README with WCAG audit documentation
- Marked WCAG audit as complete in roadmap

## [1.3.0] - 2025-01-06

### Added
- **Screenshot Annotations** (`/annotate` command)
  - Visual markers (boxes, highlights, callouts) on screenshots
  - Manual annotation via `/annotate` command
  - Automatic annotation on confusion, errors, and task completion
  - Configurable colors: red, orange, green, blue
  - Box styles: solid (errors), dashed (confusion)
  - Labels for context on annotations

- **Screenshot Annotator Skill** (`skills/screenshot-annotator/SKILL.md`)
  - CSS styling guide for annotations
  - Safe DOM injection approach via Playwright
  - Element position detection
  - Overlay management

### Changed
- Updated user-tester agent with auto-annotation triggers
- Screenshots now highlight issues automatically during testing
- Reports include annotated screenshots for clearer communication

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
