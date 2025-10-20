# CHANGELOG.md Guide

> **For Humans**: This guide explains how to maintain a changelog using the Keep a Changelog format for transparent project history.
>
> **For AI Agents**: Use this guide to understand changelog conventions, structure releases properly, and maintain version history that both humans and automation tools can parse.

## Overview

CHANGELOG.md captures all notable changes to a project in a structured, human-readable format following the [Keep a Changelog](https://keepachangelog.com/) convention. This standard:

- Provides clear release history without requiring users to parse commit logs
- Enables optional automation tools like [Openchangelog](https://openchangelog.com/) to publish and distribute updates
- Follows Semantic Versioning (SemVer) principles
- Focuses on user-facing changes rather than internal implementation details

**Note**: Openchangelog is an optional third-party service. The Keep a Changelog format is valuable on its own without any additional tooling.

**Quick Navigation**
- [Core Principles](#core-principles)
- [Best Practices](#best-practices)
- [Writing Good Entries](#writing-good-entries)
- [Workflow Integration](#workflow-integration)
- [Automation Support](#automation-support)
- [Template for New Projects](#template-for-new-projects)
- [Adoption Checklist](#adoption-checklist)
- [Further Resources](#further-resources)
- [Update Log](#update-log)

## Core Principles

### 1. Purpose

A changelog is **for humans, not machines**. Write entries that:

- Explain what changed and why it matters
- Help users understand migration requirements
- Highlight breaking changes, new features, and bug fixes
- Avoid dumping raw commit messages or diffs

### 2. Structure

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- New features coming in the next release

### Changed
- Changes to existing functionality

## [1.0.0] - 2025-01-15

### Added
- Initial stable release
- User authentication system
- Dashboard with real-time metrics

### Fixed
- Database connection timeout issue (#123)

[Unreleased]: https://github.com/user/repo/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/user/repo/releases/tag/v1.0.0
```

### 3. Standard Categories

Use these headings to group changes (in this order):

- **Added** - New features
- **Changed** - Changes to existing functionality
- **Deprecated** - Soon-to-be removed features
- **Removed** - Removed features
- **Fixed** - Bug fixes
- **Security** - Vulnerability fixes and security improvements

Only include categories that have entries. Remove empty sections.

## Best Practices

### For Repository Maintainers

1. **Update continuously** - Add entries to `Unreleased` as work completes, not just at release time
2. **Use plain language** - Describe behavior changes, not code changes
3. **Link to issues/PRs** - Provide context with references like `(#123)` or `([PR#456])`
4. **Date releases** - Use ISO 8601 format: `YYYY-MM-DD`
5. **Version properly** - Follow SemVer (Major.Minor.Patch)
6. **Compare links** - Add comparison URLs to view full diffs between versions

### For AI Agents Reading This

1. **Always update Unreleased** - When making significant changes, add an entry to `## [Unreleased]`
2. **Categorize correctly** - Choose the right heading (Added/Changed/Fixed/etc.)
3. **Write for users** - Focus on impact, not implementation details
4. **Preserve formatting** - Maintain structure, dates, and links
5. **Ask before releasing** - Moving `Unreleased` to a versioned release requires human decision

## Writing Good Entries

### Do ✅ *(Sample)*

```markdown
### Added
- User profile page with avatar upload and bio editing
- API rate limiting to prevent abuse (100 requests/hour per user)
- Dark mode toggle in settings (#234)

### Fixed
- Login form now validates email format before submission (#456)
- Dashboard charts render correctly on mobile devices
- Memory leak in background sync process
```

### Don't ❌ *(Sample)*

```markdown
### Added
- New stuff
- Updated ProfileController.ts
- Commit abc123f

### Fixed
- Bug fixes
- Various improvements
- Refactored code
```

### Example Entry Patterns *(Sample)*

```markdown
### Added
- Feature name with brief description of user benefit
- Integration with ServiceX for automated Y (#PR-number)

### Changed
- Old behavior → New behavior, with migration note if needed
- Improved performance of Z by 50% through caching

### Deprecated
- Feature X will be removed in v3.0.0, use Y instead

### Removed
- Legacy API endpoint `/v1/old-endpoint` (use `/v2/new-endpoint`)

### Fixed
- Component Z no longer crashes when data is empty (#issue-number)
- Race condition in async operation X

### Security
- Updated dependency Y to patch CVE-2024-XXXXX
- Strengthened password hashing to use Argon2id
```

## Workflow Integration

### Development Cycle

```
1. Work on feature/fix
2. Add entry to Unreleased section
3. Commit code + changelog update together
4. Repeat for all changes
```

### Release Cycle

```
1. Review all Unreleased entries
2. Decide version number (SemVer)
3. Create new dated version section
4. Move Unreleased entries to new version
5. Update comparison links
6. Tag release in Git
7. Publish release notes
```

### Example Workflow

```markdown
# Before release
## [Unreleased]
### Added
- User dashboard
### Fixed
- Login bug

# After releasing v1.2.0
## [Unreleased]
(empty or new entries)

## [1.2.0] - 2025-01-20
### Added
- User dashboard
### Fixed
- Login bug
```

## Semantic Versioning (SemVer) Alignment

Match changelog versions to Git tags:

- **MAJOR (1.0.0 → 2.0.0)** - Breaking changes (Removed, major Changed)
- **MINOR (1.0.0 → 1.1.0)** - New features (Added, non-breaking Changed)
- **PATCH (1.0.0 → 1.0.1)** - Bug fixes (Fixed, Security)

Example versioning:

```markdown
## [2.0.0] - 2025-02-01
### Removed
- Legacy authentication API (breaking change)

## [1.3.0] - 2025-01-20
### Added
- Export to PDF feature

## [1.2.1] - 2025-01-15
### Fixed
- Memory leak in data processing
```

## Openchangelog Integration

[Openchangelog](https://openchangelog.com/) auto-publishes changelogs that follow Keep a Changelog format:

1. **Setup** - Point Openchangelog to your repository's `CHANGELOG.md`
2. **Auto-parsing** - Service reads structured sections and categories
3. **Publishing** - Generates hosted changelog site automatically
4. **Updates** - Re-sync after each release to update hosted view

**Requirements for successful parsing:**

- Consistent heading levels (`##` for versions, `###` for categories)
- Standard category names (Added, Changed, etc.)
- Proper date formatting (YYYY-MM-DD)
- Valid Markdown structure

## Integration with Other Conventions

### CHANGELOG.md + AGENTS.md

- AGENTS.md documents PR requirements including changelog updates
- Agents should check AGENTS.md for project-specific changelog policies
- Example: Some projects require changelog entries before PR approval

### CHANGELOG.md + PLANS.md (ExecPlans)

- When closing an ExecPlan, summarize user-facing changes in CHANGELOG.md
- ExecPlans track detailed decisions; changelog distills into user impact
- Link from changelog entry to archived ExecPlan for context

### CHANGELOG.md + SSOT.md

- Use consistent terminology between changelog and SSOT
- When terms evolve, update both documents together
- Changelog may reference SSOT for definition changes

## Common Pitfalls

### ❌ Avoid These Mistakes

1. **Dumping commit logs** - Commits are too granular and technical
2. **Empty releases** - If nothing notable changed, don't create a release
3. **Vague descriptions** - "Various fixes" tells users nothing
4. **Missing dates** - Always include release date in ISO format
5. **Wrong categories** - "Added" for changes, "Fixed" for new features
6. **Huge entries** - Break large releases into sub-items
7. **Internal jargon** - Write for users, not developers

## Automation Support

### For CI/CD Pipelines *(Sample commands—adjust to your tooling)*

```bash
# Validate changelog format
changelog-validator CHANGELOG.md

# Extract version from latest release
VERSION=$(grep -m1 "^## \[" CHANGELOG.md | sed 's/.*\[\(.*\)\].*/\1/')

# Generate release notes from Unreleased section
changelog-extract --unreleased CHANGELOG.md > release-notes.md
```

> **Prerequisites**: `changelog-validator` や `changelog-extract` は外部ツールです。導入していない場合は、`grep` や `sed` など標準コマンドで代替してください。

### For Release Scripts *(Sample commands—adjust to your tooling)*

```bash
# Move Unreleased to new version
changelog-release --version 1.2.0 --date $(date +%Y-%m-%d)

# Update comparison links
changelog-update-links --repo user/repo
```

## Template for New Projects

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Initial project setup

## [0.1.0] - 2025-01-20

### Added
- Basic project structure
- Initial documentation

[Unreleased]: https://github.com/user/repo/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/user/repo/releases/tag/v0.1.0
```

## Adoption Checklist

- [ ] Create `CHANGELOG.md` at repository root
- [ ] Add standard header with Keep a Changelog reference
- [ ] Create `[Unreleased]` section
- [ ] Document current version with date
- [ ] Set up comparison links for versions
- [ ] Update AGENTS.md to require changelog entries in PRs
- [ ] Configure Openchangelog (optional)
- [ ] Train team on writing good entries
- [ ] Add changelog validation to CI (optional)
- [ ] Link from README.md for discoverability

## Further Resources

- [Keep a Changelog 1.1.0](https://keepachangelog.com/en/1.1.0/)
- [Semantic Versioning 2.0.0](https://semver.org/spec/v2.0.0.html)
- [Openchangelog Documentation](https://openchangelog.com/docs)
- [Common Changelog](https://common-changelog.org/) - Alternative format

## Update Log

- 2025-10-20: 初版を公開（Keep a Changelog 1.1.0とコミュニティ運用例を整理）

---

**Remember**: A good changelog respects your users' time by clearly communicating what changed and why they should care.
