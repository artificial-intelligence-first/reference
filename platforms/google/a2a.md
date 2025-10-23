---
title: Agent2Agent (A2A) Protocol
slug: a2a-protocol
status: living
last_updated: 2025-10-23
tags: [agents, orchestration, apis, google, conventions]
summary: "Open protocol enabling secure, intelligent communication and interoperability between AI agents from different frameworks and providers."
sources:
  - { id: R1, title: "A2A Protocol Specification", url: "https://a2a-protocol.org/dev/specification/", accessed: "2025-10-23" }
  - { id: R2, title: "A2A GitHub Repository", url: "https://github.com/a2aproject/A2A", accessed: "2025-10-23" }
  - { id: R3, title: "Linux Foundation A2A Announcement", url: "https://www.linuxfoundation.org/press/linux-foundation-launches-the-agent2agent-protocol-project-to-enable-secure-intelligent-communication-between-ai-agents", accessed: "2025-10-23" }
  - { id: R4, title: "Google Developers Blog: A2A Protocol", url: "https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/", accessed: "2025-10-23" }
---

# Agent2Agent (A2A) Protocol

> **For Humans**
> This document explains the Agent2Agent (A2A) protocol, an open standard for AI agent communication and interoperability. Use this when building multi-agent systems, enabling agent collaboration across different frameworks, or designing agent-to-agent communication patterns.

> **For AI Agents**
> This document defines the canonical A2A protocol specification for agent-to-agent communication. Apply these patterns when implementing agent interoperability, designing multi-agent systems, or integrating agents from different providers. Cross-reference with AGENTS.md for operational procedures and orchestration patterns.

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

- **What**: Open protocol for standardized communication between AI agents from different frameworks and providers using JSON-RPC 2.0 over HTTP
- **Why**: Enables agent interoperability, collaboration, and orchestration across heterogeneous AI systems without revealing internal implementation details
- **When**: Building multi-agent systems, integrating agents from different vendors, enabling agent discovery and task delegation
- **How**: Implement JSON-RPC 2.0 endpoints, publish Agent Cards for discovery, support synchronous/asynchronous/streaming interactions
- **Watch out**: Protocol is async-first; design for long-running tasks and human-in-the-loop interactions, not just request-response patterns

---

## Canonical Definitions

### Agent2Agent (A2A) Protocol

**Definition**: An open standard protocol enabling secure, intelligent communication and interoperability between independent AI agent systems, regardless of their underlying frameworks or providers [R1][R4].

**Scope**:
- **Includes**: Agent-to-agent communication, capability discovery (Agent Cards), task delegation, synchronous and asynchronous interactions, streaming responses, opaque execution models
- **Excludes**: Agent-to-tool communication (see MCP protocol), internal agent implementation details, model-specific APIs, training or fine-tuning protocols

**Related Concepts**:
- **Complementary**: Model Context Protocol (MCP) for agent-to-tool communication [R1]
- **Similar**: OpenAPI for REST APIs, gRPC for service-to-service communication
- **Contains**: JSON-RPC 2.0 messaging, Agent Cards, Server-Sent Events (SSE) for streaming

**Sources**: [R1][R3][R4]

### Agent Card

**Definition**: A machine-readable document describing an agent's capabilities, connection information, and supported interaction patterns [R1].

**Scope**: Used for agent discovery and capability negotiation before establishing communication.

**Example**:
```json
{
  "name": "ResearchAgent",
  "version": "1.0.0",
  "description": "Performs web research and information synthesis",
  "capabilities": ["search", "summarize", "extract"],
  "endpoint": "https://api.example.com/agents/research",
  "protocols": ["a2a-v1"],
  "authentication": {
    "type": "bearer"
  }
}
```

**Sources**: [R1]

### Opaque Execution

**Definition**: A design principle where agents collaborate based on declared capabilities and exchanged information without sharing internal thoughts, plans, reasoning traces, or tool implementations [R1][R4].

**Scope**: Enables interoperability across agents with different architectures (reasoning, non-reasoning, symbolic, neural) while maintaining encapsulation and intellectual property protection.

**Sources**: [R1][R4]

---

## Core Patterns

### Pattern 1: JSON-RPC 2.0 Communication

**Intent**: Provide standardized request-response messaging between agents using a well-established protocol.

**Context**: When agents need to invoke capabilities on other agents with structured parameters and responses.

**Implementation**:

