---
title: Context Engineering for AI-First Development
slug: context-engineering
status: living
last_updated: 2025-10-23
tags: [context, rag, retrieval, prompt-engineering, ai-first]
summary: "Best practices for designing, retrieving, and evaluating context bundles that feed AI workflows across platforms."
sources:
  - { id: R1, title: "Effective Context Engineering for AI Agents", url: "https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents", accessed: "2025-10-20" }
  - { id: R2, title: "Claude Long Context Tips", url: "https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/long-context-tips", accessed: "2025-10-20" }
  - { id: R3, title: "Context Engineering Guide", url: "https://www.promptingguide.ai/guides/context-engineering-guide", accessed: "2025-10-20" }
---

# Context Engineering for AI-First Development

> **For Humans**: This guide consolidates best practices for designing, retrieving, and evaluating context bundles that feed AI workflows. Use it to understand how to structure context effectively across different AI platforms.
>
> **For AI Agents**: Review this guide when designing context pipelines, structuring multi-document prompts, or implementing retrieval systems. Follow these patterns to maintain high-quality, relevant context delivery.

## Overview

Context engineering is the practice of designing, collecting, and delivering relevant information to AI models in ways that maximize their effectiveness. This document synthesizes guidance from Anthropic, DAIR.AI, and other sources to provide a comprehensive framework for context management in AI-first development projects.

## Core Principles

### The Four Context Pillars (Anthropic)

1. **Relevance** - Context should directly relate to the task at hand
2. **Recency** - Information should be current and up-to-date
3. **Reliability** - Sources should be trustworthy and accurate
4. **Responsibility** - Context should be ethically sourced and properly attributed

### Context Pipeline Architecture

Effective context engineering separates concerns into distinct stages:

1. **Retrieval** - Finding relevant information from available sources
2. **Filtering** - Removing irrelevant or low-quality content
3. **Formatting** - Structuring context for optimal model consumption
4. **Instrumentation** - Monitoring and measuring context quality

Each stage should have targeted metrics and evaluation criteria.

## Implementation Strategies

### Progressive Disclosure (Anthropic)

Rather than overwhelming the model with all available context upfront:

1. Start with minimal core context
2. Request clarifications from users when needed
3. Expand to comprehensive knowledge only when necessary
4. Monitor context window usage and relevance

### Multi-Document Structure (Claude-Specific)

When working with Claude's large context windows:

1. **Place priority documents early** - Most critical information should appear first
2. **Use consistent headers and delimiters** - Tag sections with clear, recognizable markers
3. **Summarize before full text** - Provide brief summaries of supporting evidence before detailed content
4. **End with explicit instructions** - Final section should clearly reference supplied materials and state the task

Example structure:
```markdown
<priority_document>
[Critical information]
</priority_document>

<supporting_evidence>
Summary: [Brief overview]
Full text: [Detailed content]
</supporting_evidence>

<task_instructions>
Using the priority document and supporting evidence above...
</task_instructions>
```

### Retrieval Stack Checklist (DAIR.AI)

Before implementing a context retrieval system, address:

- **Document ingestion** - How will source materials be collected and updated?
- **Chunking strategy** - What granularity is appropriate for your use case?
- **Embedding selection** - Which embedding model best captures your domain?
- **Ranking mechanism** - How will retrieved chunks be prioritized?

### Provenance and Metadata

Every context artifact should include:

- **Source** - Where did this information come from?
- **Timestamp** - When was it created or last updated?
- **Trust tier** - How reliable is this source?
- **Usage rights** - Can this be used in the current context?

This metadata enables:
- Citation in model outputs
- Debugging context quality issues
- Compliance with data usage policies
- Audit trails for high-stakes applications

## Evaluation and Testing

### Problem Framing (DAIR.AI)

Before building context systems, clearly define:

- **Task goals** - What should the AI accomplish?
- **User personas** - Who will interact with the system?
- **Desired outputs** - What does success look like?
- **Context constraints** - What are the limits (tokens, latency, cost)?

