---
title: LangGraph
slug: langgraph
status: living
last_updated: 2025-11-01
tags: [agents, orchestration, workflows, state-machines, langchain]
summary: "Library for building stateful, multi-agent applications with LLMs using graph-based workflows."
authors: []
sources:
  - { id: R1, title: "LangGraph Documentation", url: "https://langchain-ai.github.io/langgraph/", accessed: "2025-10-24" }
  - { id: R2, title: "LangGraph GitHub Repository", url: "https://github.com/langchain-ai/langgraph", accessed: "2025-10-24" }
---

# LangGraph

> **For Humans**: LangGraph enables building stateful, multi-agent applications with cycles, controllable workflows, and built-in persistence.
>
> **For AI Agents**: Use LangGraph for complex multi-agent orchestration with state machines, conditional routing, and human-in-the-loop patterns.

## Overview

LangGraph extends LangChain with graph-based workflow orchestration, enabling complex agent interactions with cycles, branching, and state management.

## Core Concepts

### State Graphs

```python
from langgraph.graph import StateGraph

workflow = StateGraph(AgentState)
workflow.add_node("agent", agent_node)
workflow.add_node("tool", tool_node)
workflow.add_edge("agent", "tool")
workflow.add_conditional_edges(
    "tool",
    should_continue,
    {True: "agent", False: END}
)
```

### Multi-Agent Systems

```python
supervisor = StateGraph(State)
supervisor.add_node("researcher", research_agent)
supervisor.add_node("coder", coding_agent)
supervisor.add_node("reviewer", review_agent)
```

### Human-in-the-Loop

```python
workflow.add_node("human_review", human_approval_node)
workflow.add_edge("agent", "human_review")
```

## See Also

- [LangChain](./langchain.md) - Core framework
- [Agent Orchestration](../../workflows/plans.md) - Workflow patterns

## References

- [R1] LangGraph Documentation. https://langchain-ai.github.io/langgraph/ (accessed 2025-10-24)
- [R2] LangGraph GitHub Repository. https://github.com/langchain-ai/langgraph (accessed 2025-10-24)