```python
import requests
import json

def call_agent(agent_endpoint: str, method: str, params: dict) -> dict:
    """
    Send JSON-RPC 2.0 request to another agent.
    """
    payload = {
        "jsonrpc": "2.0",
        "method": method,
        "params": params,
        "id": "req-123"
    }

    response = requests.post(
        agent_endpoint,
        json=payload,
        headers={"Content-Type": "application/json"}
    )

    return response.json()

# Example: Call a research agent
result = call_agent(
    "https://api.example.com/agents/research",
    "search",
    {"query": "Agent2Agent protocol", "max_results": 5}
)

print(result["result"])
```

**Key Principles**:
- Use JSON-RPC 2.0 standard for all agent-to-agent communication [R1]
- Transport via HTTP(S) for universal compatibility
- Include structured error handling with JSON-RPC error codes

**Trade-offs**:
- ✅ **Advantages**: Well-established standard, broad language support, simple to implement
- ⚠️ **Disadvantages**: HTTP overhead for high-frequency communication, no built-in bidirectional streaming
- 💡 **Alternatives**: Use Server-Sent Events (SSE) for streaming responses

**Sources**: [R1]

### Pattern 2: Agent Discovery via Agent Cards

**Intent**: Enable agents to discover capabilities of other agents before establishing communication.

**Context**: In multi-agent systems where agents need to find suitable collaborators for specific tasks.

**Implementation**:

```python
import requests
from typing import List, Dict

class AgentDiscovery:
    """
    Discover and select agents based on required capabilities.
    """
    def __init__(self, registry_url: str):
        self.registry_url = registry_url

    def discover_agents(self, required_capabilities: List[str]) -> List[Dict]:
        """
        Find agents that support all required capabilities.
        """
        # Fetch available agent cards from registry
        response = requests.get(f"{self.registry_url}/agents")
        agent_cards = response.json()

        # Filter agents by capabilities
        matching_agents = []
        for card in agent_cards:
            if all(cap in card.get("capabilities", []) for cap in required_capabilities):
                matching_agents.append(card)

        return matching_agents

    def select_agent(self, required_capabilities: List[str]) -> Dict:
        """
        Select the best agent for the task.
        """
        agents = self.discover_agents(required_capabilities)

        if not agents:
            raise ValueError(f"No agent found with capabilities: {required_capabilities}")

        # Simple selection: return first match
        # In production, consider load balancing, health checks, etc.
        return agents[0]

# Example usage
discovery = AgentDiscovery("https://agent-registry.example.com")
research_agent = discovery.select_agent(["search", "summarize"])

print(f"Selected agent: {research_agent['name']}")
print(f"Endpoint: {research_agent['endpoint']}")
```

**Key Principles**:
- Publish Agent Cards with accurate capability descriptions [R1]
- Update Agent Cards when capabilities change
- Include versioning information for compatibility checking

**Trade-offs**:
- ✅ **Advantages**: Dynamic discovery, loose coupling, capability-based routing
- ⚠️ **Disadvantages**: Requires registry infrastructure, potential for stale information
- 💡 **Alternatives**: Static configuration for known, stable agent topologies

**Sources**: [R1][R4]

### Pattern 3: Asynchronous Task Delegation

**Intent**: Enable long-running tasks with human-in-the-loop interactions and deferred responses.

**Context**: When tasks require significant processing time, external approvals, or multi-step workflows.

**Implementation**:

```python
import requests
import time
from typing import Optional

class AsyncAgentClient:
    """
    Client for asynchronous agent task delegation.
    """
    def __init__(self, agent_endpoint: str):
        self.agent_endpoint = agent_endpoint

    def submit_task(self, method: str, params: dict) -> str:
        """
        Submit task and receive task ID for polling.
        """
        payload = {
            "jsonrpc": "2.0",
            "method": method,
            "params": params,
            "id": "task-001"
        }

        response = requests.post(
            self.agent_endpoint,
            json=payload,
            headers={"Content-Type": "application/json"}
        )

        result = response.json()
        return result["result"]["task_id"]

    def poll_status(self, task_id: str) -> dict:
        """
        Poll task status until completion.
        """
        payload = {
            "jsonrpc": "2.0",
            "method": "get_task_status",
            "params": {"task_id": task_id},
            "id": "status-001"
        }

        response = requests.post(
            self.agent_endpoint,
            json=payload,
            headers={"Content-Type": "application/json"}
        )

        return response.json()["result"]

    def wait_for_completion(self, task_id: str, timeout: int = 300) -> dict:
        """
        Wait for task completion with polling.
        """
        start_time = time.time()

        while time.time() - start_time < timeout:
            status = self.poll_status(task_id)

            if status["state"] == "completed":
                return status["result"]
            elif status["state"] == "failed":
                raise Exception(f"Task failed: {status.get('error')}")

            time.sleep(2)  # Poll every 2 seconds

        raise TimeoutError(f"Task {task_id} did not complete within {timeout}s")

# Example usage
client = AsyncAgentClient("https://api.example.com/agents/research")
task_id = client.submit_task("deep_research", {"topic": "AI safety"})
print(f"Task submitted: {task_id}")

result = client.wait_for_completion(task_id)
print(f"Task completed: {result}")
```

