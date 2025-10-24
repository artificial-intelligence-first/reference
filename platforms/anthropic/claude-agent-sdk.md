---
title: Claude Agent SDK
slug: claude-agent-sdk
status: living
last_updated: 2025-10-23
tags: [agents, sdk, anthropic, tool-calling, orchestration]
summary: "SDK for building custom AI agents using the same infrastructure that powers Claude Code, enabling autonomous agents with tool access, file operations, and command execution."
sources:
  - { id: R1, title: "Claude Agent SDK Overview", url: "https://docs.claude.com/en/api/agent-sdk/overview", accessed: "2025-10-23" }
  - { id: R2, title: "Claude Agent SDK Python GitHub", url: "https://github.com/anthropics/claude-agent-sdk-python", accessed: "2025-10-23" }
  - { id: R3, title: "Building Agents with the Claude Agent SDK", url: "https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk", accessed: "2025-10-23" }
---

# Claude Agent SDK

> **For Humans**
> This document explains the Claude Agent SDK, a toolkit for building custom AI agents using the same infrastructure that powers Claude Code. Use this when building autonomous agents that need to interact with files, run commands, access external tools, or execute complex multi-step workflows.

> **For AI Agents**
> This document defines the Claude Agent SDK capabilities, architecture, and implementation patterns. Apply these patterns when building custom agents, implementing tool calling, managing context, or integrating with MCP servers. Cross-reference with AGENTS.md for operational procedures and Claude Code documentation for CLI usage.

---

## Table of Contents

