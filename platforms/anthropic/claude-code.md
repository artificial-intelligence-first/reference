---
title: Claude Code
slug: claude-code
status: living
last_updated: 2025-10-24
tags: [code-generation, agents, anthropic, cli, best-practices, vscode]
summary: "Agentic coding tool from Anthropic that lives in your terminal, understands your codebase, and helps you code faster through natural language commands."
sources:
  - { id: R1, title: "Claude Code Official Site", url: "https://www.anthropic.com/claude-code", accessed: "2025-10-24" }
  - { id: R2, title: "Claude Code GitHub Repository", url: "https://github.com/anthropics/claude-code", accessed: "2025-10-24" }
  - { id: R3, title: "Anthropic Cookbook", url: "https://github.com/anthropics/anthropic-cookbook", accessed: "2025-10-24" }
  - { id: R4, title: "Claude Code Best Practices", url: "https://www.anthropic.com/engineering/claude-code-best-practices", accessed: "2025-10-24" }
  - { id: R5, title: "Enabling Claude Code to work more autonomously", url: "https://www.anthropic.com/news/enabling-claude-code-to-work-more-autonomously", accessed: "2025-10-24" }
---

# Claude Code

> **For Humans**
> This document explains Claude Code, Anthropic's agentic coding tool that works in your terminal and browser. Use this when building AI-assisted development workflows, automating routine coding tasks, or integrating Claude into your development environment.

> **For AI Agents**
> This document defines Claude Code capabilities, integration patterns, and best practices. Apply these patterns when implementing AI-assisted coding workflows, automating development tasks, or extending Claude Code with plugins and MCP servers. Cross-reference with AGENTS.md for operational procedures.

---

## Table of Contents

