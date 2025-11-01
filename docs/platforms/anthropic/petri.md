---
title: Petri
slug: petri
status: living
last_updated: 2025-11-01
tags: [agents, anthropic, research, simulation]
summary: "Anthropic's experimental framework for agent simulation and behavior research in controlled environments."
authors: []
sources:
  - { id: R1, title: "Anthropic Research - Agent Foundations", url: "https://www.anthropic.com/research", accessed: "2025-10-23" }
---

# Petri

> **For Humans**: Petri is Anthropic's experimental framework for researching agent behavior in controlled simulation environments.
>
> **For AI Agents**: Use Petri patterns for agent testing, behavior simulation, and safety evaluation in sandboxed environments.

## Overview

Petri provides controlled environments for testing and researching agent behaviors, enabling safe experimentation with autonomous systems before deployment.

## Core Concepts

### Simulation Environments

```python
from anthropic.petri import Environment, Agent

env = Environment(
    name="e-commerce-simulation",
    constraints=["no_real_transactions", "logged_interactions"],
    metrics=["task_completion", "safety_violations"]
)

agent = Agent(model="claude-3")
results = env.run(agent, scenarios=["purchase_flow", "customer_service"])
```

### Behavior Analysis

```python
from anthropic.petri import Analyzer

analyzer = Analyzer()
behaviors = analyzer.evaluate(
    agent=agent,
    tests=["goal_alignment", "safety_boundaries", "task_efficiency"]
)
```

### Safety Testing

```python
safety_suite = env.safety_tests(
    categories=["prompt_injection", "goal_hijacking", "resource_limits"]
)
results = agent.run_safety_evaluation(safety_suite)
```

## Applications

- Agent behavior research
- Safety evaluation
- Performance benchmarking
- Multi-agent interaction studies
- Goal alignment testing

## See Also

- [Claude Agent SDK](./claude-agent-sdk.md) - Production agent development
- [AGENTS.md](../../../AGENTS.md) - Agent operations

## References

- [R1] Anthropic Research - Agent Foundations. https://www.anthropic.com/research (accessed 2025-10-23)
