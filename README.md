# AI-First Development Reference (SSOT)

> **For Humans**: This repository is a curated, living knowledge base of best practices and conventions for working effectively with AI coding assistants. Use these guides to structure your projects in ways that maximize AI productivity while maintaining knowledge assets over time.
>
> **For AI Agents**: This repository contains canonical reference documentation for AI-first development patterns. Read these guides to understand how to structure work, maintain documentation, and collaborate effectively with human developers. When asked to update topics, follow the governance documents in `_meta/`.

---

## Vision

A **Single Source of Truth (SSOT)** for AI-first development knowledge that grows through continuous integration of new insights. Each topic is a living document that accumulates knowledge over time—like a NotebookLM for best practices.

**Core Principle**: We don't create new files for every new piece of information. Instead, we **enrich existing topics** by integrating new knowledge into the appropriate sections, with full source attribution.

---

## Table of Contents

- [Repository Structure](#repository-structure)
- [Core Topics](#core-topics)
- [Engineering Guides](#engineering-guides)
- [Platform References](#platform-references)
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
├── topics/                            # Core AI-first development topics
│   ├── README.md                      # Topic directory guide
│   ├── AGENTS.md                      # AI agent operational documentation
│   ├── CHANGELOG.md                   # Version history format
│   ├── PLANS.md                       # ExecPlans for complex tasks
│   ├── SKILL.md                       # Agent Skills specification
│   └── SSOT.md                        # Single Source of Truth principles
├── engineering/                       # Engineering methodologies
│   ├── README.md                      # Engineering guides overview
│   ├── context.md                     # Context engineering patterns
│   └── prompt.md                      # Prompt engineering patterns
├── platforms/                         # Platform-specific implementations
│   ├── README.md                      # Platform guides overview
│   └── openai/                        # OpenAI-specific guides
│       ├── agents-sdk.md              # Responses API patterns
│       ├── agent-kit.md               # AgentKit framework concepts
│       └── codex.md                   # Code generation patterns
├── _meta/                             # Governance & style guides
│   ├── CONTRIBUTING.md                # How to add/update content
│   ├── STYLE.md                       # Writing conventions
│   └── TAXONOMY.md                    # Canonical tag vocabulary
├── _templates/                        # Templates for new content
│   ├── TOPIC_TEMPLATE.md              # New topic template
│   └── SECTION_TEMPLATE.md            # Section templates
├── _assets/                           # Images, diagrams, resources
│   ├── topics/                        # Topic-related assets
│   ├── engineering/                   # Engineering-related assets
│   └── platforms/                     # Platform-related assets
├── .github/                           # GitHub automation
│   ├── workflows/                     # CI/CD pipelines
│   │   ├── markdownlint.yml           # Markdown quality checks
│   │   └── linkcheck.yml              # Broken link detection
│   ├── ISSUE_TEMPLATE/                # Issue templates
│   │   ├── topic_update.md            # Request topic updates
│   │   └── new_topic.md               # Propose new topics
│   └── PULL_REQUEST_TEMPLATE.md       # PR checklist
└── scripts/                           # Utility scripts
```

**Design Philosophy**:
- **`topics/`** - Core AI-first development conventions (1 topic = 1 file)
- **`engineering/`** - Deep technical methodologies (context, prompting)
- **`platforms/`** - Vendor-specific implementations (OpenAI, Anthropic, Google)
- **`_meta/`** - How to maintain this repository
- **`_templates/`** - Scaffolding for new content
- **`_assets/`** - Visual and supplementary resources

---

## Core Topics

### [AGENTS.md](topics/AGENTS.md)
**OpenAI AGENTS.md convention for AI-agent-friendly operational documentation**

- Machine-focused operational procedures
- Development environment setup
- Testing, linting, and PR workflows
- Security policies and best practices
- Cross-platform agent compatibility

**Use when**: Setting up project conventions for AI agents, defining development workflows, establishing operational procedures.

---

### [SSOT.md](topics/SSOT.md)
**Single Source of Truth principles for canonical documentation**

- Governance patterns for authoritative documentation
- Canonical definitions and data contracts
- API specifications and schemas
- Policy management
- Conflict resolution strategies

**Use when**: Establishing canonical definitions, preventing documentation drift, managing data contracts and API specifications.

---

### [SKILL.md](topics/SKILL.md)
**Anthropic Agent Skills specification for domain expertise packaging**

- Progressive disclosure model
- Skill structure (YAML metadata + Markdown instructions)
- Security considerations
- Platform support (Claude API, Claude Code, Claude.ai)
- Skill development workflow

**Use when**: Packaging reusable domain expertise, creating specialized agent capabilities, building skill libraries.

---

### [PLANS.md](topics/PLANS.md)
**ExecPlans workflow for documenting complex initiatives**

- Living document structure
- Progress tracking and decision logging
- Surprise and discovery capture
- Multi-session task continuity
- Retrospective documentation

**Use when**: Starting multi-hour tasks, coordinating complex features, ensuring task resumability, tracking decisions.

---

### [CHANGELOG.md](topics/CHANGELOG.md)
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

### Future Platforms

Planned additions:
- **`platforms/anthropic/`** - Claude API, prompt caching, extended context
- **`platforms/google/`** - Gemini API, Vertex AI, multimodal patterns
- **`platforms/microsoft/`** - Azure OpenAI, Copilot integration

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

1. **Start with [AGENTS.md](topics/AGENTS.md)** - Understand the foundational convention
2. **Review [SSOT.md](topics/SSOT.md)** - Learn about canonical documentation
3. **Explore [platforms/README.md](platforms/README.md)** - If integrating specific AI platforms
4. **Consult [engineering/README.md](engineering/README.md)** - When planning context or prompt strategies
5. **Read [_meta/CONTRIBUTING.md](_meta/CONTRIBUTING.md)** - If you plan to contribute

### For AI Agents

1. **Read all topic files** to understand conventions
2. **Reference [_meta/CONTRIBUTING.md](_meta/CONTRIBUTING.md)** when asked to update content
3. **Follow [_meta/STYLE.md](_meta/STYLE.md)** for all edits
4. **Validate tags** against [_meta/TAXONOMY.md](_meta/TAXONOMY.md)
5. **Use [_templates/TOPIC_TEMPLATE.md](_templates/TOPIC_TEMPLATE.md)** when creating new topics

### For Contributors

1. **Browse [topics/](topics/)** to find relevant existing documents
2. **Check [.github/ISSUE_TEMPLATE/](.github/ISSUE_TEMPLATE/)** for issue templates
3. **Review [_meta/CONTRIBUTING.md](_meta/CONTRIBUTING.md)** for workflow guidance
4. **Use [_templates/](_templates/)** for consistent structure
5. **Submit PRs** following [.github/PULL_REQUEST_TEMPLATE.md](.github/PULL_REQUEST_TEMPLATE.md)

---

## How to Contribute

We welcome contributions of insights, best practices, and new knowledge. This repository follows an **Evergreen Note** approach—information is continuously integrated into existing topics rather than creating scattered new files.

### Adding Information to Existing Topics

1. **Identify the source** (article, documentation, research paper)
2. **Find the relevant topic** (e.g., `topics/AGENTS.md`)
3. **Open an issue** using [Topic Update template](.github/ISSUE_TEMPLATE/topic_update.md)
4. **Or submit a PR** following the [Contributing Guide](_meta/CONTRIBUTING.md)

### Proposing New Topics

1. **Verify the topic doesn't exist** in `topics/`, `engineering/`, or `platforms/`
2. **Check it's substantial** (>2000 words potential)
3. **Open an issue** using [New Topic template](.github/ISSUE_TEMPLATE/new_topic.md)
4. **Gather initial sources** (2-3 authoritative references)
5. **Use [_templates/TOPIC_TEMPLATE.md](_templates/TOPIC_TEMPLATE.md)** when approved

### Working with AI Agents

**Prompt template for adding information:**

```
Please update topics/AGENTS.md with the following information:

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

1. **[AGENTS.md](topics/AGENTS.md)** - Set up operational documentation
2. **[SSOT.md](topics/SSOT.md)** - Establish canonical definitions
3. **[CHANGELOG.md](topics/CHANGELOG.md)** - Initialize version tracking

### During Development

- **[PLANS.md](topics/PLANS.md)** - Create ExecPlan for multi-hour tasks
- **[SKILL.md](topics/SKILL.md)** - Package domain expertise
- **[SSOT.md](topics/SSOT.md)** - Reference canonical definitions
- **[AGENTS.md](topics/AGENTS.md)** - Follow testing and PR procedures
- **[context.md](engineering/context.md)** - Design retrieval pipelines
- **[prompt.md](engineering/prompt.md)** - Optimize prompts

### Platform Integration

- **[OpenAI Agents SDK](platforms/openai/agents-sdk.md)** - Implement OpenAI agents
- **[OpenAI AgentKit](platforms/openai/agent-kit.md)** - Design agent frameworks
- **[OpenAI Code Generation](platforms/openai/codex.md)** - Build code assistance

### Completing Work

1. **Update [CHANGELOG.md](topics/CHANGELOG.md)** - Document user-facing changes
2. **Close [PLANS.md](topics/PLANS.md)** - Add retrospective
3. **Update [SSOT.md](topics/SSOT.md)** - Add new canonical definitions

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

1. **Create ExecPlan** ([PLANS.md](topics/PLANS.md)) outlining phases
2. **Reference canonical schemas** from [SSOT.md](topics/SSOT.md)
3. **Follow testing procedures** from [AGENTS.md](topics/AGENTS.md)
4. **Use prompt patterns** from [prompt.md](engineering/prompt.md) when working with AI
5. **Package auth logic** as a Skill ([SKILL.md](topics/SKILL.md)) for reuse
6. **Document changes** in [CHANGELOG.md](topics/CHANGELOG.md)

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
npx markdown-link-check topics/*.md engineering/*.md
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
- **Google** - Vertex AI prompt design, Gemini documentation
- **DAIR.AI** - Context engineering research
- **DeepMind** - Prompting research and techniques
- **Community** - Keep a Changelog, Semantic Versioning, Evergreen Notes

---

**Repository Maintainer**: AI-First Development Team
**Last Updated**: 2025-10-23
**Status**: Living Repository
**Canonical URL**: `https://github.com/artificial-intelligence-first/ssot`

---

> 💡 **Remember**: This is a living knowledge base. When you learn something new about AI-first development, don't create a new file—enrich an existing topic with your insights.
