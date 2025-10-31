# Agent SDK Overview (2025 Edition)

> **For Humans**: Use this guide to understand the major agent development frameworks from OpenAI, Anthropic, and Google. This document provides a high-level overview and quick reference for selecting the right SDK for your use case.
>
> **For AI Agents**: This document provides canonical guidance for agent SDK selection. Cross-reference platform-specific documentation for detailed implementation patterns.

---

## Quick SDK Selection

### 🔷 OpenAI Agents SDK

**Best For**: Customer support, chat workflows, rapid prototyping, multi-provider flexibility

**Key Features**:
- Provider-agnostic (supports 100+ LLM providers)
- Built-in session management (SQLite, Redis)
- Native guardrails for safety
- Simple agent handoffs for specialization
- Built-in tracing and debugging

**Languages**: Python (primary)

**Documentation**: [openai/agents-sdk.md](openai/agents-sdk.md)

**Quick Start**:
```python
from agents import Agent, Runner

agent = Agent(
    name="Assistant",
    instructions="You are a helpful assistant",
    tools=[...]
)

result = Runner.run_sync(agent, "Help me with a task")
```

---

### 🟣 Anthropic Claude Agent SDK

**Best For**: Coding agents, research workflows, autonomous task execution, file system access

**Key Features**:
- Full computer access (file operations, bash execution)
- Custom tools as in-process MCP servers
- Automatic context compaction for long sessions
- Verification-focused (linting, visual feedback)
- Bidirectional client for interactive conversations

**Languages**: Python (3.10+), TypeScript

**Documentation**: [anthropic/claude-agent-sdk.md](anthropic/claude-agent-sdk.md)

**Quick Start**:
```python
from claude_agent_sdk import ClaudeSDKClient
import asyncio

async def main():
    client = ClaudeSDKClient()
    async with client.create_session(
        system_prompt="You are a helpful assistant"
    ) as session:
        response = await session.send_message("Task")
        async for chunk in response:
            print(chunk.content)

asyncio.run(main())
```

---

### 🔴 Google Agent Development Kit (ADK)

**Best For**: Complex multi-agent systems, Google Cloud deployments, enterprise Java projects

**Key Features**:
- Hierarchical multi-agent architecture
- Native Google Cloud deployment (Cloud Run, Vertex AI)
- Flexible tool integration (functions, OpenAPI, MCP)
- Java support alongside Python
- Development UI for testing
- Model-agnostic with Gemini optimization

**Languages**: Python, Java

**Documentation**: [google/adk.md](google/adk.md)

**Quick Start**:
```python
from google.adk.agents import Agent

agent = Agent(
    name="assistant",
    model="gemini-2.5-flash",
    instruction="You are a helpful assistant",
    tools=[...]
)

response = agent.run("Help me with a task")
```

---

## Feature Comparison Matrix

| Feature | OpenAI | Claude | Google ADK |
|---------|--------|--------|------------|
| **Provider Support** | 100+ providers ⭐⭐⭐ | Anthropic only | Multi-provider ⭐⭐ |
| **Multi-Agent** | Handoffs (flat) ⭐⭐ | Subagents (parallel) ⭐⭐ | Hierarchical (tree) ⭐⭐⭐ |
| **File Operations** | Limited | Full access ⭐⭐⭐ | Tool-dependent ⭐⭐ |
| **Command Execution** | Limited | Full bash ⭐⭐⭐ | Tool-dependent ⭐⭐ |
| **Session Management** | Built-in ⭐⭐⭐ | Manual ⭐⭐ | Manual ⭐⭐ |
| **Context Management** | Manual ⭐⭐ | Auto-compaction ⭐⭐⭐ | Manual ⭐⭐ |
| **Guardrails** | Built-in ⭐⭐⭐ | Custom hooks ⭐⭐ | Manual |
| **Language Support** | Python | Python, TypeScript ⭐⭐ | Python, Java ⭐⭐⭐ |
| **Development UI** | No | Via Claude Code ⭐⭐ | Yes (built-in) ⭐⭐⭐ |
| **Cloud Integration** | Generic | Generic | Google Cloud native ⭐⭐⭐ |
| **Ease of Use** | Very easy ⭐⭐⭐ | Moderate ⭐⭐ | Moderate ⭐⭐ |
| **Open Source** | MIT ✓ | MIT ✓ | Apache 2.0 ✓ |

**📊 For detailed comparison**: See [Agent SDK Comparison Guide](agent-sdk-comparison.md)

---

## Use Case Recommendations

### Research & Information Gathering
**→ Claude Agent SDK** ⭐⭐⭐
- Native bash for CLI tools
- File operations for documents
- Auto-context for long sessions

### Code Generation & Development
**→ Claude Agent SDK** ⭐⭐⭐
- Full file system access
- Bash for tests/builds
- Verification with linting

### Customer Support & Chat
**→ OpenAI Agents SDK** ⭐⭐⭐
- Built-in sessions
- Native guardrails
- Simple handoffs

