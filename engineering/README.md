# Engineering Guides (Context & Prompt)

> **For Humans**: The `engineering/` directory centralises deep-dive playbooks for context engineering and prompt engineering. Use this index to decide which guide to consult when you are shaping retrieval stacks or writing high-leverage prompts for AI-first development.
>
> **For AI Agents**: Review this file before modifying any documents in this directory so you can reference or extend the correct guide. Each document under `engineering/` distils vendor guidance into actionable, platform-agnostic steps.

## Overview

| Guide | Focus | Primary Audience | When to Use |
|-------|-------|------------------|-------------|
| `Context.md` | Designing, evaluating, and operating context pipelines | Platform engineers, AI agents managing retrieval | Building or refining retrieval + memory stacks, long-context workflows |
| `Prompt.md` | Prompt engineering patterns across Anthropic, Google, OpenAI | Developers, technical writers, AI agents crafting prompts | Creating reusable prompts, aligning multi-vendor behaviors |

## Quick Decision Guide

- Start with **`Context.md`** when you are:
  - Implementing or auditing retrieval pipelines
  - Planning multi-document or long-context layouts (e.g., Claude 100k context)
  - Defining metrics to monitor context relevance, recency, reliability, responsibility
  - Rolling out versioned context bundles or progressive disclosure strategies

- Start with **`Prompt.md`** when you are:
  - Designing prompt templates or negative prompting guardrails
  - Comparing Anthropic / Google / OpenAI prompt recommendations
  - Packaging prompts as reusable Skills or SOPs for AGENTS.md
  - Evaluating prompt iteration vs. fine-tuning trade-offs

## Integration with Repository Docs

- **`file/AGENTS.md`** – Reference testing/operational procedures when updating prompt or context processes.
- **`file/SSOT.md`** – Record canonical terminology, schema definitions, and policy links used inside prompts/context bundles.
- **`file/PLANS.md`** – Capture context/prompt architecture work inside ExecPlans (decisions, surprises, validation).
- **`file/SKILL.md`** – When a prompt or context pattern is reusable, package it as a Skill and document triggers.
- **`sdk/README.md`** – Combine these guides with SDK-specific instructions when implementing retrieval or prompt orchestration in code.

## Maintenance Tips

- Keep source citations at the top of each guide current (update sync dates when refreshing content).
- Explicitly label sample or hypothetical sections so readers know what requires adaptation.
- Log structural or policy changes in each guide’s Update Log for traceability.
- Align terminology with `SSOT.md` and update AGENTS.md runbooks whenever procedures change.

## Update Log

- 2025-10-20: Added directory index describing the purpose of `Context.md` and `Prompt.md` and how they integrate with the rest of the repository.

---

**Remember:** These engineering guides translate vendor documentation into actionable standards for AI-first projects. Keep them aligned with upstream sources and the rest of the repository’s governance docs.***