**Key Principles**:
- Design for asynchronous-first interactions [R1]
- Provide status endpoints for task tracking
- Support cancellation and timeout mechanisms
- Enable human-in-the-loop approvals when necessary

**Trade-offs**:
- ✅ **Advantages**: Supports long-running tasks, better resource utilization, enables human oversight
- ⚠️ **Disadvantages**: More complex client implementation, requires task state management
- 💡 **Alternatives**: Use Server-Sent Events for real-time progress updates instead of polling

**Sources**: [R1]

### Pattern 4: Streaming Responses with Server-Sent Events

**Intent**: Provide real-time progress updates and incremental results for long-running agent tasks.

**Context**: When clients need to show progress, intermediate results, or interactive feedback during task execution.

**Implementation**:

```python
import requests
import json

def stream_agent_response(agent_endpoint: str, method: str, params: dict):
    """
    Stream agent response using Server-Sent Events (SSE).
    """
    payload = {
        "jsonrpc": "2.0",
        "method": method,
        "params": params,
        "id": "stream-001"
    }

    # Request with SSE Accept header
    response = requests.post(
        agent_endpoint,
        json=payload,
        headers={
            "Content-Type": "application/json",
            "Accept": "text/event-stream"
        },
        stream=True
    )

    # Process SSE stream
    for line in response.iter_lines():
        if line:
            decoded_line = line.decode('utf-8')

            if decoded_line.startswith('data: '):
                data = decoded_line[6:]  # Remove 'data: ' prefix

                if data == '[DONE]':
                    break

                try:
                    event = json.loads(data)
                    yield event
                except json.JSONDecodeError:
                    continue

# Example usage
for event in stream_agent_response(
    "https://api.example.com/agents/writer",
    "generate_article",
    {"topic": "A2A Protocol", "length": "long"}
):
    if event.get("type") == "progress":
        print(f"Progress: {event['message']}")
    elif event.get("type") == "chunk":
        print(event["content"], end="", flush=True)
    elif event.get("type") == "complete":
        print(f"\nCompleted: {event['metadata']}")
```

**Key Principles**:
- Use Server-Sent Events (SSE) for unidirectional streaming [R1]
- Send structured event types (progress, chunk, error, complete)
- Include metadata for client-side processing
- Handle connection errors and reconnection gracefully

**Trade-offs**:
- ✅ **Advantages**: Real-time updates, better user experience, incremental results
- ⚠️ **Disadvantages**: Requires SSE support, connection management complexity
- 💡 **Alternatives**: Use polling for simpler implementations or WebSocket for bidirectional streaming

**Sources**: [R1]

---

## Decision Checklist

Use A2A protocol when:

- [ ] **Multi-Agent System**: You need multiple AI agents to collaborate on complex tasks [R1][R4]
  - Verify: Identify at least two distinct agent capabilities required for your use case
  - Impact: Single-agent systems don't need inter-agent communication protocols

- [ ] **Heterogeneous Agents**: Agents are built with different frameworks or by different vendors [R3][R4]
  - Verify: Agents use different underlying technologies (e.g., OpenAI, Anthropic, Google, custom frameworks)
  - Impact: Homogeneous systems can use framework-specific communication

- [ ] **Opaque Execution**: Agents should not expose internal reasoning or tool implementations [R1][R4]
  - Verify: Privacy, security, or IP concerns require encapsulation
  - Impact: Transparent systems may use simpler direct integration

- [ ] **Long-Running Tasks**: Tasks require significant processing time or human-in-the-loop interactions [R1]
  - Verify: Tasks take >30 seconds or require external approvals
  - Impact: Short-lived synchronous tasks may not benefit from async patterns

- [ ] **Agent Discovery**: Dynamic agent selection based on capabilities is required [R1]
  - Verify: Agent topology is not fixed or agents join/leave dynamically
  - Impact: Static configurations may not need discovery mechanisms

