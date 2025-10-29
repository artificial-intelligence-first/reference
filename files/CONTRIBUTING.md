---
title: CONTRIBUTING.md Guide
slug: contributing
status: living
last_updated: 2025-10-29
tags: [contributing, open-source, collaboration, community, governance]
summary: "Best practices for creating CONTRIBUTING.md files that guide contributors through the process of participating in open-source projects effectively."
sources:
  - { id: R1, title: "GitHub Docs - Setting guidelines for repository contributors", url: "https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/setting-guidelines-for-repository-contributors", accessed: "2025-10-29" }
  - { id: R2, title: "Mozilla Science Lab - Contributing Guidelines", url: "https://mozillascience.github.io/working-open-workshop/contributing/", accessed: "2025-10-29" }
  - { id: R3, title: "Wikipedia - Contributing guidelines", url: "https://en.wikipedia.org/wiki/Contributing_guidelines", accessed: "2025-10-29" }
  - { id: R4, title: "pyOpenSci - Contributing File Guide", url: "https://www.pyopensci.org/python-package-guide/documentation/repository-files/contributing-file.html", accessed: "2025-10-29" }
  - { id: R5, title: "Dagster CONTRIBUTING.md", url: "https://github.com/dagster-io/dagster/blob/3c2d36054f4014ca8316e533975a538d6eff62c4/docs/CONTRIBUTING.md", accessed: "2025-10-29" }
---

# CONTRIBUTING.md Guide

> **For Humans**: This guide explains how to create effective CONTRIBUTING.md files that welcome contributors and provide clear guidelines for participating in open-source projects.
>
> **For AI Agents**: Use this guide to understand CONTRIBUTING.md conventions, help maintainers create contributor guidelines, and follow contribution processes when working with open-source repositories.

## Overview

CONTRIBUTING.md is a standard file in open-source projects that provides guidelines for how to contribute to the project. It serves as the primary entry point for new contributors, reducing friction and establishing clear expectations for collaboration.

**Key Benefits**:
- Reduces maintainer burden by answering common questions upfront
- Sets clear expectations for contribution quality and process
- Welcomes new contributors with friendly, actionable guidance
- Establishes consistent workflows across the project
- Increases contribution quality through clear standards

