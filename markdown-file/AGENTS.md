# AGENTS.md Guide

> **For Humans**: This guide explains the AGENTS.md convention for structuring AI-agent-friendly documentation in your repositories.
>
> **For AI Agents**: AGENTS.md provides machine-focused operational knowledge complementing human-facing documentation. Read this to understand project conventions, development workflows, and automation patterns.

## Overview

AGENTS.md is a Markdown convention introduced by OpenAI and partner teams to provide AI coding agents with a predictable, machine-focused playbook. It is increasingly adopted across the AI tooling community and is recognized by major agent platforms including:

- OpenAI Codex
- Cursor
- VS Code with AI extensions
- And other AI-assisted development tools

**Key Purpose**: Give AI agents the operational knowledge they need to edit and validate code safely without relying solely on human-facing documentation.

## Core Principles

### 1. Placement & Scope

- Place `AGENTS.md` at the repository root
- Large monorepos may include nested `AGENTS.md` files for specific subsystems
- Agents read the closest (most specific) instructions when multiple files exist
- Explicit user prompts override file guidance

### 2. Content Structure

AGENTS.md typically includes sections covering:

- **Development environment setup** - Tools, dependencies, workspace configuration
- **Build & test commands** - How to compile, run tests, validate changes
- **Linting & formatting** - Code style enforcement, quality checks
- **Security policies** - Credential handling, secret management, security scanning
- **PR & commit policies** - Contribution workflow, review requirements

### 3. Machine-First, Human-Readable

- Write clear, imperative instructions
- Use plain language while being technically precise
- Include concrete commands and examples
- Focus on "what" and "how" over "why" (save philosophy for README.md)

## Standard Template

```markdown
# AGENTS.md

## Dev Environment Tips
- List required tools, versions, package managers
- Document workspace setup commands
- Explain how to navigate the codebase structure
- Note any non-obvious configuration requirements

## Testing Instructions
- Where to find test files and CI configuration
- Commands to run full test suite
- How to run specific test subsets
- Requirements before merging (all tests passing, coverage thresholds, etc.)
- Expectations around adding/updating tests with code changes

## Build & Deployment
- Build commands for different environments
- How to verify successful builds
- Deployment procedures (if applicable)
- Artifact locations and validation

## Linting & Code Quality
- Linter configuration and commands
- Formatting tools and auto-fix commands
- Type checking requirements
- When to run quality checks (pre-commit, pre-PR, etc.)

## PR Instructions
- PR title/description format
- Required checks before submitting
- Review process expectations
- Branch naming conventions
- Commit message guidelines

## Security & Credentials
- How to handle secrets and credentials
- Security scanning tools and requirements
- Dependency vulnerability checks
- Prohibited practices
```

## Best Practices

### For Repository Maintainers

1. **Keep it updated** - AGENTS.md should reflect current practices
2. **Be specific** - Include exact commands, not just concepts
3. **Test instructions** - Verify that agents can successfully follow the guidance
4. **Link to details** - Reference deeper documentation when needed
5. **Version control** - Track changes to understand evolution of practices

### For AI Agents Reading This

1. **Prioritize AGENTS.md** over general documentation when performing coding tasks
2. **Check for nested files** in subdirectories of large projects
3. **Follow instructions literally** unless explicitly overridden by the user
4. **Report ambiguities** back to users rather than guessing
5. **Reference specific sections** when explaining your actions

## Sample Workflow: pnpm + Turborepo + Vite