**Decision Matrix**:

| Scenario | Use A2A | Alternative | Reason |
|----------|---------|-------------|--------|
| Multi-vendor agent integration | ✅ Yes | Direct API integration | A2A provides standard interoperability |
| Single framework multi-agent | ⚠️ Optional | Framework-native communication | A2A adds future flexibility |
| Agent-to-tool communication | ❌ No | Model Context Protocol (MCP) | MCP designed specifically for tool use |
| Real-time chat between agents | ✅ Yes | WebSocket custom protocol | A2A supports streaming via SSE |
| Batch job orchestration | ⚠️ Optional | Workflow engines (Airflow, Temporal) | Depends on coordination complexity |

---

## Anti-patterns / Pitfalls

### Anti-pattern 1: Treating A2A as Synchronous-Only

**Symptom**: Implementing only request-response patterns without support for long-running tasks or streaming.

**Why It Happens**: Developers familiar with REST APIs default to synchronous patterns.

**Impact**:
- Tasks timeout on long-running operations
- Poor user experience with no progress feedback
- Resource waste from connection holding

**Solution**: Implement asynchronous task delegation with status polling or streaming responses [R1].

**Example**:

```python
# ❌ Anti-pattern: Synchronous-only with timeout issues
def call_agent_sync(endpoint, method, params):
    # This will timeout for long-running tasks
    response = requests.post(endpoint, json={
        "jsonrpc": "2.0",
        "method": method,
        "params": params,
        "id": "1"
    }, timeout=30)  # Fixed 30s timeout
    return response.json()

# ✅ Correct pattern: Async with task tracking
def call_agent_async(endpoint, method, params):
    # Submit task
    response = requests.post(endpoint, json={
        "jsonrpc": "2.0",
        "method": method,
        "params": params,
        "id": "1"
    })
    task_id = response.json()["result"]["task_id"]

    # Poll for completion
    return poll_task_completion(endpoint, task_id)
```

**Sources**: [R1]

### Anti-pattern 2: Exposing Internal Agent Implementation

**Symptom**: Agent Cards or responses include internal reasoning traces, tool names, or implementation details.

**Why It Happens**: Debugging habits or attempting to provide "transparency" to other agents.

**Impact**:
- Breaks opaque execution principle
- Creates tight coupling between agents
- Exposes intellectual property or security details

**Solution**: Share only capabilities, inputs, outputs, and status—never internal execution details [R1][R4].

**Example**:

```python
# ❌ Anti-pattern: Exposing internal details
agent_card = {
    "capabilities": ["research"],
    "internal_tools": ["google_search", "wikipedia_api", "perplexity"],  # Don't expose this
    "reasoning_model": "gpt-4",  # Don't expose this
    "internal_prompts": "..."  # Don't expose this
}

# ✅ Correct pattern: Opaque capabilities only
agent_card = {
    "name": "ResearchAgent",
    "version": "1.0.0",
    "capabilities": ["research", "summarize"],
    "endpoint": "https://api.example.com/agents/research",
    "max_query_length": 500,
    "supported_languages": ["en", "ja", "es"]
}
```

**Sources**: [R1][R4]

### Anti-pattern 3: Using A2A for Agent-to-Tool Communication

**Symptom**: Wrapping tool APIs (search, calculators, databases) as A2A agents.

**Why It Happens**: Attempting to standardize all interactions through a single protocol.

**Impact**:
- Unnecessary abstraction overhead
- Misses tool-specific optimization opportunities
- Conflicts with Model Context Protocol (MCP) ecosystem

**Solution**: Use MCP for agent-to-tool communication and A2A for agent-to-agent communication [R1].

**Example**:

```python
# ❌ Anti-pattern: Wrapping tools as A2A agents
search_agent = {
    "name": "SearchTool",  # This is a tool, not an agent
    "capabilities": ["search"],
    "protocol": "a2a"
}

# ✅ Correct pattern: Use MCP for tools, A2A for agents
# Tools via MCP
search_tool = {
    "type": "mcp_tool",
    "name": "web_search",
    "protocol": "mcp"
}

# Agents via A2A
research_agent = {
    "name": "ResearchAgent",
    "capabilities": ["research", "synthesize"],
    "protocol": "a2a",
    "uses_tools": ["web_search"]  # Uses MCP tools internally
}
```

**Sources**: [R1]

---

## Evaluation

### Metrics