- [TL;DR](#tldr)
- [Canonical Definitions](#canonical-definitions)
- [Core Patterns](#core-patterns)
- [Advanced Features (September 2025)](#advanced-features-september-2025)
- [Decision Checklist](#decision-checklist)
- [Anti-patterns / Pitfalls](#anti-patterns--pitfalls)
- [Evaluation](#evaluation)
- [Update Log](#update-log)
- [See Also](#see-also)
- [References](#references)

---

## TL;DR

- **What**: Agentic coding tool that lives in your terminal (CLI) and browser (Web), understands codebases, and executes development tasks through natural language
- **Why**: Accelerates development by automating routine tasks, explaining complex code, handling Git workflows, and integrating with existing tools
- **When**: Building features, refactoring code, debugging, writing tests, managing Git workflows, CI/CD automation, code review
- **How**: Install via npm, run in terminal alongside your IDE, use natural language commands, extend with plugins and MCP servers
- **Watch out**: Claude Code can directly edit files and run commands—review changes before committing, especially in production environments

---

## Canonical Definitions

### Claude Code

**Definition**: An agentic coding tool developed by Anthropic that operates in your terminal and browser, understanding your codebase and assisting with development tasks through natural language commands [R1][R2].

**Scope**:
- **Includes**: Code generation and editing, Git workflows, command execution, file operations, GitHub integration, plugin system, MCP integration, CI/CD automation (GitHub Actions), headless mode
- **Excludes**: IDE replacement, direct model training, non-development tasks

**Platforms**:
- **CLI**: Terminal-based interface (macOS, Linux, Windows)
- **Web**: Browser-based interface at claude.ai/code (October 2025)
- **Mobile**: iOS app preview (October 2025)

**Related Concepts**:
- **Similar**: GitHub Copilot, Cursor, Aider
- **Complementary**: MCP (Model Context Protocol) for tool integration
- **Built with**: 90% of Claude Code product written by Anthropic's AI models [R1]

**Sources**: [R1][R2]

### Slash Commands

**Definition**: Reusable prompt templates stored as Markdown files in `.claude/commands/` directory, accessible through the `/` menu for repeated workflows [R2].

**Scope**: Custom commands for debugging, log analysis, code review, and team-specific workflows.

**Example**:
```markdown
<!-- .claude/commands/review.md -->
# Code Review

Review the current changes for:
- Code quality and style
- Potential bugs or edge cases
- Performance implications
- Security concerns

Provide specific suggestions for improvement.
```

**Sources**: [R2]

### Headless Mode

**Definition**: Non-interactive mode for running Claude Code in CI/CD pipelines, pre-commit hooks, build scripts, and automation contexts [R2].

**Scope**: Enables programmatic usage without terminal interaction, with streaming JSON output for parsing.

**Example**:
```bash
claude-code -p "Fix all linting errors" --output-format stream-json
```

**Sources**: [R2]

---

## Core Patterns

### Pattern 1: Natural Language Development Workflow

**Intent**: Use natural language to describe development tasks and let Claude Code execute them autonomously.

**Context**: When you need to implement features, refactor code, or automate routine development tasks.

**Implementation**:

```bash
# Install Claude Code
npm install -g @anthropic-ai/claude-code

# Set API key
export ANTHROPIC_API_KEY=your-api-key

# Start Claude Code in your project
cd /path/to/your/project
claude-code

# Example natural language commands:
# "Add a new API endpoint for user authentication"
# "Refactor the payment processing module to use async/await"
# "Write unit tests for the UserService class"
# "Fix the bug where dates are displayed in wrong timezone"
# "Create a GitHub PR with my changes and a descriptive commit message"
```

**Key Principles**:
- Claude Code understands your codebase structure and conventions [R1]
- Operates alongside your IDE without requiring workflow changes [R1]
- Directly edits files, runs commands, and creates commits [R2]
- Uses Git, MCP servers, and command-line tools to extend capabilities [R2]

**Trade-offs**:
- ✅ **Advantages**: Fast iteration, natural language interface, autonomous execution, Git integration
- ⚠️ **Disadvantages**: Requires API access (Claude Pro/Max/Team/Enterprise), review needed before committing changes
- 💡 **Alternatives**: Use IDE-integrated assistants for line-by-line suggestions, or use Claude API directly for custom workflows

**Sources**: [R1][R2]

### Pattern 2: Custom Slash Commands for Repeated Workflows

**Intent**: Create reusable command templates for common development tasks across your team.

**Context**: When you have repeated workflows like debugging, log analysis, code review, or deployment procedures.

**Implementation**:

```bash
# Create .claude/commands directory
mkdir -p .claude/commands

# Create custom command: .claude/commands/debug-api.md
cat > .claude/commands/debug-api.md << 'EOF'
# Debug API Issues

Analyze the API logs and identify issues:

1. Check the latest error logs in `logs/api.log`
2. Identify the failing endpoint and error patterns
3. Review the relevant controller and service code
4. Suggest fixes with code examples
5. Write a test to prevent regression

Focus on errors from the last hour.
EOF

# Check into Git for team sharing
git add .claude/commands/
git commit -m "Add debug-api slash command"
```

**Usage**:
```bash
# In Claude Code, type "/" to see available commands
claude-code

# In the prompt, type:
/debug-api
```

**Key Principles**:
- Store commands in `.claude/commands/` as Markdown files [R2]
- Commands become available in slash menu when you type `/` [R2]
- Check commands into Git for team-wide availability [R2]
- Use clear, descriptive names and detailed instructions

**Trade-offs**:
- ✅ **Advantages**: Reusable workflows, team standardization, version-controlled procedures
- ⚠️ **Disadvantages**: Requires Markdown authoring, maintenance overhead for complex commands
- 💡 **Alternatives**: Use shell scripts or Make targets for non-AI automation

**Sources**: [R2]

### Pattern 3: MCP Integration for Extended Capabilities

**Intent**: Connect Claude Code to external tools and services via Model Context Protocol (MCP) servers.

**Context**: When you need to access databases, APIs, search engines, or other external resources during development.

**Implementation**:

```json
// claude_desktop_config.json or claude_code_config.json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_..."
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgresql://..."
      }
    }
  }
}
```

**Usage**:
```bash
# Claude Code can now use MCP tools
claude-code

# Example prompts:
# "List all open GitHub issues labeled 'bug'"
# "Query the users table and show recent signups"
# "Search the documentation for authentication examples"
```

**Key Principles**:
- Claude Code functions as both MCP server and client [R2]
- Connect to any MCP server for tool access [R2]
- Configure servers in `claude_code_config.json`
- Tools become available automatically in Claude Code

**Trade-offs**:
- ✅ **Advantages**: Extensible tool access, standardized protocol, broad ecosystem
- ⚠️ **Disadvantages**: Requires MCP server setup, credential management
- 💡 **Alternatives**: Use GitHub CLI directly or implement custom tool calling

**Sources**: [R2]

### Pattern 4: GitHub Actions for Automated Code Assistance

**Intent**: Automate code review, issue triage, and implementation using Claude Code in GitHub workflows.

**Context**: When you want AI assistance on pull requests, issue responses, or automated code fixes.

**Implementation**:

```yaml
# .github/workflows/claude-code-review.yml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]
  issue_comment:
    types: [created]

jobs:
  claude-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Claude Code Review
        uses: anthropics/claude-code-action@v1
        with:
          anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
          mode: auto  # 'auto' | 'interactive' | 'review' | 'implement'

          # Optional: Specific prompt for custom behavior
          # prompt: "Review this PR for security issues and performance"
```

**Modes**:
- **auto**: Intelligently detects when to activate (e.g., @claude mentions)
- **interactive**: Code assistant responding to comments
- **review**: Analyzes code changes and provides feedback
- **implement**: Implements fixes or features from issue descriptions

**Key Principles**:
- Supports multiple authentication methods (Anthropic API, AWS Bedrock, Google Vertex AI) [R2]
- Intelligent context detection for activation [R2]
- Integrates with GitHub PR/issue workflows [R2]
- Can create commits and push changes

**Trade-offs**:
- ✅ **Advantages**: Automated code review, faster issue resolution, consistent analysis
- ⚠️ **Disadvantages**: API costs for CI runs, requires careful permission scoping
- 💡 **Alternatives**: Manual code review or traditional linting/static analysis tools

**Sources**: [R2]

### Pattern 5: Headless Mode for CI/CD Integration

**Intent**: Run Claude Code non-interactively in automated pipelines, pre-commit hooks, and build scripts.

**Context**: When you need AI-powered code transformations or analysis in automated workflows without terminal interaction.

**Implementation**:

```bash
# Pre-commit hook: .git/hooks/pre-commit
#!/bin/bash

# Run Claude Code in headless mode to fix linting issues
claude-code -p "Fix all eslint errors in staged files" \
  --output-format stream-json \
  > /tmp/claude-output.json

# Check if Claude made changes
if git diff --quiet; then
  echo "No linting issues found"
else
  echo "Claude Code fixed linting issues. Review changes:"
  git diff
  exit 1  # Prevent commit to allow review
fi
```

**CI/CD Pipeline**:
```yaml
# .github/workflows/ci.yml
- name: Auto-fix linting issues
  run: |
    claude-code -p "Fix all linting errors and format code" \
      --output-format stream-json

    if ! git diff --quiet; then
      git config user.name "Claude Code Bot"
      git config user.email "bot@example.com"
      git add .
      git commit -m "fix: auto-fix linting issues [claude-code]"
      git push
    fi
```

**Key Principles**:
- Use `-p` flag for non-interactive prompts [R2]
- Use `--output-format stream-json` for machine-readable output [R2]
- Review changes before committing to main branches
- Set appropriate timeouts for long-running tasks

**Trade-offs**:
- ✅ **Advantages**: Automated fixes, consistent formatting, reduced manual work
- ⚠️ **Disadvantages**: Potential for incorrect changes, API usage costs
- 💡 **Alternatives**: Traditional linters and formatters (ESLint, Prettier, Black)

**Sources**: [R2]

---

## Advanced Features (September 2025)

### Checkpoints: Autonomous Operation with Safety [R5]

Claude Code now automatically saves code state before each change, enabling instant rollback to previous versions.

**Key Capabilities**:
- **Automatic state saving**: Every change creates a checkpoint
- **Quick rollback**: Press Esc twice or use `/rewind` command
- **Safe experimentation**: Try changes without fear of breaking code

**Usage**:
```bash
# Claude Code automatically creates checkpoints

# To rewind to previous state:
# Press: Esc + Esc
# Or type: /rewind

# To rewind to specific checkpoint:
/rewind 3  # Go back 3 changes
```

**Benefits**:
- Enables more autonomous operation
- Reduces risk of irreversible changes
- Faster iteration cycles

**Sources**: [R5]

### VS Code Extension [R5]

Native VS Code extension brings Claude Code directly into your IDE.

**Features**:
- Integrated Claude Code interface within VS Code
- Access to full Claude Code capabilities
- Seamless workflow without switching contexts

**Installation**:
```bash
# Install from VS Code marketplace
code --install-extension anthropic.claude-code
```

**Sources**: [R5]

### Terminal v2.0 [R5]

Updated terminal interface with improved usability and features.

**New Features**:
- **Improved status visibility**: Clearer indication of Claude Code's current state
- **Searchable prompt history**: Press Ctrl+r to search previous commands
- **Better error messages**: More informative feedback

**Usage**:
```bash
# Search command history
Ctrl+r  # then type to search

# Navigate through history
Up/Down arrows
```

**Sources**: [R5]

### Plugin System [R5]

Extend Claude Code with custom commands, agents, hooks, and MCP servers.

**Capabilities**:
- **Custom commands**: Add domain-specific workflows
- **Custom agents**: Build specialized AI assistants
- **Hooks**: Intercept and modify Claude Code behavior
- **MCP servers**: Connect to external tools and services

**Example Plugin Structure**:
```
.claude/
├── commands/          # Custom slash commands
├── plugins/           # Plugin modules
│   ├── my-plugin/
│   │   ├── config.yml
│   │   └── index.js
└── mcp-servers/       # MCP server configurations
```

**Sources**: [R5]

### Default Model: Claude Sonnet 4.5 [R5]

Claude Sonnet 4.5 is now the default model for Claude Code, offering improved:
- Code understanding and generation
- Multi-file refactoring capabilities
- Context awareness across large codebases

**Sources**: [R5]

---

## Decision Checklist

Use Claude Code when:

- [ ] **Development Acceleration**: You need to speed up routine coding tasks (boilerplate, refactoring, tests) [R1]
  - Verify: Tasks are repetitive or time-consuming
  - Impact: Manual coding remains slower and more error-prone

- [ ] **Codebase Understanding**: You need to understand or explain complex code quickly [R1]
  - Verify: Large or unfamiliar codebase
  - Impact: Manual exploration takes significant time

- [ ] **Git Workflow Integration**: You need AI assistance with commits, PRs, and code review [R2]
  - Verify: Team uses GitHub/Git for version control
  - Impact: Manual Git operations remain time-consuming

- [ ] **API Access**: You have Claude Pro, Max, Team, Enterprise, or Console account [R1]
  - Verify: Active subscription with API access
  - Impact: Cannot use Claude Code without API access

- [ ] **CI/CD Automation**: You want to automate code fixes or analysis in pipelines [R2]
  - Verify: Existing CI/CD infrastructure
  - Impact: Manual review remains necessary without automation

**Decision Matrix**:

| Scenario | Use Claude Code | Alternative | Reason |
|----------|----------------|-------------|--------|
| Writing new features | ✅ Yes | Manual coding | Faster iteration with natural language |
| Line-by-line suggestions | ⚠️ Optional | GitHub Copilot, IDE plugins | IDE plugins may be faster for completions |
| Simple refactoring | ✅ Yes | Manual or IDE refactoring | Claude Code handles complex multi-file changes |
| Code review | ✅ Yes | Manual review | Automated analysis with GitHub Actions |
| Quick bug fixes | ✅ Yes | Manual debugging | Claude Code can identify and fix issues |
| Production-critical code | ⚠️ Caution | Manual with review | Always review AI-generated changes carefully |

---

## Anti-patterns / Pitfalls

### Anti-pattern 1: Committing Without Review

**Symptom**: Accepting all Claude Code changes without review, leading to bugs or incorrect implementations.

**Why It Happens**: Trust in AI capabilities, time pressure, assumption that AI is always correct.

**Impact**:
- Bugs introduced into codebase
- Security vulnerabilities
- Breaking changes deployed
- Loss of team knowledge and context

**Solution**: Always review Claude Code changes before committing, especially for production code [R4].

**Example**:

```bash
# ❌ Anti-pattern: Blind commit
claude-code
# After Claude makes changes:
git add .
git commit -m "Claude Code changes"  # No review!
git push

# ✅ Correct pattern: Review first
claude-code
# After Claude makes changes:
git diff  # Review all changes
git add -p  # Stage changes interactively
# Review staged changes again
git commit -m "feat: add user authentication endpoint

Implemented by Claude Code with following changes:
- Added POST /api/auth/login endpoint
- Created AuthService with JWT token generation
- Added unit tests for authentication flow

Reviewed and verified implementation."
```

**Sources**: [R4]

### Anti-pattern 2: Vague or Ambiguous Prompts

**Symptom**: Claude Code produces incorrect or incomplete results because instructions are unclear.

**Why It Happens**: Assuming AI will infer intent, rushing through prompts, not providing enough context.

**Impact**:
- Wasted time on incorrect implementations
- Multiple iterations needed
- Frustration with tool effectiveness

**Solution**: Provide clear, specific, and contextual prompts with examples and constraints [R3][R4].

**Example**:

```bash
# ❌ Anti-pattern: Vague prompt
"Make the code better"

# ✅ Correct pattern: Specific prompt
"Refactor the UserController.handleLogin method to:
1. Use async/await instead of promises
2. Add input validation for email and password
3. Return appropriate HTTP status codes (200, 400, 401, 500)
4. Add error logging
5. Follow the existing code style in this project

Context: This is part of our Express.js API and should match
the patterns used in OrderController.ts"
```

**Sources**: [R3][R4]

### Anti-pattern 3: Ignoring Model Context Limits

**Symptom**: Claude Code fails or produces incomplete results when working with very large codebases.

**Why It Happens**: Not understanding token/context limits, attempting to process entire codebases at once.

**Impact**:
- Incomplete analysis or generation
- Errors or timeouts
- Poor quality outputs

**Solution**: Break large tasks into smaller chunks, use specific file paths, leverage MCP for external context [R2][R3].

**Example**:

```bash
# ❌ Anti-pattern: Unbounded context
"Refactor the entire application to use TypeScript"

# ✅ Correct pattern: Chunked approach
"Refactor the following files to TypeScript:
1. src/controllers/UserController.js
2. src/services/UserService.js
3. Update imports in related files

After completion, we'll move to the next module."
```

**Sources**: [R2][R3]

### Anti-pattern 4: Using Claude Code as Only Testing Method

**Symptom**: Relying solely on Claude Code for testing without manual QA or test suites.

**Why It Happens**: Over-confidence in AI testing capabilities, desire to save time.

**Impact**:
- Edge cases missed
- Integration issues not caught
- Production bugs
- Lack of regression prevention

**Solution**: Use Claude Code to generate tests, but maintain comprehensive test suites and manual QA [R2][R4].

**Example**:

```bash
# ❌ Anti-pattern: AI testing only
claude-code
"Test this feature"  # No actual test suite created

# ✅ Correct pattern: Generate test suite
claude-code
"Create a comprehensive Jest test suite for UserService:
- Unit tests for all public methods
- Test success and error cases
- Mock external dependencies
- Aim for >90% code coverage
- Follow existing test patterns in tests/services/

Then run the tests and fix any failures."

# Follow up with:
npm test  # Verify tests pass
# Manual QA for UI/UX
# Integration testing
```

**Sources**: [R2][R4]

---

## Evaluation

### Metrics

**Primary Metrics**:
- **Development Velocity**: Time saved on routine tasks
  - Target: 30-50% reduction in boilerplate, refactoring, and test writing time
  - Measurement: Compare time with/without Claude Code for similar tasks

- **Code Quality**: Defect rate and code review feedback
  - Target: Maintain or improve existing quality standards
  - Measurement: Track bugs introduced, code review comments, test coverage

- **Adoption Rate**: Developer usage and satisfaction
  - Target: >70% of team actively using Claude Code
  - Measurement: Usage logs, developer surveys

**Secondary Metrics**:
- API usage and cost
- Time to onboard new team members
- Documentation quality improvement
- Git workflow efficiency (commits, PRs)

### Testing Strategies

**Unit Tests**:
```bash
# Generate tests with Claude Code
claude-code
"Create unit tests for src/services/PaymentService.ts:
- Test all payment methods (credit card, debit, PayPal)
- Test error handling (invalid card, insufficient funds, network errors)
- Mock Stripe API calls
- Achieve >90% coverage"

# Verify tests
npm test -- PaymentService.test.ts
npm run coverage
```

**Integration Tests**:
```bash
# Generate integration tests
claude-code
"Create integration tests for the authentication flow:
1. User registration endpoint
2. Email verification
3. Login with verified account
4. Access protected resource with JWT
5. Token refresh flow

Use Supertest for API testing and seed test database."
```

**Code Review**:
```bash
# Use GitHub Action for automated review
# See Pattern 4: GitHub Actions

# Manual review checklist:
git diff main...feature-branch
# Check:
# - Correctness of implementation
# - Edge case handling
# - Security implications
# - Performance impact
# - Test coverage
```

### Success Criteria

- [ ] Claude Code used for >50% of routine development tasks
- [ ] Code quality metrics maintained or improved
- [ ] Developer satisfaction score >4/5
- [ ] Time to implement features reduced by >30%
- [ ] Test coverage increased or maintained
- [ ] Zero critical bugs introduced by AI-generated code (with proper review)

**Sources**: [R1][R2][R4]

---

## Update Log

### 2025-10-24
- **Added**: September 2025 autonomous operation features: Checkpoints, VS Code extension, Terminal v2.0, and plugin system details
- **Updated**: Default model information (Claude Sonnet 4.5)
- **Sources**: [R5]

### 2025-10-23
- **Initial version**: Comprehensive Claude Code documentation
- **Added**: Canonical definitions for Claude Code, Slash Commands, and Headless Mode
- **Added**: Core patterns for natural language development, custom commands, MCP integration, GitHub Actions, and CI/CD
- **Added**: Decision checklist, anti-patterns, and evaluation criteria
- **Included**: October 2025 web and mobile app launch information
- **Sources**: [R1][R2][R3][R4]

---

## See Also

### Prerequisites
- [AGENTS.md](../../files/AGENTS.md): Operational documentation for AI agents
- [SKILL.md](../../files/SKILL.md): Agent Skills specification for packaging domain expertise

### Related Topics
- [Context Engineering](../../engineering/context.md): Managing context in AI interactions
- [Prompt Engineering](../../engineering/prompt.md): Effective prompt design patterns

### Related Protocols
- **Model Context Protocol (MCP)**: For connecting Claude Code to external tools and services
- **Agent2Agent (A2A)**: For multi-agent communication (see [platforms/google/a2a.md](../google/a2a.md))

### Platform-Specific
- **Anthropic Cookbook**: Code examples and patterns at https://github.com/anthropics/anthropic-cookbook
- **Claude API**: Direct API access for custom integrations
- **Claude Models**: Opus 4.1, Sonnet 4.5, Haiku 3.5 compatibility

---

## References

- [R1] Anthropic. "Claude Code Official Site." Anthropic. https://www.anthropic.com/claude-code (accessed 2025-10-23)
- [R2] Anthropic. "Claude Code GitHub Repository." GitHub. https://github.com/anthropics/claude-code (accessed 2025-10-23)
- [R3] Anthropic. "Anthropic Cookbook." GitHub. https://github.com/anthropics/anthropic-cookbook (accessed 2025-10-23)
- [R4] Anthropic. "Claude Code Best Practices." Anthropic Engineering Blog. https://www.anthropic.com/engineering/claude-code-best-practices (accessed 2025-10-23)

---

**Document ID**: `platforms/anthropic/claude-code`
**Canonical URL**: `https://github.com/artificial-intelligence-first/ssot/blob/main/platforms/anthropic/claude-code.md`
**License**: MIT
