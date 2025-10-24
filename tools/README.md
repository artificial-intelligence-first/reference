# AI Development Tools & Protocols

> **For Humans**: This directory contains guides for tools, protocols, and standards that enable AI systems to interact with external environments.
>
> **For AI Agents**: Reference these guides when implementing integrations, communication protocols, or tool-based interactions with external systems.

---

## Overview

This directory covers:
- **Communication Protocols**: Standards for AI-to-system and AI-to-AI communication
- **Integration Frameworks**: Tools and patterns for connecting AI to external data and services
- **Standardization Efforts**: Open protocols and specifications for interoperability

---

## Available Guides

### [Model Context Protocol (MCP)](mcp.md)
**Open standard for connecting LLM applications to external data and tools**

- Client-server architecture with JSON-RPC 2.0
- Five core primitives: Prompts, Resources, Tools, Roots, Sampling
- Official SDKs for Python, TypeScript, C#, Java, and Go
- Pre-built servers for GitHub, PostgreSQL, Google Drive, Slack, and more
- Security considerations and best practices
- Integration with Claude Desktop, OpenAI Agents SDK, and custom applications

**Use when**: Building AI applications that need standardized access to external data sources, tools, or services; creating reusable integrations across multiple AI platforms.

**Introduced**: November 2024 by Anthropic
**Adopted by**: OpenAI (March 2025), Microsoft, Google

---

## Related Directories

### Core Topics
- [AGENTS.md](../files/AGENTS.md): AI agent operational documentation conventions
- [SKILL.md](../files/SKILL.md): Anthropic Agent Skills for domain expertise packaging

### Engineering
- [Context Engineering](../engineering/context.md): Context retrieval patterns that complement MCP resources
- [Prompt Engineering](../engineering/prompt.md): Designing prompts for MCP-enabled agents

### Platforms
- [Claude Code](../platforms/anthropic/claude-code.md): MCP integration with Claude Desktop
- [Claude Agent SDK](../platforms/anthropic/claude-agent-sdk.md): Building custom agents with MCP support
- [OpenAI Agents SDK](../platforms/openai/agents-sdk.md): MCP support in OpenAI agents
- [Agent2Agent Protocol](../platforms/google/a2a.md): Cross-agent communication standard
- [LangChain](../platforms/langchain/langchain.md): LLM framework with potential MCP integration
- [LangGraph](../platforms/langchain/langgraph.md): Multi-agent orchestration with MCP

---

## Future Content

Planned additions to this directory:

### Communication Protocols
- **WebSocket Patterns**: Real-time bidirectional communication for AI agents
- **gRPC for AI**: High-performance RPC for AI service communication
- **Server-Sent Events (SSE)**: Streaming updates to AI clients

### Integration Frameworks
- **Function Calling Standards**: Cross-platform tool invocation patterns
- **RAG Integration Patterns**: Retrieval-Augmented Generation best practices
- **Vector Database Protocols**: Standard interfaces for embedding storage and search

### Tool Ecosystems
- **OpenAI Function Calling**: OpenAI's tool integration approach
- **Anthropic Tool Use**: Claude's tool calling patterns
- **LangChain Tools**: Tool abstractions in LangChain ecosystem

### Security & Governance
- **Tool Permission Models**: Fine-grained access control for AI tools
- **Audit Logging Standards**: Tracking AI tool usage and data access
- **Credential Management**: Secure secret handling for AI integrations

---

## Contributing

To propose new tools or protocols for this directory:

1. **Verify it's a cross-platform tool or protocol** (not platform-specific)
2. **Check it has broad adoption** or is an emerging standard
3. **Ensure substantial documentation exists** (>2000 words potential)
4. **Open an issue** using the New Topic template
5. **Follow [Contributing Guidelines](../_meta/CONTRIBUTING.md)**

**Platform-specific tools** should go in `platforms/{vendor}/` instead.
**File-level concepts** may belong in `files/` instead.

---

## Quick Reference

| Tool/Protocol | Purpose | Best For |
|---------------|---------|----------|
| **MCP** | LLM-to-external-system integration | Standardized data access, tool calling, cross-platform AI apps |

---

**Last Updated**: 2025-10-24
**Maintainer**: AI-First Development Team