**Quick Navigation**
- [Core Principles](#core-principles)
- [Essential Sections](#essential-sections)
- [Best Practices](#best-practices)
- [Content Guidelines](#content-guidelines)
- [GitHub Integration](#github-integration)
- [Template for New Projects](#template-for-new-projects)
- [Adoption Checklist](#adoption-checklist)
- [Common Patterns by Project Type](#common-patterns-by-project-type)
- [Further Resources](#further-resources)
- [Update Log](#update-log)

## Core Principles

### 1. Welcome and Inclusivity

A CONTRIBUTING.md should be **welcoming and encouraging**:

- Use friendly, inclusive language
- Assume good faith from contributors
- Acknowledge that contributions come in many forms (code, docs, bug reports, design)
- Lower barriers to entry for new contributors
- Express gratitude for contributions

**Example Opening** *(Sample)*:

```markdown
# Contributing to [Project Name]

Thank you for considering contributing to [Project Name]! We value all contributions,
whether it's fixing a typo, reporting a bug, or implementing a new feature.

This document provides guidelines to help you contribute effectively.
```

### 2. Clarity and Specificity

Provide **clear, actionable instructions**:

- Use step-by-step workflows
- Include examples and templates
- Link to relevant resources
- Define technical terms
- Provide concrete acceptance criteria

### 3. Completeness

Cover all aspects of contribution:

- Development environment setup
- Testing requirements
- Documentation standards
- Code style and formatting
- Review process and timelines
- Communication channels

## Essential Sections

A comprehensive CONTRIBUTING.md typically includes these sections (in order):

### 1. Introduction and Welcome

- Brief welcome message
- Types of contributions welcomed
- Code of Conduct reference
- Quick links to key resources

### 2. Getting Started

**Environment Setup**:
```markdown
## Getting Started

### Prerequisites
- Node.js 18+ and npm 9+
- Git 2.30+
- Docker (optional, for integration tests)

### Setup
1. Fork the repository
2. Clone your fork: `git clone https://github.com/YOUR-USERNAME/project.git`
3. Install dependencies: `npm install`
4. Run tests: `npm test`
```

### 3. How to Report Bugs

**Bug Report Guidelines** [R1][R2]:
- Link to issue tracker
- Provide issue template or required information
- Explain triage process
- Set expectations for response time

**Example** *(Sample)*:
```markdown
## Reporting Bugs

Before creating a bug report:
1. Check existing issues to avoid duplicates
2. Update to the latest version to confirm the bug still exists

When filing a bug report, include:
- Clear, descriptive title
- Steps to reproduce the behavior
- Expected vs. actual behavior
- Environment details (OS, version, etc.)
- Screenshots or error messages if applicable
```

### 4. How to Suggest Features

**Feature Request Process** [R1][R2]:
- Criteria for accepting features
- Discussion process (RFC, issues, etc.)
- Timeline expectations
- Alignment with project roadmap

### 5. Pull Request Process

**PR Workflow** [R1][R2][R5]:
```markdown
## Pull Request Process

1. **Create a branch** from `main` using naming convention: `feature/`, `fix/`, `docs/`
2. **Make your changes** following our coding standards
3. **Write tests** for new functionality
4. **Update documentation** if you change APIs or behavior
5. **Run the test suite**: `npm test`
6. **Run linting**: `npm run lint`
7. **Commit** using conventional commit format
8. **Push** to your fork and create a pull request
9. **Request review** from maintainers
10. **Address feedback** promptly and iterate

### PR Requirements
- [ ] All tests pass
- [ ] Code follows style guidelines
- [ ] Documentation updated
- [ ] Commit messages follow conventions
- [ ] No merge conflicts
```

### 6. Coding Standards

**Code Style Guidelines** [R2][R4][R5]:
- Language-specific conventions
- Formatting tools (Prettier, Black, gofmt, etc.)
- Linting rules (ESLint, Pylint, etc.)
- Naming conventions
- Comment and documentation style
- File organization patterns

**Example** *(Sample)*:
```markdown
## Coding Standards

### Style
- Follow [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- Use 2 spaces for indentation
- Maximum line length: 100 characters
- Run `npm run format` before committing

### Naming
- Functions: `camelCase`
- Classes: `PascalCase`
- Constants: `UPPER_SNAKE_CASE`
- Files: `kebab-case.ts`

### Documentation
- JSDoc comments for all public APIs
- Inline comments for complex logic
- README updates for new features
```

### 7. Testing Requirements

**Testing Guidelines** [R2][R4][R5]:
- Required test coverage percentage
- Types of tests (unit, integration, e2e)
- Testing framework and tools
- How to run tests locally
- Test naming conventions

### 8. Documentation Requirements

**Documentation Standards** [R2][R4][R5]:
- API documentation format (JSDoc, Sphinx, etc.)
- README updates
- Changelog entries
- Inline code comments
- Tutorial or guide updates

**Example from Dagster** [R5]:
```markdown
## Documentation Guidelines

### Images
- Minimum resolution: 1920x1080
- Store in `/docs/static/images/`
- Use descriptive alt text

### Links
- Use full paths, not relative links
- Omit trailing slashes for SEO

### Code Examples
- Reference external files when possible
- Include working, tested examples
- Add comments explaining key steps
```

### 9. Commit Message Format

**Commit Conventions** [R1][R2]:
- Conventional Commits format
- Required structure (type, scope, description)
- Examples of good vs. bad commits

**Example** *(Sample)*:
```markdown
## Commit Message Format

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types**: feat, fix, docs, style, refactor, test, chore

**Examples**:
✅ `feat(auth): add OAuth2 support`
✅ `fix(api): handle null response in user endpoint`
✅ `docs(readme): update installation instructions`

❌ `updated stuff`
❌ `fixed bug`
```

### 10. Review Process

**Review Timeline and Expectations** [R1][R2]:
- Expected review turnaround time
- Review criteria
- How to request re-review
- Merging process and requirements

### 11. Community and Communication

**Communication Channels** [R1][R2]:
- Discussion forums
- Chat platforms (Discord, Slack)
- Mailing lists
- Office hours or community calls
- Social media

**Example** *(Sample)*:
```markdown
## Community

- **GitHub Discussions**: For questions and general discussion
- **Discord**: Real-time chat at discord.gg/project
- **Twitter**: @project for announcements
- **Monthly Community Call**: First Tuesday, 5pm UTC
```

### 12. License and Legal

**Legal Considerations** [R1][R2][R3]:
- License information (link to LICENSE file)
- Contributor License Agreement (CLA) if required
- Copyright assignment
- Developer Certificate of Origin (DCO)

**Example** *(Sample)*:
```markdown
## License

By contributing, you agree that your contributions will be licensed under the MIT License.

All contributors must sign our Contributor License Agreement (CLA) before we can merge PRs.
Sign at: https://cla.project.org
```

## Best Practices

### For Repository Maintainers

**✅ DO**:

1. **Keep it current** - Update as processes evolve [R1][R2]
2. **Be specific** - Provide exact commands, file paths, and examples [R1][R2][R5]
3. **Set expectations** - Define response times, review criteria, and quality standards [R1][R2]
4. **Provide templates** - Include issue and PR templates [R1][R2]
5. **Thank contributors** - Acknowledge contributions in changelogs and release notes [R2]
6. **Link related docs** - Reference Code of Conduct, LICENSE, README, issue templates [R1]
7. **Consider skill levels** - Provide guidance for first-time contributors [R2]
8. **Explain the "why"** - Don't just say what to do, explain why it matters [R2]
9. **Maintain consistency** - Align with other governance files (GOVERNANCE.md, SECURITY.md) [R1][R3]
10. **Test the instructions** - Verify setup steps work for new contributors [R2]

**❌ DON'T**:

1. **Assume knowledge** - Don't skip steps obvious to maintainers but unclear to newcomers [R2]
2. **Be vague** - Avoid phrases like "make sure code is good" [R1]
3. **Overwhelm** - Don't create 50-page documents; link to detailed guides instead [R2]
4. **Ignore updates** - Outdated CONTRIBUTING.md is worse than none [R1]
5. **Be unwelcoming** - Avoid stern or discouraging language [R2]

### For AI Agents Working with CONTRIBUTING.md

When asked to help with contributions or create CONTRIBUTING.md:

1. **Read the CONTRIBUTING.md first** - Always check for existing guidelines before contributing [R1][R2]
2. **Follow the exact process** - Don't skip steps like testing or documentation [R1][R2]
3. **Use provided templates** - Fill out issue and PR templates completely [R1]
4. **Match code style** - Run formatters and linters as specified [R5]
5. **Update documentation** - Keep docs synchronized with code changes [R5]
6. **Write tests** - Meet coverage requirements [R4][R5]
7. **Follow commit conventions** - Use the specified commit message format [R1][R2]
8. **Ask for clarification** - When guidelines are unclear, ask maintainers [R2]

## Content Guidelines

### Writing Style

**Tone and Voice** [R2]:
- Friendly and encouraging
- Direct and concise
- Use active voice
- Avoid jargon without explanation
- Use inclusive language (they/them, contributors, folks)

**Good Example** *(Sample)*:
```markdown
We'd love your help making this project better! Here's how to get started...
```

**Bad Example** *(Sample)*:
```markdown
Contributors must strictly adhere to all rules. Violations will result in rejection.
```

### Formatting and Structure [R5]

- **Use headings** - Clear hierarchy with H2 and H3
- **Use lists** - Bullet points and numbered steps
- **Use code blocks** - Syntax highlighting for commands
- **Use callouts** - Highlight important notes, warnings, tips
- **Use links** - Reference other docs, use full paths [R5]
- **Use examples** - Show good and bad patterns [R5]

**Callout Examples** *(Sample)*:
```markdown
> **Note**: This feature requires Node.js 18 or higher.

> **Warning**: Running this command will delete all local data.

> **Tip**: Use `npm run dev` for faster iteration during development.
```

## GitHub Integration

### Automatic Display

GitHub automatically displays CONTRIBUTING.md in several locations [R1][R3]:

1. **When creating an issue** - Link appears above the issue form
2. **When creating a PR** - Link appears in the PR interface
3. **In repository insights** - Listed in Community Standards
4. **Via .github/ directory** - Place at `.github/CONTRIBUTING.md` for org-wide defaults

### File Placement [R1][R3]

CONTRIBUTING.md can be placed in:
- Repository root: `/CONTRIBUTING.md` (most common)
- Docs folder: `/docs/CONTRIBUTING.md`
- Hidden folder: `/.github/CONTRIBUTING.md` (for organization-wide defaults)

GitHub will find and display it from any of these locations.

### Related Files

CONTRIBUTING.md works alongside [R1][R3]:
- **CODE_OF_CONDUCT.md** - Community behavior standards
- **GOVERNANCE.md** - Decision-making processes
- **SECURITY.md** - Security vulnerability reporting
- **SUPPORT.md** - Where to get help
- **Issue templates** - `.github/ISSUE_TEMPLATE/`
- **PR template** - `.github/PULL_REQUEST_TEMPLATE.md`
- **README.md** - Project overview
- **LICENSE** - Legal terms

## Template for New Projects

```markdown
# Contributing to [Project Name]

Thank you for your interest in contributing to [Project Name]! We welcome contributions
from everyone, whether you're fixing a typo or adding a major feature.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [How to Contribute](#how-to-contribute)
- [Reporting Bugs](#reporting-bugs)
- [Suggesting Features](#suggesting-features)
- [Pull Request Process](#pull-request-process)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Documentation](#documentation)
- [Community](#community)
- [License](#license)

## Code of Conduct

This project adheres to our [Code of Conduct](CODE_OF_CONDUCT.md). By participating,
you are expected to uphold this code.

## Getting Started

### Prerequisites

- [Tool 1] version X.X+
- [Tool 2] version Y.Y+

### Setup

1. Fork the repository
2. Clone your fork: `git clone https://github.com/YOUR-USERNAME/project.git`
3. Create a branch: `git checkout -b feature/your-feature-name`
4. Install dependencies: `[command]`
5. Run tests: `[command]`

## How to Contribute

We accept the following types of contributions:

- 🐛 Bug fixes
- ✨ New features
- 📝 Documentation improvements
- 🎨 Code quality improvements
- 🧪 Test coverage improvements

## Reporting Bugs

Before submitting a bug report:
1. Check [existing issues](link) for duplicates
2. Verify the bug exists in the latest version

When reporting bugs, include:
- Clear title and description
- Steps to reproduce
- Expected vs. actual behavior
- Environment details
- Screenshots or logs

## Suggesting Features

Feature requests are welcome! Please:
1. Check [existing feature requests](link)
2. Explain the use case and benefits
3. Be open to discussion and iteration

## Pull Request Process

1. Create a branch from `main`
2. Make your changes
3. Write/update tests
4. Update documentation
5. Run test suite: `[command]`
6. Run linting: `[command]`
7. Commit using [conventional commits](https://www.conventionalcommits.org/)
8. Push and create a pull request
9. Fill out the PR template completely
10. Request review from maintainers

### PR Checklist

- [ ] Tests pass locally
- [ ] Code follows style guidelines
- [ ] Documentation updated
- [ ] Commit messages follow conventions
- [ ] No merge conflicts

## Coding Standards

- Follow [Style Guide](link)
- Run `[formatter]` before committing
- Maximum line length: [number]
- See [detailed standards](link) for language-specific rules

## Testing

- Write tests for all new features
- Maintain minimum [X]% code coverage
- Run full suite: `[command]`
- See [testing guide](link) for details

## Documentation

- Update README.md for user-facing changes
- Add JSDoc/docstrings for new APIs
- Update CHANGELOG.md in Unreleased section
- Include code examples for new features

## Community

- **Discussions**: [link]
- **Chat**: [link]
- **Email**: [link]

## License

By contributing, you agree that your contributions will be licensed under
the [LICENSE TYPE]. See [LICENSE](LICENSE) for details.
```

## Adoption Checklist

Use this checklist when creating or updating CONTRIBUTING.md [R1][R2][R4]:

**Planning Phase**:
- [ ] Identify all contribution types accepted (code, docs, design, etc.)
- [ ] Document development environment requirements
- [ ] Define coding standards and style guides
- [ ] Establish testing requirements and coverage goals
- [ ] Determine review process and timeline expectations
- [ ] Choose commit message convention
- [ ] Identify community communication channels

**Writing Phase**:
- [ ] Draft welcoming introduction
- [ ] Create step-by-step setup instructions
- [ ] Document bug reporting process
- [ ] Document feature request process
- [ ] Write detailed PR workflow
- [ ] Specify coding standards with examples
- [ ] Explain testing requirements
- [ ] Define documentation requirements
- [ ] List communication channels
- [ ] Reference Code of Conduct
- [ ] Link to LICENSE

**Testing Phase**:
- [ ] Verify setup instructions work for new contributors
- [ ] Test all commands and examples
- [ ] Validate links
- [ ] Review for clarity and completeness
- [ ] Check for inclusive language
- [ ] Get feedback from potential contributors

**Publishing Phase**:
- [ ] Place file in repository root or `.github/`
- [ ] Link from README.md
- [ ] Create issue and PR templates
- [ ] Add to repository Community Standards
- [ ] Announce to community

**Maintenance Phase**:
- [ ] Review quarterly for accuracy
- [ ] Update when processes change
- [ ] Incorporate contributor feedback
- [ ] Keep examples current
- [ ] Validate links regularly

## Common Patterns by Project Type

### Open Source Libraries [R2][R4]

**Focus Areas**:
- API stability and versioning
- Backward compatibility
- Performance considerations
- Cross-platform testing
- Documentation for public APIs
- Semantic versioning practices

**Example Emphasis** *(Sample)*:
```markdown
## API Changes

Breaking changes require:
- Major version bump
- Migration guide
- Deprecation warnings in previous minor version
- 6-month deprecation period
```

### Documentation Projects [R2][R5]

**Focus Areas**:
- Writing style and tone
- Screenshot standards
- Link formatting [R5]
- Example code quality
- Accessibility requirements
- Internationalization

**Example from Dagster** [R5]:
- Minimum image resolution requirements
- Structured callout syntax (notes, warnings, tips)
- Code example referencing from external files
- Front matter configuration standards

### Web Applications [R2][R4]

**Focus Areas**:
- Browser compatibility
- Accessibility (WCAG compliance)
- Performance budgets
- Security considerations
- Design system adherence
- Responsive design testing

### Data Science / Machine Learning [R4]

**Focus Areas**:
- Reproducibility (random seeds, environment specs)
- Dataset documentation
- Model documentation
- Computational requirements
- Notebook standards
- Experiment tracking

## Further Resources

### Official Documentation
- [GitHub Community Guidelines](https://docs.github.com/en/communities) [R1]
- [Open Source Guides](https://opensource.guide/) [R2]
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Keep a Changelog](https://keepachangelog.com/)

### Example CONTRIBUTING.md Files
- [Dagster](https://github.com/dagster-io/dagster/blob/master/docs/CONTRIBUTING.md) [R5]
- [Atom](https://github.com/atom/atom/blob/master/CONTRIBUTING.md)
- [Rails](https://github.com/rails/rails/blob/main/CONTRIBUTING.md)
- [Node.js](https://github.com/nodejs/node/blob/main/CONTRIBUTING.md)
- [TensorFlow](https://github.com/tensorflow/tensorflow/blob/master/CONTRIBUTING.md)

### Related Specifications
- [Code of Conduct (Contributor Covenant)](https://www.contributor-covenant.org/)
- [Developer Certificate of Origin](https://developercertificate.org/)
- [SPDX License List](https://spdx.org/licenses/)

## References

- [R1] GitHub Docs, "Setting guidelines for repository contributors", <https://docs.github.com/en/communities/setting-up-your-project-for-healthy-contributions/setting-guidelines-for-repository-contributors>
- [R2] Mozilla Science Lab, "Contributing Guidelines", <https://mozillascience.github.io/working-open-workshop/contributing/>
- [R3] Wikipedia, "Contributing guidelines", <https://en.wikipedia.org/wiki/Contributing_guidelines>
- [R4] pyOpenSci, "Contributing File Guide", <https://www.pyopensci.org/python-package-guide/documentation/repository-files/contributing-file.html>
- [R5] Dagster, "CONTRIBUTING.md", <https://github.com/dagster-io/dagster/blob/3c2d36054f4014ca8316e533975a538d6eff62c4/docs/CONTRIBUTING.md>

## Update Log

- **2025-10-29**: Initial creation with comprehensive guidelines, examples, and best practices from GitHub, Mozilla, Wikipedia, pyOpenSci, and Dagster [R1-R5]
