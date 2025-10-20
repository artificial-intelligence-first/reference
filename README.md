# AI-First Development Reference

> **For Humans**: This repository is a curated collection of best practices and conventions for working effectively with AI coding assistants. Use these guides to structure your projects in ways that maximize AI productivity.
>
> **For AI Agents**: This repository contains canonical reference documentation for AI-first development patterns. Read these guides to understand how to structure work, maintain documentation, and collaborate effectively with human developers.

Use the guides in this repository to establish consistent, AI-friendly workflows across your projects.

## Overview

This repository provides best practices, patterns, and reference documentation to help AI agents (Claude Code, GitHub Copilot, Cursor, etc.) work more effectively when assisting with software development projects. Each guide is designed to be readable by both humans (for understanding) and AI agents (for operational guidance).

## Documentation

### Development Conventions (markdown-file/)

#### [AGENTS.md](markdown-file/AGENTS.md)
Guidelines for leveraging AI agents, design patterns, and effective prompt engineering techniques.

#### [SSOT.md](markdown-file/SSOT.md)
Single Source of Truth principles and documentation management best practices for AI-assisted development.

#### [SKILL.md](markdown-file/SKILL.md)
Skill sets, technology stacks, and development methodologies for AI-assisted development.

#### [PLANS.md](markdown-file/PLANS.md)
Project planning, task management, and efficient development workflows using AI tools.

#### [CHANGELOG.md](markdown-file/CHANGELOG.md)
Version history and changes to this repository.

### SDK & Framework Guides (sdk/)

#### [OpenAI AgentKit (Conceptual)](sdk/Agent_Kit.md)
Conceptual, higher-level scaffold for autonomous agents. Examples are illustrative—validate against the latest OpenAI releases.

#### [OpenAI Agents API (Low-Level)](sdk/Agents_SDK.md)
Practical guidance for orchestrating agents with OpenAI’s current Python/TypeScript SDKs and the Responses API.

#### [OpenAI Code Generation](sdk/Codex-SDK.md)
Modern code-generation patterns using the Responses API (Codex legacy behaviour is described as historical context only).

**See [sdk/README.md](sdk/README.md)** for SDK comparison, integration patterns, and decision guide. Always cross-check with the official OpenAI documentation before implementation.

## When to Use Each Guide

Understanding which guide to reference at different stages of your project:

### Starting a New Project
1. **Read [AGENTS.md](markdown-file/AGENTS.md)** first to set up your project's operational documentation
2. **Create [SSOT.md](markdown-file/SSOT.md)** to establish canonical definitions and data contracts
3. **Initialize [CHANGELOG.md](markdown-file/CHANGELOG.md)** to track project evolution

### During Development
- **[PLANS.md](markdown-file/PLANS.md)** - Create an ExecPlan for any multi-hour task or complex feature
- **[SKILL.md](markdown-file/SKILL.md)** - Package domain-specific expertise into reusable Skills
- **[SSOT.md](markdown-file/SSOT.md)** - Reference for canonical definitions; update when terminology evolves
- **[AGENTS.md](markdown-file/AGENTS.md)** - Follow for testing, linting, and PR procedures

### Completing Work
1. **Update [CHANGELOG.md](markdown-file/CHANGELOG.md)** with user-facing changes
2. **Close [PLANS.md](markdown-file/PLANS.md)** ExecPlan with retrospective
3. **Update [SSOT.md](markdown-file/SSOT.md)** if new definitions were introduced

## How These Documents Work Together

These guides form an integrated system:

- **AGENTS.md** defines *how* to work (procedures, commands, workflows)
- **SSOT.md** defines *what* things are (terminology, schemas, policies)
- **PLANS.md** tracks *complex initiatives* (multi-step tasks, decisions, progress)
- **SKILL.md** packages *domain expertise* (specialized knowledge, procedures)
- **CHANGELOG.md** records *what changed* (version history, user impact)

**Example workflow**: When implementing a new feature, create an ExecPlan (PLANS.md) that references testing procedures (AGENTS.md) and canonical data schemas (SSOT.md). Upon completion, summarize user-facing changes in CHANGELOG.md and package any reusable procedures as a Skill (SKILL.md).

## Repository Structure

```
reference/
├── README.md              # This file - repository overview and guide index
├── markdown-file/         # Comprehensive guides for AI-first development
│   ├── AGENTS.md          # OpenAI AGENTS.md convention guide
│   ├── CHANGELOG.md       # Keep a Changelog format guide
│   ├── PLANS.md           # ExecPlans workflow guide
│   ├── SKILL.md           # Anthropic Agent Skills guide
│   └── SSOT.md            # Single Source of Truth principles guide
└── sdk/                   # OpenAI SDK and framework documentation
    ├── README.md          # SDK overview, comparison, and integration patterns
    ├── Agent_Kit.md       # OpenAI AgentKit framework guide (conceptual)
    ├── Agents_SDK.md      # OpenAI Agents SDK reference
    └── Codex-SDK.md       # OpenAI code generation guide
```

**Note for Implementation**: While these guides recommend placing files like `AGENTS.md` and `SSOT.md` at your project root, this reference repository organizes them into directories for easier navigation. When adopting these conventions in your own projects, follow the placement recommendations within each guide.

## Quick Start

**For first-time readers:**
1. Start with [AGENTS.md](markdown-file/AGENTS.md) to understand the foundational convention
2. Review [SSOT.md](markdown-file/SSOT.md) to learn about canonical documentation
3. Explore [sdk/README.md](sdk/README.md) if you're integrating OpenAI SDKs for agent or code generation features
4. Browse other guides based on your immediate needs, adapting the sample workflows and checklists to your context

**For AI agents:**
1. Read all guide files to understand conventions
2. Reference specific guides when performing relevant tasks
3. Consult SDK guides when implementing agent or code generation features
4. Follow cross-document integration patterns consistently

## Usage

1. Reference these documents to find patterns applicable to your projects
2. Use these materials as context when working with AI agents
3. Customize and extend with project-specific patterns and best practices
4. Maintain consistency across all documentation following these conventions

## Contributing

We welcome contributions of insights and best practices related to AI-first development. Template examples, sample ExecPlans, and Skill packages are especially appreciated.

## License

MIT License
