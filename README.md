# AI-First Development Reference (SSOT)

[![Link Check](https://github.com/artificial-intelligence-first/ssot/actions/workflows/linkcheck.yml/badge.svg)](https://github.com/artificial-intelligence-first/ssot/actions/workflows/linkcheck.yml)
[![Markdown Lint](https://github.com/artificial-intelligence-first/ssot/actions/workflows/markdownlint.yml/badge.svg)](https://github.com/artificial-intelligence-first/ssot/actions/workflows/markdownlint.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> **For Humans**: This repository contains best practices and conventions for working effectively with AI coding assistants. Use these guides to structure your projects for maximum AI productivity.
>
> **For AI Agents**: This repository contains canonical reference documentation for AI-first development patterns. Read these guides to understand how to structure work, maintain documentation, and collaborate effectively with human developers. When asked to update topics, follow the governance documents in `_meta/`.

---

## Vision

A **Single Source of Truth (SSOT)** for AI-first development knowledge.

---

## Table of Contents

- [Repository Structure](#repository-structure)
- [Core Files](#core-files)
- [Engineering Guides](#engineering-guides)
- [Tools & Protocols](#tools--protocols)
- [Platform References](#platform-references)
- [System Tools](#system-tools)
- [Governance](#governance)
- [Quick Start](#quick-start)
- [How to Contribute](#how-to-contribute)
- [When to Use Each Guide](#when-to-use-each-guide)
- [Integration Patterns](#integration-patterns)
- [License](#license)

---

## Repository Structure

```
ssot/
├── README.md                          # This file - repository overview
├── files/                             # Core AI-first development files
│   ├── README.md                      # Files directory guide
│   ├── AGENTS.md                      # AI agent operational documentation
│   ├── CHANGELOG.md                   # Version history format
│   ├── PLANS.md                       # ExecPlans for complex tasks
│   ├── SKILL.md                       # Agent Skills specification
│   └── SSOT.md                        # Single Source of Truth principles
├── engineering/                       # Engineering methodologies
│   ├── README.md                      # Engineering guides overview
│   ├── context.md                     # Context engineering patterns
│   └── prompt.md                      # Prompt engineering patterns
├── tools/                             # AI development tools & protocols
│   ├── README.md                      # Tools and protocols overview
│   └── mcp.md                         # Model Context Protocol guide
├── platforms/                         # Platform-specific implementations
│   ├── README.md                      # Platform guides overview
│   ├── openai/                        # OpenAI-specific guides
│   │   ├── agents-sdk.md              # Responses API patterns
│   │   ├── agent-kit.md               # AgentKit framework concepts
│   │   └── codex.md                   # Code generation patterns
│   ├── anthropic/                     # Anthropic-specific guides
│   │   ├── claude-code.md             # Claude Code agentic tool
│   │   └── claude-agent-sdk.md        # Claude Agent SDK
│   ├── google/                        # Google-specific guides
│   │   └── a2a.md                     # Agent2Agent protocol
│   └── langchain/                     # LangChain-specific guides
│       ├── langchain.md               # LangChain framework
│       └── langgraph.md               # LangGraph orchestration
├── system/                            # System tools & utilities
│   ├── README.md                      # System tools overview
│   └── git/                           # Git version control
│       └── worktree.md                # Git worktree patterns
├── _meta/                             # Governance & style guides
│   ├── CONTRIBUTING.md                # How to add/update content
│   ├── STYLE.md                       # Writing conventions
│   └── TAXONOMY.md                    # Canonical tag vocabulary
├── _templates/                        # Templates for new content
│   ├── TOPIC_TEMPLATE.md              # New topic template
│   └── SECTION_TEMPLATE.md            # Section templates
└── .github/                           # GitHub automation
    ├── workflows/                     # CI/CD pipelines
    │   ├── markdownlint.yml           # Markdown quality checks
    │   └── linkcheck.yml              # Broken link detection
    ├── ISSUE_TEMPLATE/                # Issue templates
    │   ├── topic_update.md            # Request topic updates
    │   └── new_topic.md               # Propose new topics
    └── PULL_REQUEST_TEMPLATE.md       # PR checklist
```

**Design Philosophy**:
- **`files/`** - Core AI-first development file conventions (1 topic = 1 file)
- **`engineering/`** - Deep technical methodologies (context, prompting)
- **`tools/`** - Cross-platform tools and communication protocols
- **`platforms/`** - Vendor-specific implementations (OpenAI, Anthropic, Google, LangChain)
- **`system/`** - System tools and utilities (Git, Docker, build tools)
- **`_meta/`** - How to maintain this repository
- **`_templates/`** - Scaffolding for new content

---

## Core Files

### [AGENTS.md](files/AGENTS.md)
**OpenAI AGENTS.md convention for AI-agent-friendly operational documentation**

- Machine-focused operational procedures
- Development environment setup
- Testing, linting, and PR workflows
- Security policies and best practices
- Cross-platform agent compatibility

**Use when**: Setting up project conventions for AI agents, defining development workflows, establishing operational procedures.

---

### [SSOT.md](files/SSOT.md)
**Single Source of Truth principles for canonical documentation**

- Governance patterns for authoritative documentation
- Canonical definitions and data contracts
- API specifications and schemas
- Policy management
- Conflict resolution strategies

**Use when**: Establishing canonical definitions, preventing documentation drift, managing data contracts and API specifications.

---

### [SKILL.md](files/SKILL.md)
**Anthropic Agent Skills specification for domain expertise packaging**

- Progressive disclosure model
- Skill structure (YAML metadata + Markdown instructions)
- Security considerations
- Platform support (Claude API, Claude Code, Claude.ai)
- Skill development workflow

**Use when**: Packaging reusable domain expertise, creating specialized agent capabilities, building skill libraries.

---

### [PLANS.md](files/PLANS.md)
**ExecPlans workflow for documenting complex initiatives**

- Document structure and format
- Progress tracking and decision logging
- Surprise and discovery capture
- Multi-session task continuity
- Retrospective documentation

**Use when**: Starting multi-hour tasks, coordinating complex features, ensuring task resumability, tracking decisions.

---

### [CHANGELOG.md](files/CHANGELOG.md)
**Keep a Changelog format for version history**

- Semantic versioning practices
- User-facing change communication
- Release note structure
- Automation compatibility (Openchangelog)
- Maintenance best practices

**Use when**: Tracking project changes, communicating releases, maintaining version history, coordinating with users.

---

## Engineering Guides

### [Context Engineering](engineering/context.md)
**Best practices for designing, retrieving, and evaluating context bundles**

- Four Context Pillars: Relevance, Recency, Reliability, Responsibility
- Context pipeline architecture (retrieval, filtering, formatting, delivery)
- Multi-document structuring strategies
- Long-context optimization techniques
- Context quality monitoring and evaluation

**Use when**: Building RAG systems, optimizing retrieval pipelines, structuring prompts with extensive context, evaluating context quality.

**Sources**: Anthropic, DAIR.AI, DeepMind research

---

### [Prompt Engineering](engineering/prompt.md)
**Cross-platform prompt engineering patterns**

- Universal principles (clarity, specificity, examples)
- Platform-specific techniques (Anthropic Claude, Google Gemini, OpenAI GPT)
- Constraint-first prompt design
- Chain-of-thought and reasoning patterns
- Prompt iteration workflows

**Use when**: Designing effective prompts, troubleshooting AI outputs, optimizing for specific platforms, establishing prompt standards.

**Sources**: Anthropic, Google Vertex AI, OpenAI, DeepMind

---

## Tools & Protocols

### [Model Context Protocol (MCP)](tools/mcp.md)
**Open standard for connecting LLM applications to external data sources and tools**

- Client-server architecture using JSON-RPC 2.0
- Five core primitives: Prompts, Resources, Tools, Roots, Sampling
- Official SDKs: Python, TypeScript, C#, Java, Go
- Pre-built servers for GitHub, PostgreSQL, Google Drive, Slack, Puppeteer
- Security considerations and vulnerability mitigation
- Integration with Claude Desktop, OpenAI Agents SDK, custom applications

**Use when**: Building AI applications requiring standardized external data access, creating reusable tool integrations across multiple AI platforms, implementing secure bidirectional AI-to-system communication.

**Introduced**: November 2024 by Anthropic
**Adopted by**: OpenAI (March 2025), Microsoft, Google

---

## Platform References

### OpenAI

#### [Agents SDK (Responses API)](platforms/openai/agents-sdk.md)
**Production patterns for OpenAI Responses API**

- Python & TypeScript quickstarts
- Tool calling and function execution
- Streaming responses
- Session management
- Production deployment patterns

**Use when**: Building agents with OpenAI SDKs, implementing tool calling, deploying production OpenAI integrations.

---

#### [AgentKit (Conceptual)](platforms/openai/agent-kit.md)
**Higher-level agent framework patterns**

- Agent orchestration abstractions
- Memory management patterns (Redis, vector stores)
- Safety policy enforcement
- Logging and evaluation hooks
- Multi-agent workflows

**Use when**: Designing agent frameworks, implementing memory layers, building agent orchestration systems.

**Note**: Conceptual patterns—verify against official OpenAI releases.

---

#### [Code Generation](platforms/openai/codex.md)
**Modern code-assistance patterns (post-Codex)**

- Recommended models (GPT-4.1, GPT-4.1-mini, GPT-4o-mini)
- Code completion strategies
- Refactoring and editing patterns
- IDE integration approaches
- Legacy Codex migration guide

**Use when**: Building code generation features, migrating from Codex, optimizing code assistance tools.

---

## Anthropic

### [Claude Code](platforms/anthropic/claude-code.md)
**Agentic coding tool for terminal and web**

- Natural language development workflow
- Direct file editing, command execution, and Git integration
- Custom slash commands for repeated workflows
- MCP integration for extended capabilities
- GitHub Actions for automated code assistance
- Headless mode for CI/CD integration
- Supports Opus 4.1, Sonnet 4.5, Haiku 3.5 models

**Use when**: Building features rapidly, refactoring code, automating development tasks, integrating AI assistance into Git workflows and CI/CD pipelines.

**Availability**: CLI (macOS, Linux, Windows), Web (claude.ai/code), Mobile (iOS preview)

---

### [Claude Agent SDK](platforms/anthropic/claude-agent-sdk.md)
**SDK for building custom AI agents**

- Programmatic access to Claude Code infrastructure
- Async streaming API with full agent capabilities
- Custom tools as in-process MCP servers
- Fine-grained tool permissions and security controls
- Automatic context management (compact feature)
- External MCP server integration
- Python 3.10+ and TypeScript support

**Use when**: Building custom autonomous agents, integrating agents into applications, extending beyond coding use cases (research, data analysis, workflow automation).

**Powers**: Claude Code CLI and web interface

---

### [Petri](platforms/anthropic/petri.md)
**Open-source automated auditing framework for AI safety research**

- AI agents test target models across diverse scenarios
- Multi-turn conversation testing with simulated users and tools
- Automated scoring across safety risk categories (deception, sycophancy, power-seeking, refusal failure)
- Built on UK AISI's Inspect framework
- Supports major model API providers
- Ships with 111 seed instructions, runs in minutes
- Detects misaligned behaviors: autonomous deception, oversight subversion, whistleblowing

**Use when**: Evaluating AI model safety, testing for risky behaviors, conducting AI safety research, validating model alignment, automating model auditing, comparing safety across models.

**Powers**: Claude 4 and Claude Sonnet 4.5 System Cards, OpenAI collaboration for reward hacking detection

---

## Google

### [Agent2Agent (A2A) Protocol](platforms/google/a2a.md)
**Open protocol for AI agent interoperability**

- JSON-RPC 2.0 over HTTP(S) communication standard
- Agent discovery via Agent Cards
- Async-first design for long-running tasks
- Server-Sent Events (SSE) streaming
- Opaque execution model for privacy and security
- Linux Foundation hosted open-source project

**Use when**: Building multi-agent systems, enabling cross-framework agent communication, implementing agent discovery and task delegation.

**Industry Support**: Hosted by Linux Foundation with support from 50+ companies including AWS, Microsoft, Google, Salesforce, SAP, and ServiceNow.

---

## LangChain

### [LangChain](platforms/langchain/langchain.md)
**Open-source framework for building LLM-powered applications**

- Modular architecture with chains, agents, and memory
- Extensive integrations with LLM providers and vector databases
- Retrieval-Augmented Generation (RAG) patterns
- LCEL (LangChain Expression Language) for declarative workflows
- LangServe for API deployment
- LangSmith for observability and evaluation
- Python and JavaScript/TypeScript support

**Use when**: Building chatbots, document analysis systems, RAG applications, conversational agents with memory, or any LLM-powered workflow requiring structured composition.

**Ecosystem**: Used by companies including Replit, Clay, Rippling, Cloudflare, and Workday.

---

### [LangGraph](platforms/langchain/langgraph.md)
**Graph-based orchestration for stateful multi-agent workflows**

- Directed graph architecture for complex agent orchestration
- Built-in state management and persistence (checkpoints)
- Human-in-the-loop workflows with pause/resume capability
- Time-travel debugging for exploring alternative execution paths
- Streaming real-time updates
- LangGraph Studio for visual debugging
- Multi-agent coordination with conditional branching

**Use when**: Building multi-agent systems, workflows requiring conditional logic or parallel execution, agents needing human approval, long-running tasks that pause/resume, or applications requiring state persistence.

**Deployment**: LangGraph Cloud (managed), self-hosted, or VPC deployment options.

---

### Future Platforms

Planned additions:
- **`platforms/anthropic/`** - Claude API, prompt caching, extended context (additional guides)
- **`platforms/google/`** - Gemini API, Vertex AI, multimodal patterns (additional guides)
- **`platforms/microsoft/`** - Azure OpenAI, Copilot integration

---

## System Tools

### Git

#### [Git Worktree](system/git/worktree.md)
**Managing multiple working trees for simultaneous branch work**

- Multiple working trees from single repository
- Commands: add, list, remove, prune, lock, move, repair
- Parallel development workflows
- Hotfix patterns during feature development
- Code review without context switching
- Anti-patterns and common pitfalls

**Use when**: Working on multiple branches simultaneously, urgent hotfixes during feature work, code reviews without stashing, parallel builds and tests.

**Key commands**:
- `git worktree add` - Create new worktree
- `git worktree list` - Show all worktrees
- `git worktree remove` - Clean up worktree
- `git worktree prune` - Remove stale metadata

---

### Future System Tools

Planned additions:
- **`system/git/hooks.md`** - Git hooks for automation and validation
- **`system/git/submodules.md`** - Submodule management patterns
- **`system/docker/`** - Container-based development environments
- **`system/shell/`** - Shell scripting patterns
- **`system/ci-cd/`** - CI/CD integration patterns

---

## Governance

### [Contributing Guide](_meta/CONTRIBUTING.md)
**How to add, update, and maintain knowledge in this repository**

- Information addition workflow (human and AI-assisted)
- Section mapping guide
- Conflict resolution strategies
- Quality checklist
- Maintenance schedules

**Read this first** when contributing to the repository.

---

### [Style Guide](_meta/STYLE.md)
**Writing conventions, formatting rules, and structural patterns**

- YAML frontmatter format
- Heading conventions
- Citation and reference format
- Code block standards
- Markdown linting rules
- Commit message format

**Reference this** when writing or editing any markdown file.

---

### [Taxonomy](_meta/TAXONOMY.md)
**Controlled vocabulary for tags and terminology**

- Canonical tags by category
- Tag definitions and scope
- Synonyms and related concepts
- Tag validation rules
- Deprecation process

**Check this** before adding tags to frontmatter.

---

## Quick Start

### For First-Time Readers

1. **Start with [AGENTS.md](files/AGENTS.md)** - Understand the foundational convention
2. **Review [SSOT.md](files/SSOT.md)** - Learn about canonical documentation
3. **Check [tools/README.md](tools/README.md)** - If working with AI integration protocols
4. **Explore [platforms/README.md](platforms/README.md)** - If integrating specific AI platforms
5. **Browse [system/README.md](system/README.md)** - If working with Git, Docker, or system tools
6. **Consult [engineering/README.md](engineering/README.md)** - When planning context or prompt strategies
7. **Read [_meta/CONTRIBUTING.md](_meta/CONTRIBUTING.md)** - If you plan to contribute

### For AI Agents

1. **Read all topic files** to understand conventions
2. **Reference [_meta/CONTRIBUTING.md](_meta/CONTRIBUTING.md)** when asked to update content
3. **Follow [_meta/STYLE.md](_meta/STYLE.md)** for all edits
4. **Validate tags** against [_meta/TAXONOMY.md](_meta/TAXONOMY.md)
5. **Use [_templates/TOPIC_TEMPLATE.md](_templates/TOPIC_TEMPLATE.md)** when creating new topics

### For Contributors

1. **Browse [files/](files/)** to find relevant existing documents
2. **Check [.github/ISSUE_TEMPLATE/](.github/ISSUE_TEMPLATE/)** for issue templates
3. **Review [_meta/CONTRIBUTING.md](_meta/CONTRIBUTING.md)** for workflow guidance
4. **Use [_templates/](_templates/)** for consistent structure
5. **Submit PRs** following [.github/PULL_REQUEST_TEMPLATE.md](.github/PULL_REQUEST_TEMPLATE.md)

---

## How to Contribute

We welcome contributions of insights, best practices, and new knowledge.

### Adding Information to Existing Topics

1. **Identify the source** (article, documentation, research paper)
2. **Find the relevant file** (e.g., `files/AGENTS.md`)
3. **Open an issue** using [Topic Update template](.github/ISSUE_TEMPLATE/topic_update.md)
4. **Or submit a PR** following the [Contributing Guide](_meta/CONTRIBUTING.md)

### Proposing New Topics

1. **Verify the topic doesn't exist** in `files/`, `engineering/`, or `platforms/`
2. **Check it's substantial** (>2000 words potential)
3. **Open an issue** using [New Topic template](.github/ISSUE_TEMPLATE/new_topic.md)
4. **Gather initial sources** (2-3 authoritative references)
5. **Use [_templates/TOPIC_TEMPLATE.md](_templates/TOPIC_TEMPLATE.md)** when approved

### Working with AI Agents

**Prompt template for adding information:**

```
Please update files/AGENTS.md with the following information:

Source: https://example.com/article
Section: Core Patterns

Key Points:
- [Point 1]
- [Point 2]

Instructions:
1. Read the source
2. Integrate into the specified section
3. Add reference [R#] with proper citation
4. Update frontmatter (last_updated, sources array)
5. Add entry to Update Log
6. Follow _meta/STYLE.md conventions
```

See [_meta/CONTRIBUTING.md](_meta/CONTRIBUTING.md) for detailed AI interaction patterns.

---

## When to Use Each Guide

### Starting a New Project

1. **[AGENTS.md](files/AGENTS.md)** - Set up operational documentation
2. **[SSOT.md](files/SSOT.md)** - Establish canonical definitions
3. **[CHANGELOG.md](files/CHANGELOG.md)** - Initialize version tracking

### During Development

- **[PLANS.md](files/PLANS.md)** - Create ExecPlan for multi-hour tasks
- **[SKILL.md](files/SKILL.md)** - Package domain expertise
- **[SSOT.md](files/SSOT.md)** - Reference canonical definitions
- **[AGENTS.md](files/AGENTS.md)** - Follow testing and PR procedures
- **[context.md](engineering/context.md)** - Design retrieval pipelines
- **[prompt.md](engineering/prompt.md)** - Optimize prompts

### Platform Integration

- **[Model Context Protocol](tools/mcp.md)** - Implement standardized tool integrations
- **[OpenAI Agents SDK](platforms/openai/agents-sdk.md)** - Implement OpenAI agents
- **[OpenAI AgentKit](platforms/openai/agent-kit.md)** - Design agent frameworks
- **[OpenAI Code Generation](platforms/openai/codex.md)** - Build code assistance

### Completing Work

1. **Update [CHANGELOG.md](files/CHANGELOG.md)** - Document user-facing changes
2. **Close [PLANS.md](files/PLANS.md)** - Add retrospective
3. **Update [SSOT.md](files/SSOT.md)** - Add new canonical definitions

---

## Integration Patterns

These guides form an integrated system:

| Guide | Purpose | Example Use |
|-------|---------|-------------|
| **AGENTS.md** | How to work | Testing procedures, linting commands |
| **SSOT.md** | What things are | API endpoints, data schemas |
| **PLANS.md** | Complex initiatives | Multi-step features, refactoring projects |
| **SKILL.md** | Domain expertise | Financial analysis, form filling |
| **CHANGELOG.md** | What changed | Version history, release notes |

### Example Workflow

**Implementing a new authentication feature:**

1. **Create ExecPlan** ([PLANS.md](files/PLANS.md)) outlining phases
2. **Reference canonical schemas** from [SSOT.md](files/SSOT.md)
3. **Follow testing procedures** from [AGENTS.md](files/AGENTS.md)
4. **Use prompt patterns** from [prompt.md](engineering/prompt.md) when working with AI
5. **Package auth logic** as a Skill ([SKILL.md](files/SKILL.md)) for reuse
6. **Document changes** in [CHANGELOG.md](files/CHANGELOG.md)

---

## Automation

This repository includes automated quality checks:

### Continuous Integration

- **Markdown Linting** - Runs on every PR ([markdownlint](.github/workflows/markdownlint.yml))
- **Link Checking** - Runs on PRs and weekly ([linkcheck](.github/workflows/linkcheck.yml))

### Local Development

```bash
# Run markdown linting
npx markdownlint-cli2 "**/*.md"

# Check links
npx markdown-link-check files/*.md engineering/*.md
```

---

## Maintenance

### Regular Reviews

- **Monthly**: Check References for link rot
- **Quarterly**: Review tags against TAXONOMY.md, audit document status
- **Annually**: Major refactoring, template updates, structural improvements

### Document Lifecycle

- `draft` → `living` → `stable` → `deprecated`
- Track status in frontmatter `status` field
- Never delete deprecated content—mark and retain for historical reference

---

## License

MIT License

---

## Acknowledgments

This repository synthesizes best practices from:

- **Anthropic** - Claude documentation, prompt engineering guides, Agent Skills
- **OpenAI** - AGENTS.md convention, Responses API, code generation patterns
- **Google** - Vertex AI prompt design, Gemini documentation, Agent2Agent protocol
- **Linux Foundation** - A2A Protocol Project, agent interoperability standards
- **DAIR.AI** - Context engineering research
- **DeepMind** - Prompting research and techniques
- **Community** - Keep a Changelog, Semantic Versioning

---

**Repository Maintainer**: AI-First Development Team
**Last Updated**: 2025-10-24
**Canonical URL**: `https://github.com/artificial-intelligence-first/ssot`
