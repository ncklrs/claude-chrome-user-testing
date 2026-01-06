# Contributing to User Testing Agent

Thank you for your interest in contributing! This project welcomes contributions of all kinds.

## Ways to Contribute

### 1. Add New Personas

The most valuable contribution is new personas that catch different UX issues.

#### Persona Guidelines

1. **Create a JSON file** in `skills/user-testing/personas/`
2. **Follow the schema** from existing personas
3. **Include all required fields**:
   - `id` - kebab-case identifier
   - `name` - Display name
   - `category` - One of: generational, accessibility, professional, context, shopping
   - `techComfort` - 1-10 scale
   - `patience` - 1-10 scale
   - `genderVariants` - male, female, neutral with names and backgrounds
   - `traits` - behavioral traits
   - `timing` - interaction timing in milliseconds
   - `narrationStyle` - vocabulary, expressions, frustration threshold
   - `frustrationTriggers` - array of UX issues that frustrate this persona
   - `positiveReactions` - array of UX patterns this persona appreciates
   - `typicalBehaviors` - array of behaviors during testing

4. **Add entry to `personas/index.json`**

#### Example Persona Ideas

- **Elderly with tremors** - Tests for large tap targets, undo functionality
- **Night shift worker** - Tests dark mode, blue light considerations
- **Rural user** - Tests on slow connections, offline capability
- **Enterprise IT admin** - Tests security, SSO, audit requirements
- **Non-profit volunteer** - Tests for simplicity, accessibility, low bandwidth
- **International user** - Tests localization, date/currency formats

### 2. Improve Existing Personas

- Add more expressions to `narrationStyle.expressions`
- Add more `frustrationTriggers` and `positiveReactions`
- Improve `typicalBehaviors` for more realistic testing
- Refine timing values based on research

### 3. Report Issues

Open an issue for:
- Personas that don't behave realistically
- Missing frustration triggers or positive reactions
- Timing that feels too fast or too slow
- Bugs in the command or agent

### 4. Documentation

- Improve README with usage examples
- Add example test sessions and reports
- Document persona creation best practices

## Submission Process

### For New Personas

1. Fork the repository
2. Create a branch: `git checkout -b persona/[persona-name]`
3. Add your persona JSON file
4. Update `personas/index.json`
5. Test the persona with several websites
6. Submit a pull request with:
   - Description of the persona
   - What UX issues it's designed to catch
   - Example sites you tested with

### For Bug Fixes / Improvements

1. Fork the repository
2. Create a branch: `git checkout -b fix/[description]`
3. Make your changes
4. Submit a pull request with:
   - Description of the issue
   - How your change fixes it

## Code Style

### JSON Files
- Use 2-space indentation
- Include all required fields
- Keep arrays readable (one item per line for long arrays)

### Markdown Files
- Use ATX-style headers (`#`, `##`, etc.)
- Include examples where helpful
- Keep lines under 100 characters where reasonable

## Persona Quality Checklist

Before submitting a new persona, verify:

- [ ] Persona has a clear purpose (what UX issues does it catch?)
- [ ] All three gender variants have distinct names and backgrounds
- [ ] Timing values are realistic for the persona type
- [ ] At least 10 frustration triggers
- [ ] At least 10 positive reactions
- [ ] At least 10 typical behaviors
- [ ] Narration expressions match the persona's vocabulary
- [ ] Tested with at least 3 different websites

## Research Resources

When creating personas, consider referencing:

- **Accessibility**: WCAG 2.1 guidelines, WebAIM research
- **Demographics**: Pew Research Center, Nielsen Norman Group
- **Behavioral**: UX research papers, usability studies
- **Technical**: Web Vitals research, performance studies

## Questions?

Open an issue with the `question` label and we'll help!

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