```markdown
## Dev Environment Tips
- Use `pnpm dlx turbo run where <project_name>` to jump to a package instead of scanning with `ls`.
- Run `pnpm install --filter <project_name>` to add the package to your workspace so Vite, ESLint, and TypeScript can see it.
- Use `pnpm create vite@latest <project_name> -- --template react-ts` to spin up a new React + Vite package with TypeScript checks ready.
- Check the name field inside each package's package.json to confirm the right name—skip the top-level one.

## Testing Instructions
- Find the CI plan in the .github/workflows folder.
- Run `pnpm turbo run test --filter <project_name>` to run every check defined for that package.
- From the package root you can just call `pnpm test`. The commit should pass all tests before you merge.
- To focus on one step, add the Vitest pattern: `pnpm vitest run -t "<test name>"`.
- Fix any test or type errors until the whole suite is green.
- After moving files or changing imports, run `pnpm lint --filter <project_name>` to be sure ESLint and TypeScript rules still pass.
- Add or update tests for the code you change, even if nobody asked.

## PR Instructions
- Title format: [<project_name>] <Title>
- Always run `pnpm lint` and `pnpm test` before committing.
- Once tests pass, run `pnpm lint` from the repo root in case there's shared tooling.
- List every change in your PR description with checkboxes, calling out the tests you ran plus any follow-up work.
- Ask for review by tagging your team's Slack or GitHub handle. Include context for risky changes (e.g., migrations, rewrites).
- Don't merge when CI is red. Leave review feedback in the PR so others know the current status.
- Update documentation and changelog entries as necessary.
```

> **Note**: Treat this as a sample. Replace commands, tooling, and review policies with the specifics of your repository.

## Integration with Other Conventions

### AGENTS.md + SSOT.md

- Use AGENTS.md for operational procedures
- Use SSOT.md for canonical data definitions and terminology
- Cross-reference when procedural steps depend on canonical definitions

### AGENTS.md + CHANGELOG.md

- AGENTS.md explains *how* to contribute
- CHANGELOG.md tracks *what* was contributed
- Keep PR instructions in AGENTS.md aligned with changelog update requirements

### AGENTS.md + PLANS.md (ExecPlans)

- AGENTS.md provides standing procedures
- ExecPlans document specific multi-step initiatives
- ExecPlans should reference relevant AGENTS.md sections for standard practices

## Tool Support

### OpenAI Code Generation Models
The latest OpenAI code generation models (e.g., GPT-4.1 Code, GPT-4.1 Mini) can rely on AGENTS.md to understand repository-specific procedures. Review each model’s documentation to confirm the most recent behavior.

### Cursor
Cursor treats AGENTS.md as the project-specific workflow guide.

### VS Code AI Extensions
AI-assisted extensions for VS Code increasingly ingest AGENTS.md as contextual guidance.

### Custom Agents
For bespoke agents, implement an AGENTS.md parser so they inherit standard operating procedures.

> **Support Note**: Compatibility may change—review each tool’s release notes regularly.

## Conflict Resolution

When guidance conflicts arise:

1. **Most specific wins** - Nested AGENTS.md overrides root AGENTS.md
2. **User commands override** - Explicit user instructions take precedence over file guidance
3. **Newer over older** - Within the same file, later sections clarify earlier ones
4. **Explicit over implicit** - Direct commands override general principles

## Adoption Checklist

- [ ] Create `AGENTS.md` at repository root
- [ ] Document development environment setup
- [ ] Specify test execution and validation procedures
- [ ] Define PR and commit requirements
- [ ] Include linting and code quality standards
- [ ] Add security and credential handling policies
- [ ] Review and update with team
- [ ] Link from README.md for human discoverability
- [ ] Test with AI agents to verify clarity
- [ ] Establish regular review cadence (e.g., quarterly)

## Community & Standards

- **Specification**: Maintained openly on GitHub (MIT License)
- **Discussion**: Active community feedback on scope and evolution
- **Vendor Neutrality**: Designed to work across AI platforms
- **Evolution**: Ongoing refinement based on real-world usage

## Further Resources

- [OpenAI AGENTS.md Specification](https://agents.md)
- [GitHub Repository](https://github.com/openai/agents.md)
- Growing collection of community examples
- Industry coverage and adoption case studies

## Update Log

- 2025-10-20: Published the initial edition (reassembled from community practices and the public specification).

---

**Remember**: AGENTS.md is about making AI agents effective collaborators. Keep instructions clear, commands concrete, and guidance current.