- [TL;DR](#tldr)
- [Canonical Definitions](#canonical-definitions)
- [Core Patterns](#core-patterns)
- [Decision Checklist](#decision-checklist)
- [Anti-patterns / Pitfalls](#anti-patterns--pitfalls)
- [Evaluation](#evaluation)
- [Update Log](#update-log)
- [See Also](#see-also)
- [References](#references)

---

## TL;DR

- **What**: Python and TypeScript SDK for building custom AI agents using Claude Code's infrastructure—file operations, command execution, and tool integration
- **Why**: Enables autonomous agents beyond coding: research, data analysis, visualization, workflow automation, with built-in context management and MCP integration
- **When**: Building custom agents, automating complex workflows, integrating Claude with external tools, requiring programmatic agent control
- **How**: Install via pip/npm, use async streaming API, define custom tools, configure system prompts, extend with MCP servers
- **Watch out**: Renamed from "Claude Code SDK" to "Claude Agent SDK" to reflect broader applications beyond coding—ensure you're using latest documentation

---

## Canonical Definitions

### Claude Agent SDK

**Definition**: A software development kit providing programmatic access to the infrastructure that powers Claude Code, enabling developers to build custom autonomous agents with file operations, command execution, and tool integration [R1][R3].

**Scope**:
- **Includes**: Async streaming API, custom tool creation, system prompt configuration, in-process MCP servers, context management (auto-summarization), session management, file/command/search operations, tool permissions
- **Excludes**: Pre-built agents (use Claude Code for that), model training, non-Claude models

**Languages**:
- **Python**: `claude-agent-sdk` via pip (Python 3.10+)
- **TypeScript**: `@anthropic-ai/claude-agent-sdk` via npm

**Related Concepts**:
- **Powers**: Claude Code CLI and web interface
- **Complements**: Claude API (for basic completions), MCP (for tool integration)
- **Similar**: LangChain AgentExecutor, AutoGPT frameworks
- **Broader than**: Original "Claude Code SDK" name—now supports all autonomous agent use cases

**Sources**: [R1][R2][R3]

### Custom Tools

**Definition**: Python or TypeScript functions offered to Claude for invocation during agent execution, implemented as in-process MCP servers that run directly within your application [R2][R3].

**Scope**: Function-based tools that extend agent capabilities without separate process management.

**Example**:
```python
from claude_agent_sdk import ClaudeSDKClient

def search_database(query: str) -> str:
    """Search the product database for items matching the query."""
    # Implementation here
    return f"Found 5 products matching '{query}'"

client = ClaudeSDKClient()
client.add_tool(search_database)
```

**Sources**: [R2][R3]

### Compact Feature

**Definition**: Automatic context summarization mechanism that condenses previous messages when approaching context limits, enabling long-running agent sessions without context exhaustion [R2][R3].

**Scope**: Built-in context management—no manual intervention required.

**Sources**: [R2][R3]

---

## Core Patterns

### Pattern 1: Simple Query API for Lightweight Use Cases

**Intent**: Use the lightweight `query()` API for simple text generation without tool calling or session management.

**Context**: When you need basic Claude responses without autonomous agent capabilities.

**Implementation**:

```python
from claude_agent_sdk import query
import asyncio

async def simple_generation():
    """
    Use query() for lightweight text generation.
    """
    # Simple async query
    response_stream = query("Explain quantum computing in simple terms")

    # Iterate over streaming response
    async for message in response_stream:
        print(message.content, end="", flush=True)

# Run the async function
asyncio.run(simple_generation())
```

**Key Principles**:
- `query()` returns `AsyncIterator` of response messages [R2]
- No tool calling support—purely for text generation [R2]
- Best for simple, stateless interactions
- Async-first design for streaming responses

**Trade-offs**:
- ✅ **Advantages**: Simple API, minimal setup, fast for basic use cases
- ⚠️ **Disadvantages**: No tools, no session persistence, no agent capabilities
- 💡 **Alternatives**: Use `ClaudeSDKClient` for full agent features

**Sources**: [R2]

### Pattern 2: Full-Featured Agent with ClaudeSDKClient

**Intent**: Build autonomous agents with tool access, session management, and custom workflows using the full SDK client.

**Context**: When you need agents that can execute commands, edit files, and maintain state across interactions.

**Implementation**:

```python
from claude_agent_sdk import ClaudeSDKClient
import asyncio

async def run_agent():
    """
    Create a full-featured agent with tool access.
    """
    # Initialize client with API key
    client = ClaudeSDKClient(
        api_key="your-api-key",
        # Or use AWS Bedrock / Google Vertex AI
    )

    # Define system prompt for agent behavior
    system_prompt = """You are a data analysis assistant.
    You can read CSV files, perform statistical analysis,
    and create visualizations. Be thorough and explain your findings."""

    # Create agent session
    async with client.create_session(
        system_prompt=system_prompt
    ) as session:
        # Send task to agent
        response = await session.send_message(
            "Analyze the sales data in data/sales.csv and create a summary report"
        )

        # Agent will autonomously:
        # 1. Read the CSV file
        # 2. Perform analysis
        # 3. Generate visualizations
        # 4. Create summary report

        # Stream response
        async for chunk in response:
            print(chunk.content, end="", flush=True)

asyncio.run(run_agent())
```

**Key Principles**:
- `ClaudeSDKClient` provides full agent capabilities [R2]
- Sessions maintain state across interactions [R2]
- System prompts define agent role and expertise [R2]
- Agents have access to file operations, bash commands by default [R3]

**Trade-offs**:
- ✅ **Advantages**: Full autonomy, tool access, session persistence, streaming
- ⚠️ **Disadvantages**: More complex setup, requires API key, resource usage
- 💡 **Alternatives**: Use Claude API directly for simple completions

**Sources**: [R2][R3]

### Pattern 3: Custom Tools as In-Process MCP Servers

**Intent**: Extend agent capabilities by providing custom Python functions as tools, without managing separate processes.

**Context**: When agents need access to databases, APIs, or custom logic beyond built-in capabilities.

**Implementation**:

```python
from claude_agent_sdk import ClaudeSDKClient
from typing import List, Dict
import asyncio

# Define custom tool functions
def query_inventory(product_name: str) -> Dict:
    """
    Query the inventory database for product availability.

    Args:
        product_name: Name of the product to check

    Returns:
        Dictionary with stock information
    """
    # Database query logic here
    return {
        "product": product_name,
        "in_stock": 45,
        "warehouse": "West Coast",
        "reorder_level": 20
    }

def place_order(product_name: str, quantity: int, customer_id: str) -> str:
    """
    Place an order for a product.

    Args:
        product_name: Product to order
        quantity: Number of units
        customer_id: Customer identifier

    Returns:
        Order confirmation message
    """
    # Order placement logic
    return f"Order placed: {quantity}x {product_name} for customer {customer_id}"

async def run_inventory_agent():
    """
    Agent with custom inventory tools.
    """
    client = ClaudeSDKClient()

    # Add custom tools
    client.add_tool(query_inventory)
    client.add_tool(place_order)

    system_prompt = """You are an inventory management assistant.
    You can check stock levels and place orders.
    Always verify stock before placing orders."""

    async with client.create_session(system_prompt=system_prompt) as session:
        response = await session.send_message(
            "Check if we have Widget A in stock, and if so, place an order for 10 units for customer C123"
        )

        # Agent will:
        # 1. Call query_inventory("Widget A")
        # 2. Evaluate stock level
        # 3. Call place_order("Widget A", 10, "C123") if sufficient stock
        # 4. Report results

        async for chunk in response:
            print(chunk.content, end="", flush=True)

asyncio.run(run_inventory_agent())
```

**Key Principles**:
- Custom tools are Python functions with type hints and docstrings [R2]
- Tools run in-process—no subprocess management needed [R2]
- Better performance with no IPC overhead [R2]
- Simpler deployment with single Python process [R2]
- Tools are prominent in Claude's context window [R3]

**Trade-offs**:
- ✅ **Advantages**: No subprocess overhead, simple deployment, type-safe, good performance
- ⚠️ **Disadvantages**: Limited to Python/TypeScript, no polyglot tool support
- 💡 **Alternatives**: Use external MCP servers for polyglot tools or isolation

**Sources**: [R2][R3]

### Pattern 4: Tool Permissions and Security

**Intent**: Control which tools agents can access with fine-grained permissions for security and safety.

**Context**: When running agents in production or with access to sensitive operations.

**Implementation**:

```python
from claude_agent_sdk import ClaudeSDKClient, ToolPermission
import asyncio

async def run_restricted_agent():
    """
    Agent with restricted tool access.
    """
    client = ClaudeSDKClient()

    # Define tools
    def read_file(path: str) -> str:
        """Read file contents."""
        with open(path) as f:
            return f.read()

    def write_file(path: str, content: str) -> str:
        """Write content to file."""
        with open(path, 'w') as f:
            f.write(content)
        return f"Wrote to {path}"

    def delete_file(path: str) -> str:
        """Delete a file."""
        import os
        os.remove(path)
        return f"Deleted {path}"

    # Add tools with permissions
    client.add_tool(read_file, permission=ToolPermission.ALWAYS)
    client.add_tool(write_file, permission=ToolPermission.REQUIRE_APPROVAL)
    client.add_tool(delete_file, permission=ToolPermission.NEVER)

    system_prompt = "You are a file analysis assistant. You can read and analyze files."

    async with client.create_session(system_prompt=system_prompt) as session:
        response = await session.send_message(
            "Analyze the log file at logs/app.log"
        )

        # Agent can:
        # - Read files freely (ALWAYS)
        # - Write files with approval (REQUIRE_APPROVAL)
        # - Cannot delete files (NEVER)

        async for chunk in response:
            if chunk.requires_approval:
                # Prompt user for approval
                approved = input(f"Approve {chunk.tool_name}? (y/n): ") == 'y'
                await session.approve(chunk.approval_id, approved)
            else:
                print(chunk.content, end="", flush=True)

asyncio.run(run_restricted_agent())
```

**Key Principles**:
- Control tool access with permission levels [R2]
- `ALWAYS`: Tool available without approval
- `REQUIRE_APPROVAL`: User must approve each invocation
- `NEVER`: Tool not available to agent
- Implement approval callbacks for interactive permission

**Trade-offs**:
- ✅ **Advantages**: Enhanced security, audit trail, user control, prevents dangerous operations
- ⚠️ **Disadvantages**: Adds interaction overhead, requires approval UI
- 💡 **Alternatives**: Use environment isolation (containers) for high-risk operations

**Sources**: [R2]

### Pattern 5: Context Management with Compact Feature

**Intent**: Enable long-running agent sessions without hitting context limits through automatic summarization.

**Context**: When agents need to work on extended tasks that would normally exceed context windows.

**Implementation**:

```python
from claude_agent_sdk import ClaudeSDKClient
import asyncio

async def long_running_agent():
    """
    Agent that works on extended task with automatic context management.
    """
    client = ClaudeSDKClient()

    system_prompt = """You are a comprehensive code reviewer.
    Review each file thoroughly for bugs, style, performance, and security."""

    async with client.create_session(
        system_prompt=system_prompt,
        compact=True  # Enable automatic summarization
    ) as session:
        # Long-running task reviewing many files
        files_to_review = [
            "src/auth/login.py",
            "src/auth/register.py",
            "src/auth/password.py",
            "src/api/users.py",
            "src/api/posts.py",
            # ... many more files
        ]

        for file_path in files_to_review:
            response = await session.send_message(
                f"Review {file_path} for issues"
            )

            # Compact feature automatically summarizes old messages
            # when approaching context limit, so agent never runs out

            async for chunk in response:
                print(chunk.content, end="", flush=True)

            print(f"\n--- Completed {file_path} ---\n")

asyncio.run(long_running_agent())
```

**Key Principles**:
- Enable with `compact=True` in session creation [R2]
- Automatic summarization when approaching context limits [R2][R3]
- No manual context management needed [R3]
- Maintains task coherence across summaries
- Critical for long-running agents

**Trade-offs**:
- ✅ **Advantages**: No context exhaustion, enables extended tasks, automatic, maintains coherence
- ⚠️ **Disadvantages**: Some detail loss in summarization, slight latency during compaction
- 💡 **Alternatives**: Manual context windowing or breaking tasks into smaller chunks

**Sources**: [R2][R3]

### Pattern 6: MCP Server Integration

**Intent**: Connect agents to external MCP servers for access to databases, APIs, and third-party services.

**Context**: When agents need capabilities beyond custom tools, like database access or API integrations.

**Implementation**:

```python
from claude_agent_sdk import ClaudeSDKClient
import asyncio

async def agent_with_mcp_servers():
    """
    Agent connected to external MCP servers.
    """
    client = ClaudeSDKClient()

    # Connect to MCP servers
    await client.connect_mcp_server(
        name="github",
        command="npx",
        args=["-y", "@modelcontextprotocol/server-github"],
        env={"GITHUB_TOKEN": "ghp_..."}
    )

    await client.connect_mcp_server(
        name="postgres",
        command="npx",
        args=["-y", "@modelcontextprotocol/server-postgres"],
        env={"DATABASE_URL": "postgresql://..."}
    )

    system_prompt = """You are a DevOps assistant.
    You can query databases and interact with GitHub."""

    async with client.create_session(system_prompt=system_prompt) as session:
        response = await session.send_message(
            "Check GitHub for open security issues, then query the database for affected users"
        )

        # Agent can now use:
        # - GitHub tools (list issues, PRs, etc.)
        # - Database tools (queries, updates, etc.)

        async for chunk in response:
            print(chunk.content, end="", flush=True)

asyncio.run(agent_with_mcp_servers())
```

**Key Principles**:
- Connect to external MCP servers programmatically [R2]
- MCP servers provide additional tools to agent
- Support for standard MCP server ecosystem
- Authentication via environment variables
- Can combine in-process tools with external MCP servers

**Trade-offs**:
- ✅ **Advantages**: Access to broad MCP ecosystem, standardized protocol, polyglot support
- ⚠️ **Disadvantages**: Subprocess management, IPC overhead, complex setup
- 💡 **Alternatives**: Use in-process custom tools for Python/TypeScript logic

**Sources**: [R2]

---

## Decision Checklist

Use Claude Agent SDK when:

- [ ] **Custom Agent Development**: You need to build autonomous agents with specific behaviors [R1][R3]
  - Verify: Requirements go beyond Claude Code CLI capabilities
  - Impact: Claude Code may be sufficient for interactive use

- [ ] **Programmatic Control**: You need to integrate agent capabilities into applications [R2]
  - Verify: Need to embed agents in existing systems or workflows
  - Impact: Claude API may be sufficient for simple completions

- [ ] **Tool Integration**: Agents need access to custom tools, databases, or APIs [R2][R3]
  - Verify: Built-in file/command operations are insufficient
  - Impact: Manual API integration would be required otherwise

- [ ] **Session Management**: You need persistent agent sessions with state [R2]
  - Verify: Multi-turn interactions with context maintenance
  - Impact: Stateless API calls lose context between interactions

- [ ] **Beyond Coding**: Use cases extend beyond code generation (research, data analysis, automation) [R3]
  - Verify: Need agent capabilities for non-coding workflows
  - Impact: Claude API with function calling may be alternative

**Decision Matrix**:

| Scenario | Use Agent SDK | Alternative | Reason |
|----------|--------------|-------------|--------|
| Building custom agents | ✅ Yes | Claude API + custom code | SDK provides batteries-included agent infrastructure |
| Interactive coding | ⚠️ Optional | Claude Code CLI | CLI is simpler for interactive use |
| Simple completions | ❌ No | Claude API | SDK overhead unnecessary |
| Tool calling required | ✅ Yes | Claude API function calling | SDK has richer tool ecosystem |
| Long-running tasks | ✅ Yes | Manual context management | Compact feature handles automatically |
| Production deployment | ✅ Yes | Custom agent framework | Battle-tested infrastructure from Claude Code |

---

## Anti-patterns / Pitfalls

### Anti-pattern 1: Using SDK for Simple Completions

**Symptom**: Initializing full `ClaudeSDKClient` and agent sessions for basic text generation.

**Why It Happens**: Assuming SDK is always the best way to use Claude.

**Impact**:
- Unnecessary overhead and complexity
- Slower response times
- Higher resource usage
- More expensive API calls

**Solution**: Use Claude API directly or lightweight `query()` function for simple use cases [R2].

**Example**:

```python
# ❌ Anti-pattern: Overkill for simple completion
from claude_agent_sdk import ClaudeSDKClient
import asyncio

async def generate_summary():
    client = ClaudeSDKClient()
    async with client.create_session() as session:
        response = await session.send_message("Summarize this text...")
        async for chunk in response:
            print(chunk.content)

# ✅ Correct pattern: Use query() for simple generation
from claude_agent_sdk import query
import asyncio

async def generate_summary():
    response = query("Summarize this text...")
    async for chunk in response:
        print(chunk.content, end="")

# ✅ Or use Claude API directly
from anthropic import Anthropic

client = Anthropic()
response = client.messages.create(
    model="claude-sonnet-4.5",
    messages=[{"role": "user", "content": "Summarize this text..."}]
)
print(response.content[0].text)
```

**Sources**: [R2]

### Anti-pattern 2: Over-Permissive Tool Access

**Symptom**: Giving agents unrestricted access to all tools without permission controls.

**Why It Happens**: Trusting AI completely, prioritizing convenience over security.

**Impact**:
- Accidental data deletion or corruption
- Unauthorized API calls or operations
- Security vulnerabilities
- Production incidents

**Solution**: Implement least-privilege tool permissions [R2].

**Example**:

```python
# ❌ Anti-pattern: Unrestricted dangerous tools
client = ClaudeSDKClient()

def delete_database(db_name: str):
    """Delete entire database."""
    # Dangerous operation
    pass

def charge_customer(customer_id: str, amount: float):
    """Charge customer credit card."""
    # Financial operation
    pass

# Added without restrictions!
client.add_tool(delete_database)
client.add_tool(charge_customer)

# ✅ Correct pattern: Restricted permissions
from claude_agent_sdk import ToolPermission

client = ClaudeSDKClient()

# Require approval for dangerous operations
client.add_tool(delete_database, permission=ToolPermission.NEVER)  # Disabled
client.add_tool(charge_customer, permission=ToolPermission.REQUIRE_APPROVAL)

# Read-only operations can be ALWAYS
def get_customer_info(customer_id: str):
    """Get customer information."""
    pass

client.add_tool(get_customer_info, permission=ToolPermission.ALWAYS)
```

**Sources**: [R2]

### Anti-pattern 3: Ignoring Async/Await Patterns

**Symptom**: Attempting to use SDK synchronously or blocking on async operations incorrectly.

**Why It Happens**: Unfamiliarity with Python async programming.

**Impact**:
- Runtime errors
- Blocked event loops
- Poor performance
- Application hangs

**Solution**: Properly use async/await patterns throughout [R2].

**Example**:

```python
# ❌ Anti-pattern: Incorrect async usage
from claude_agent_sdk import ClaudeSDKClient

def run_agent():  # Not async!
    client = ClaudeSDKClient()
    session = client.create_session()  # Missing await!
    response = session.send_message("Hello")  # Missing await!
    print(response)  # Won't work

# ✅ Correct pattern: Proper async/await
from claude_agent_sdk import ClaudeSDKClient
import asyncio

async def run_agent():  # Async function
    client = ClaudeSDKClient()
    async with client.create_session() as session:  # Async context manager
        response = await session.send_message("Hello")  # Await async call
        async for chunk in response:  # Async iteration
            print(chunk.content, end="")

# Run with asyncio
asyncio.run(run_agent())
```

**Sources**: [R2]

### Anti-pattern 4: Vague System Prompts

**Symptom**: Minimal or generic system prompts that don't guide agent behavior effectively.

**Why It Happens**: Underestimating importance of system prompts, rushing setup.

**Impact**:
- Inconsistent agent behavior
- Poor task completion
- Unexpected tool usage
- Low-quality outputs

**Solution**: Write detailed, specific system prompts defining role, expertise, and constraints [R2][R3].

**Example**:

```python
# ❌ Anti-pattern: Vague system prompt
system_prompt = "You are a helpful assistant."

# ✅ Correct pattern: Detailed system prompt
system_prompt = """You are a financial data analyst assistant specialized in quarterly earnings reports.

**Your expertise:**
- Reading and parsing financial CSV files and Excel spreadsheets
- Calculating key metrics (revenue growth, profit margins, EPS)
- Creating visualizations (line charts, bar charts, trend analysis)
- Identifying anomalies and trends in financial data

**Your workflow:**
1. Always start by examining the data structure and quality
2. Calculate requested metrics with clear explanations
3. Create visualizations to support findings
4. Provide actionable insights and recommendations
5. Document assumptions and limitations

**Constraints:**
- Only work with files in the data/ directory
- Always validate data before analysis
- Flag any missing or suspicious data points
- Use standard accounting principles

**Communication style:**
- Be precise with numbers (2 decimal places for percentages)
- Explain financial terms for non-experts
- Provide context for metrics and trends
"""
```

**Sources**: [R2][R3]

---

## Evaluation

### Metrics

**Primary Metrics**:
- **Task Completion Rate**: Percentage of agent tasks completed successfully
  - Target: >85% completion rate
  - Measurement: `(completed_tasks / total_tasks) * 100`

- **Tool Invocation Accuracy**: Percentage of correct tool selections
  - Target: >90% accuracy
  - Measurement: Manual review of tool usage appropriateness

- **Development Velocity**: Time to build and deploy custom agents
  - Target: <50% time compared to custom implementation
  - Measurement: Track development hours

**Secondary Metrics**:
- API usage and cost
- Context window utilization
- Agent response latency
- Error rate and recovery

### Testing Strategies

**Unit Tests**:
```python
import pytest
from claude_agent_sdk import ClaudeSDKClient

@pytest.mark.asyncio
async def test_custom_tool_invocation():
    """Verify custom tools are called correctly."""
    client = ClaudeSDKClient()

    # Define test tool
    def test_tool(input: str) -> str:
        return f"Processed: {input}"

    client.add_tool(test_tool)

    async with client.create_session() as session:
        response = await session.send_message("Use test_tool with 'hello'")
        result = await response.get_full_response()

        assert "Processed: hello" in result

@pytest.mark.asyncio
async def test_tool_permissions():
    """Verify tool permissions are enforced."""
    from claude_agent_sdk import ToolPermission

    client = ClaudeSDKClient()

    def restricted_tool():
        return "Should not execute"

    client.add_tool(restricted_tool, permission=ToolPermission.NEVER)

    async with client.create_session() as session:
        response = await session.send_message("Use restricted_tool")
        result = await response.get_full_response()

        assert "cannot use that tool" in result.lower()
```

**Integration Tests**:
```python
@pytest.mark.asyncio
@pytest.mark.integration
async def test_agent_workflow():
    """Test complete agent workflow with multiple tools."""
    client = ClaudeSDKClient()

    # Set up tools
    def read_data():
        return "sample data"

    def analyze_data(data):
        return "analysis result"

    client.add_tool(read_data)
    client.add_tool(analyze_data)

    async with client.create_session(
        system_prompt="You are a data analyst."
    ) as session:
        response = await session.send_message(
            "Read the data and perform analysis"
        )

        result = await response.get_full_response()

        # Verify workflow completion
        assert "analysis result" in result
```

**Performance Benchmarks**:
```bash
# Measure agent response latency
python benchmark_agent.py --iterations 100

# Measure context compaction efficiency
python benchmark_compact.py --messages 1000

# Measure tool invocation overhead
python benchmark_tools.py --tool-count 50
```

### Success Criteria

- [ ] Agent task completion rate >85%
- [ ] Tool invocation accuracy >90%
- [ ] Development time reduced by >50% vs custom implementation
- [ ] Context never exhausted with compact feature enabled
- [ ] Zero critical security incidents with proper permissions
- [ ] Agent API cost within budget constraints

**Sources**: [R1][R2][R3]

---

## Update Log

### 2025-10-23
- **Initial version**: Comprehensive Claude Agent SDK documentation
- **Added**: Canonical definitions for Claude Agent SDK, Custom Tools, and Compact Feature
- **Added**: Core patterns for query API, ClaudeSDKClient, custom tools, permissions, context management, and MCP integration
- **Added**: Decision checklist, anti-patterns, and evaluation criteria
- **Noted**: SDK rename from "Claude Code SDK" to "Claude Agent SDK" to reflect broader applications
- **Sources**: [R1][R2][R3]

---

## See Also

### Prerequisites
- [AGENTS.md](../../files/AGENTS.md): Operational documentation for AI agents
- [Claude Code](./claude-code.md): CLI and web interface built on this SDK

### Related Topics
- [Context Engineering](../../engineering/context.md): Managing context in AI interactions
- [Prompt Engineering](../../engineering/prompt.md): Effective prompt design patterns
- [SKILL.md](../../files/SKILL.md): Agent Skills specification

### Related Protocols
- **Model Context Protocol (MCP)**: For connecting agents to external tools and services
- **Agent2Agent (A2A)**: For multi-agent communication (see [platforms/google/a2a.md](../google/a2a.md))

### Platform-Specific
- **Claude API**: Direct API access at docs.anthropic.com
- **Anthropic Cookbook**: Code examples at github.com/anthropics/anthropic-cookbook
- **Claude Models**: Sonnet 4.5 recommended for agents

---

## References

- [R1] Anthropic. "Agent SDK Overview." Claude Documentation. https://docs.claude.com/en/api/agent-sdk/overview (accessed 2025-10-23)
- [R2] Anthropic. "Claude Agent SDK Python." GitHub. https://github.com/anthropics/claude-agent-sdk-python (accessed 2025-10-23)
- [R3] Anthropic. "Building Agents with the Claude Agent SDK." Anthropic Engineering Blog. https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk (accessed 2025-10-23)

---

**Document ID**: `platforms/anthropic/claude-agent-sdk`
**Canonical URL**: `https://github.com/artificial-intelligence-first/ssot/blob/main/platforms/anthropic/claude-agent-sdk.md`
**License**: MIT
