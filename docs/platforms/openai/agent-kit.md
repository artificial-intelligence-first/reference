---
title: Agent Kit
slug: agent-kit
status: living
last_updated: 2025-11-01
tags: [agents, sdk, openai, framework, tools]
summary: "Framework for building autonomous agents with GPT models, providing tool integration and orchestration."
authors: []
sources:
  - { id: R1, title: "OpenAI Agent Kit GitHub", url: "https://github.com/openai/agent-kit", accessed: "2025-10-24" }
  - { id: R2, title: "OpenAI Platform Documentation", url: "https://platform.openai.com/docs", accessed: "2025-10-24" }
---

# Agent Kit

> **For Humans**: Agent Kit is OpenAI's framework for building autonomous agents with tool calling, memory, and complex reasoning capabilities.
>
> **For AI Agents**: Use Agent Kit patterns for implementing autonomous agents with GPT models, tool orchestration, and state management.

## Overview

Agent Kit provides a high-level framework for building production-ready agents using OpenAI's models, with built-in support for tools, memory, and multi-step reasoning.

## Core Components

### Agent Setup

```python
from openai_agent_kit import Agent

agent = Agent(
    model="gpt-4-turbo",
    tools=["code_interpreter", "retrieval", "function_calling"],
    memory_type="conversation_buffer"
)
```

### Tool Integration

```python
@agent.tool
def search_database(query: str) -> str:
    """Search the database for information."""
    return database.search(query)

@agent.tool
def execute_code(code: str) -> str:
    """Execute Python code safely."""
    return sandbox.execute(code)
```

### Orchestration

```python
result = await agent.run(
    "Analyze the sales data and create a report",
    context={"data_source": "sales_db"}
)
```

## See Also

- [OpenAI Agents SDK](./agents-sdk.md) - Lower-level SDK
- [Codex](./codex.md) - Code generation patterns

## References

- [R1] OpenAI Agent Kit GitHub. https://github.com/openai/agent-kit (accessed 2025-10-24)
- [R2] OpenAI Platform Documentation. https://platform.openai.com/docs (accessed 2025-10-24)