### Evaluation Patterns

Test context quality using:

1. **Contrastive prompting** - Compare outputs with different context bundles
2. **Ablation tests** - Remove context elements to measure impact
3. **User-in-the-loop reviews** - Gather human feedback on context relevance
4. **Regression testing** - Maintain test suites that replay representative tasks

### Metrics for Each Pillar

- **Relevance** - Precision/recall of retrieved information, task completion rate
- **Recency** - Staleness metrics, update frequency, time-to-refresh
- **Reliability** - Source credibility scores, fact-checking results, error rates
- **Responsibility** - Attribution coverage, bias metrics, provenance completeness

## Operational Best Practices

### Monitoring and Drift Detection

Implement telemetry to track:
- Context retrieval latency
- Token usage per request
- Relevance scores over time
- User feedback and corrections
- Context version effectiveness

### Safe Rollouts

When updating context strategies:

1. **Version context bundles** - Maintain multiple versions for comparison and rollback
2. **A/B test changes** - Deploy to subset of traffic before full rollout
3. **Monitor impact metrics** - Track task success, user satisfaction, error rates
4. **Document changes** - Record what changed, why, and observed effects
5. **Enable quick rollback** - Keep previous versions readily available

### Integration with Development Workflow

- **Sync with source-of-truth repositories** - Automate context updates from canonical sources
- **Validate payloads** - Use automation scripts to verify context formatting and completeness
- **Cross-reference with documentation** - Ensure context aligns with SSOT.md and AGENTS.md
- **Package reusable patterns** - Create Skills (SKILL.md) for proven context strategies

## Platform-Specific Considerations

### Anthropic Claude

- Supports very large context windows (100K+ tokens)
- Responds well to structured, tagged sections
- Benefits from explicit task instructions at the end
- Works effectively with progressive disclosure

### OpenAI GPT Models

- Context windows vary by model (consult current documentation)
- Performs well with concise, high-signal context
- Supports function calling for dynamic context retrieval
- Benefits from upfront constraint declarations

### General Best Practices

- **Test context limits** - Understand actual vs. advertised context windows
- **Optimize for latency** - More context = slower responses
- **Monitor costs** - Token usage directly impacts API costs
- **Plan for context overflow** - Have strategies for when content exceeds limits

## Common Pitfalls

Avoid these context engineering mistakes:

- **Dumping entire codebases** - Provide targeted, relevant sections instead
- **Ignoring recency** - Stale context leads to outdated or incorrect outputs
- **Missing provenance** - Always track where information came from
- **No evaluation loop** - Context quality degrades without continuous monitoring
- **Overloading context window** - More isn't always better; relevance matters most
- **Inconsistent formatting** - Use standardized structures for predictable parsing

## Decision Guide

| Scenario | Recommendation |
|----------|----------------|
| Building new retrieval system | Follow DAIR.AI checklist, start simple |
| Working with Claude | Use multi-document structure with tags |
| Limited context window | Prioritize progressive disclosure |
| High-stakes application | Implement full provenance tracking |
| Rapid prototyping | Start with manual context curation, automate later |
| Production deployment | Add comprehensive monitoring and rollback capabilities |

## Integration with Other Guides

- **AGENTS.md** - Context retrieval helpers should follow operational procedures
- **SSOT.md** - Use as canonical source for terminology and schemas in context
- **SKILL.md** - Package proven context strategies as reusable Skills
- **PLANS.md** - Document context architecture decisions in ExecPlans

## Resources and References

### Primary Sources
- Anthropic: [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- Anthropic: [Claude Long Context Tips](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/long-context-tips)
- DAIR.AI: [Context Engineering Guide](https://www.promptingguide.ai/guides/context-engineering-guide)

### Related Documentation
- `/context/` - Vendor-specific context engineering notes
- `/prompt/` - Prompt engineering best practices
- `/sdk/` - SDK-specific implementation guidance

## Update Log

- 2025-10-20: Initial creation consolidating context/ directory guidance for engineering reference
