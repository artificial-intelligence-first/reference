# AI-First Development Conventions (Directory Guide)

> **For Humans**: This index explains how to navigate the foundational governance guides stored in `topics/`. Use it as a quick reminder of what each document covers and when to update it.
>
> **For AI Agents**: Review this guide before touching the documents in this directory. It clarifies which file governs which behaviour so you can reference or update the right source of truth.

## Overview

The `topics/` directory contains five core specifications that govern AI-first development projects:

1. **AGENTS.md** – Operational procedures and commands for contributors and AI agents.
2. **CHANGELOG.md** – Keep a Changelog format reference for communicating releases.
3. **PLANS.md** – ExecPlan workflow for multi-step initiatives.
4. **SKILL.md** – Anthropic-style Skill packaging guidelines.
5. **SSOT.md** – Single Source of Truth principles for canonical data and policies.

These documents are authoritative references and should be kept consistent with the repository root `README.md`.

## Quick Decision Guide

| Document | Use When… | Typical Updates |
|----------|-----------|-----------------|
| `AGENTS.md` | You need to define “how to work” (setup, tests, PR rules, safety practices). | Update whenever workflows, scripts, or CI expectations change. |
| `CHANGELOG.md` | You document “what changed” for releases. | Update alongside user-facing changes or before tagging a release. |
| `PLANS.md` | You run complex, multi-hour projects that require resumable context. | Update continuously while executing an ExecPlan. |
| `SKILL.md` | You package domain expertise for AI agents. | Update when adding or modifying Skills. |
| `SSOT.md` | You define canonical terminology, schemas, or policies. | Update whenever the authoritative source of truth changes. |

## Integration Patterns

- **ExecPlan workflow**: Start a new initiative by creating an ExecPlan (`PLANS.md`), follow operational procedures from `AGENTS.md`, and update `CHANGELOG.md` and `SSOT.md` as needed.
- **Documentation consistency**: When definitions or policies change, update `SSOT.md` first, then propagate adjustments to `AGENTS.md`, `PLANS.md`, or other docs.
- **Skill creation**: When new workflows emerge, capture reusable expertise via `SKILL.md` and link back to the relevant sections in `AGENTS.md` or ExecPlans.

## Maintenance Tips

- Keep dates, placeholders, and sample annotations current (see “Sample” notes inside each guide).
- Cross-reference updates between files (e.g., note in `AGENTS.md` when `SSOT.md` is updated).
- Use the Update Logs in each document to track revisions and provide transparency.

## Related Resources

- Root `README.md` – Repository-wide map and guidance.
- `platforms/README.md` – Platform-specific SDK and framework documentation (complements these governance docs).

---

**Remember:** Treat this directory as the governance layer for your AI-first projects. Update the appropriate guide whenever processes, definitions, or reusable knowledge change, and keep cross-references aligned so humans and AI agents stay on the same page.