**Primary Metrics**:
- **Agent Interoperability Success Rate**: Percentage of successful cross-framework agent communications
  - Target: >95% success rate
  - Measurement: `(successful_calls / total_calls) * 100`

- **Discovery Latency**: Time to discover and connect to suitable agent
  - Target: <500ms for discovery, <2s for first communication
  - Measurement: Track time from capability query to first successful RPC call

- **Task Completion Rate**: Percentage of delegated tasks that complete successfully
  - Target: >90% completion rate
  - Measurement: `(completed_tasks / submitted_tasks) * 100`

**Secondary Metrics**:
- Communication overhead compared to direct API integration
- Agent Card freshness and accuracy
- Error recovery success rate

### Testing Strategies

**Unit Tests**:
```python
def test_json_rpc_message_format():
    """Verify A2A messages conform to JSON-RPC 2.0 spec."""
    message = create_a2a_message("search", {"query": "test"})
    assert message["jsonrpc"] == "2.0"
    assert "method" in message
    assert "params" in message
    assert "id" in message

def test_agent_card_schema():
    """Verify Agent Card contains required fields."""
    card = load_agent_card("research_agent")
    assert "name" in card
    assert "capabilities" in card
    assert "endpoint" in card
    assert "protocols" in card
```

**Integration Tests**:
- **Cross-framework communication**: Test agents from different frameworks communicating via A2A
- **Async task handling**: Submit long-running tasks and verify status tracking
- **Streaming responses**: Verify SSE stream integrity and event ordering
- **Error handling**: Test timeout, retry, and failure scenarios

**Performance Benchmarks**:
```bash
# Measure A2A communication latency
ab -n 1000 -c 10 -p request.json -T application/json \
  https://api.example.com/agents/research

# Measure discovery latency
python benchmark_discovery.py --iterations 100
```

### Success Criteria

- [ ] Agents from at least 2 different frameworks successfully communicate
- [ ] Agent discovery completes in <500ms
- [ ] Long-running tasks (>30s) complete with status tracking
- [ ] Streaming responses deliver events in real-time with <1s latency
- [ ] Error rate <5% across all agent interactions
- [ ] Agent Cards remain accurate and up-to-date

**Sources**: [R1]

---

## Update Log

### 2025-10-23
- **Initial version**: Comprehensive A2A protocol documentation
- **Added**: Canonical definitions for A2A, Agent Cards, and Opaque Execution
- **Added**: Core patterns for JSON-RPC communication, agent discovery, async delegation, and streaming
- **Added**: Decision checklist, anti-patterns, and evaluation criteria
- **Sources**: [R1][R2][R3][R4]

---

## See Also

### Prerequisites
- [AGENTS.md](../../topics/AGENTS.md): Operational documentation for AI agents
- [SSOT.md](../../topics/SSOT.md): Single Source of Truth principles for canonical definitions

### Related Topics
- [Orchestration patterns](../../topics/AGENTS.md#orchestration): Multi-agent coordination strategies
- [Context engineering](../../engineering/context.md): Managing context in agent interactions

### Related Protocols
- **Model Context Protocol (MCP)**: For agent-to-tool communication (complementary to A2A)
- **JSON-RPC 2.0**: Underlying RPC standard used by A2A
- **Server-Sent Events (SSE)**: Streaming protocol for real-time updates

### Platform-Specific
- OpenAI Agents SDK: Implementing A2A with OpenAI agents (see [platforms/openai/agents-sdk.md](../openai/agents-sdk.md))
- Google AI Agents: Native A2A support in Google's agent frameworks

---

## References

- [R1] A2A Project. "Agent2Agent Protocol Specification." A2A Protocol Documentation. https://a2a-protocol.org/dev/specification/ (accessed 2025-10-23)
- [R2] A2A Project. "A2A GitHub Repository." GitHub. https://github.com/a2aproject/A2A (accessed 2025-10-23)
- [R3] The Linux Foundation. "Linux Foundation Launches the Agent2Agent Protocol Project to Enable Secure, Intelligent Communication Between AI Agents." Press Release. https://www.linuxfoundation.org/press/linux-foundation-launches-the-agent2agent-protocol-project-to-enable-secure-intelligent-communication-between-ai-agents (accessed 2025-10-23)
- [R4] Google Developers. "A2A: A New Era of Agent Interoperability." Google Developers Blog. https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/ (accessed 2025-10-23)

---

**Document ID**: `platforms/google/a2a`
**Canonical URL**: `https://github.com/artificial-intelligence-first/ssot/blob/main/platforms/google/a2a.md`
**License**: MIT
