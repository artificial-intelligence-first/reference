# AI Agent Platforms (2025 Edition)

> **For Humans**: This directory contains detailed documentation for major AI agent platforms (OpenAI, Anthropic, Google). Navigate to each platform's subdirectory for SDK documentation, tools, and best practices.
>
> **For AI Agents**: This directory contains canonical documentation for major AI agent platforms. Reference platform-specific subdirectories for detailed SDK documentation, patterns, and implementation guidance.

---

## Platform Directory

### 🔷 OpenAI
**Official Site**: https://platform.openai.com/

OpenAI agent development tools and SDKs:
- **[Agents SDK](openai/agents-sdk.md)** - Official Python/TypeScript SDK using Responses API
- **[AgentKit](openai/agent-kit.md)** - Conceptual scaffolding patterns (community)
- **[Codex](openai/codex.md)** - Code generation workflows (post-Codex deprecation)

**Key Features**:
- Provider-agnostic (supports 100+ LLM providers)
- Built-in session management and guardrails
- Simple agent handoffs for specialization

---

### 🟣 Anthropic
**Official Site**: https://www.anthropic.com/

Anthropic agent development tools and frameworks:
- **[Claude Agent SDK](anthropic/claude-agent-sdk.md)** - Agent SDK built on Claude Code infrastructure
- **[Claude Code](anthropic/claude-code.md)** - CLI and web interface
- **[Petri](anthropic/petri.md)** - Orchestration system

**Key Features**:
- Full computer access (file operations, bash execution)
- Automatic context compaction for long sessions
- Verification-focused approach (linting, visual feedback)

---

### 🔴 Google
**Official Site**: https://ai.google.dev/

Google agent development tools and protocols:
- **[Agent Development Kit (ADK)](google/adk.md)** - Hierarchical multi-agent framework
- **[Agent2Agent (A2A) Protocol](google/a2a.md)** - Cross-framework agent communication protocol

**Key Features**:
- Hierarchical multi-agent architecture
- Python and Java support
- Native Google Cloud (Cloud Run, Vertex AI) integration

---

### 🦜 LangChain
**Official Site**: https://www.langchain.com/

LangChain tools and frameworks:
- Documentation in progress

---

## Quick Comparison

| Feature | OpenAI | Anthropic | Google ADK |
|---------|--------|-----------|------------|
| **Language Support** | Python | Python, TypeScript | Python, Java |
| **Provider Support** | 100+ providers | Anthropic only | Multi-provider |
| **Multi-Agent** | Handoffs (flat) | Subagents (parallel) | Hierarchical (tree) |
| **File Operations** | Limited | Full access | Tool-dependent |
| **Session Management** | Built-in | Manual | Manual |
| **Development UI** | No | Via Claude Code | Built-in |

---

## Use Case Recommendations

### Coding & Software Development
**→ Anthropic Claude Agent SDK** ⭐⭐⭐
- Full file system access
- Bash execution for tests/builds
- Verification with linting

### Customer Support & Chat
**→ OpenAI Agents SDK** ⭐⭐⭐
- Built-in session management
- Native guardrails
- Simple handoffs

### Complex Multi-Agent Systems
**→ Google ADK** ⭐⭐⭐
- Hierarchical architecture
- Parallel execution
- Development UI

### Research & Information Gathering
**→ Anthropic Claude Agent SDK** ⭐⭐⭐
- Bash access for CLI tools
- File operations for documents
- Auto-context compaction for long sessions

### Enterprise Java Applications
**→ Google ADK** ⭐⭐⭐
- Native Java support
- Maven integration
- Strong typing

### Google Cloud Deployment
**→ Google ADK** ⭐⭐⭐
- Native Cloud Run / Vertex AI integration
- Gemini model optimization

### Multi-Provider Flexibility
**→ OpenAI Agents SDK** ⭐⭐⭐
- Supports 100+ providers
- Easy provider switching
- Cost optimization

### Rapid Prototyping
**→ OpenAI Agents SDK** ⭐⭐⭐
- Minimal boilerplate
- Simple API
- Fast iteration cycle

---

## Installation

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

## Documentation Structure

Each platform's documentation follows this structure:

- **TL;DR** - Overview and key points
- **Canonical Definitions** - Formal definitions and concepts
- **Core Patterns** - Implementation patterns with code examples
- **Decision Checklist** - Decision criteria for adoption
- **Anti-patterns / Pitfalls** - Patterns to avoid and common mistakes
- **Evaluation** - Metrics and testing strategies
- **Update Log** - Documentation change history

---

## Related Documentation

- **[AGENTS.md](../files/AGENTS.md)** - Operational documentation for AI agents
- **[SSOT.md](../files/SSOT.md)** - Single Source of Truth principles
- **[PLANS.md](../files/PLANS.md)** - Execution planning patterns

---

## Protocols & Standards

- **Model Context Protocol (MCP)** - Standardized tool integration across all SDKs
- **Agent2Agent (A2A)** - Cross-framework agent communication protocol

---

## Update Log

### 2025-10-31
- Added comprehensive Google ADK documentation
- Reorganized to platform-specific structure
- Added quick comparison table and use case recommendations

### 2025-10-20
- Rebuilt OpenAI SDK documentation to align with Responses API
- Added Codex deprecation warnings
- Marked AgentKit sections as conceptual samples

---

**Remember**: Always verify the latest information with official platform documentation.