### Complex Multi-Agent Systems
**→ Google ADK** ⭐⭐⭐
- Hierarchical architecture
- Parallel execution
- Development UI

### Enterprise Java Applications
**→ Google ADK** ⭐⭐⭐
- Native Java support
- Maven integration
- Strongly-typed

### Google Cloud Deployments
**→ Google ADK** ⭐⭐⭐
- Native Cloud Run/Vertex AI
- Optimized for Gemini
- Integrated monitoring

### Multi-Provider Flexibility
**→ OpenAI Agents SDK** ⭐⭐⭐
- Supports 100+ providers
- Easy provider switching
- Cost optimization

### Rapid Prototyping
**→ OpenAI Agents SDK** ⭐⭐⭐
- Minimal boilerplate
- Simple API
- Fast iteration

---

## Decision Flowchart

```
START: What's your primary need?
│
├─ File/bash operations? → Claude Agent SDK ✓
│
├─ Hierarchical multi-agent? → Google ADK ✓
│
├─ Customer support/chat? → OpenAI Agents SDK ✓
│
├─ Google Cloud deployment? → Google ADK ✓
│
├─ Java required? → Google ADK ✓
│
├─ Multi-provider support? → OpenAI Agents SDK ✓
│
└─ Rapid prototyping? → OpenAI Agents SDK ✓
```

---

## Documentation Map

### Core SDK Documentation
- **[agent-sdk-comparison.md](agent-sdk-comparison.md)** – Comprehensive side-by-side comparison with architecture diagrams, migration guides, and cost analysis

### OpenAI Platform
- **[openai/agent-kit.md](openai/agent-kit.md)** – Conceptual scaffolding, memory models, safety guardrails
- **[openai/agents-sdk.md](openai/agents-sdk.md)** – Official Python/TypeScript SDK usage, tool calling, streaming
- **[openai/codex.md](openai/codex.md)** – Code generation workflows (post-Codex deprecation)

### Anthropic Platform
- **[anthropic/claude-agent-sdk.md](anthropic/claude-agent-sdk.md)** – Full SDK documentation with patterns for file ops, tools, hooks
- **[anthropic/claude-code.md](anthropic/claude-code.md)** – Claude Code CLI and web interface
- **[anthropic/petri.md](anthropic/petri.md)** – Petri orchestration system

### Google Platform
- **[google/adk.md](google/adk.md)** – Complete ADK documentation for Python and Java
- **[google/a2a.md](google/a2a.md)** – Agent2Agent protocol for cross-framework communication

### Related Protocols
- **Model Context Protocol (MCP)**: Standardized tool integration across all SDKs
- **Agent2Agent (A2A)**: Cross-framework agent communication protocol

---

## Installation Quick Reference

### OpenAI Agents SDK
```bash
pip install openai-agents
# Optional: pip install openai-agents[redis]
```

### Claude Agent SDK
```bash
pip install claude-agent-sdk
# Requires: Python 3.10+, Node.js, Claude Code 2.0.0+
```

### Google ADK
```bash
# Python
pip install google-adk

# Java (Maven)
<dependency>
  <groupId>com.google.adk</groupId>
  <artifactId>adk-java</artifactId>
  <version>0.3.0</version>
</dependency>
```

---

## Cost Optimization

### Model Pricing (Approximate, 2025-10-31)

| Provider | Model | Input ($/1M) | Output ($/1M) |
|----------|-------|--------------|---------------|
| OpenAI | gpt-4.1-mini | $0.40 | $1.20 |
| Anthropic | claude-3-5-haiku | $0.80 | $4.00 |
| Google | gemini-2.5-flash | $0.075 | $0.30 |

**Optimization Tips**:
- **OpenAI**: Use provider flexibility to route to cheapest option
- **Claude**: Context compaction reduces long-session costs
- **Google**: Use flash models for sub-agents (extremely cheap)

---

## Getting Started

1. **Evaluate your use case** using the recommendations above
2. **Review detailed documentation** for your chosen SDK
3. **Install the SDK** using the quick reference commands
4. **Follow the quick start** example in the SDK documentation
5. **Explore the comparison guide** for advanced architecture decisions

---

## Related Documentation

- **[AGENTS.md](../files/AGENTS.md)**: Operational documentation for AI agents
- **[SSOT.md](../files/SSOT.md)**: Single Source of Truth principles
- **[PLANS.md](../files/PLANS.md)**: Execution planning patterns

---

## Update Log

### 2025-10-31
- **Initial version**: Created comprehensive agent SDK overview
- **Added**: Quick selection guide for all three major SDKs
- **Added**: Feature comparison matrix and use case recommendations
- **Added**: Decision flowchart and installation quick reference
- **Added**: Documentation map with links to all platform-specific guides

---

**Document ID**: `platforms/AGENT_SDKS`
**License**: MIT
