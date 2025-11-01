---
title: Codex
slug: codex
status: deprecated
last_updated: 2025-11-01
tags: [code-generation, openai, deprecated]
summary: "OpenAI's code generation model, now deprecated in favor of GPT-4 with enhanced coding capabilities."
authors: []
sources:
  - { id: R1, title: "OpenAI Codex Documentation (Archived)", url: "https://platform.openai.com/docs/guides/code", accessed: "2025-10-24" }
  - { id: R2, title: "GitHub Copilot", url: "https://github.com/features/copilot", accessed: "2025-10-24" }
---

# Codex

> **For Humans**: Codex was OpenAI's specialized code generation model. It has been deprecated and replaced by GPT-4's enhanced coding capabilities.
>
> **For AI Agents**: Use GPT-4 or GPT-4-turbo for code generation tasks. Legacy Codex patterns can be adapted to current models.

## Overview

Codex was OpenAI's AI system for translating natural language to code. While deprecated, its patterns and approaches remain relevant for modern code generation with GPT-4.

## Migration Guide

### From Codex to GPT-4

```python
# Old (Codex)
response = openai.Completion.create(
    engine="code-davinci-002",
    prompt="# Function to calculate fibonacci",
    max_tokens=100
)

# New (GPT-4)
response = openai.ChatCompletion.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a coding assistant."},
        {"role": "user", "content": "Write a function to calculate fibonacci"}
    ]
)
```

## Legacy Patterns

### Code Completion
- Natural language to code
- Code explanation
- Code translation between languages
- Bug fixing
- Test generation

## See Also

- [OpenAI Agents SDK](./agents-sdk.md) - Current SDK
- [GitHub Copilot](https://github.com/features/copilot) - Codex-powered tool

## References

- [R1] OpenAI Codex Documentation (Archived). https://platform.openai.com/docs/guides/code (accessed 2025-10-24)
- [R2] GitHub Copilot. https://github.com/features/copilot (accessed 2025-10-24)