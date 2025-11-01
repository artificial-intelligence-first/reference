---
title: A2A Protocol
slug: a2a-protocol
status: living
last_updated: 2025-11-01
tags: [agents, orchestration, apis, google, conventions]
summary: "Open protocol for secure, intelligent communication between AI agents across different frameworks."
authors: []
sources:
  - { id: R1, title: "A2A Protocol Specification", url: "https://a2a-protocol.org/dev/specification/", accessed: "2025-10-23" }
  - { id: R2, title: "A2A GitHub Repository", url: "https://github.com/a2aproject/A2A", accessed: "2025-10-23" }
  - { id: R3, title: "Linux Foundation A2A Announcement", url: "https://www.linuxfoundation.org/press/linux-foundation-launches-the-agent2agent-protocol-project-to-enable-secure-intelligent-communication-between-ai-agents", accessed: "2025-10-23" }
  - { id: R4, title: "Google Developers Blog: A2A Protocol", url: "https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/", accessed: "2025-10-23" }
---

# A2A Protocol

> **For Humans**: This document explains the Agent2Agent (A2A) protocol, an open standard for AI agent communication and interoperability. Use this when building multi-agent systems, enabling agent collaboration across different frameworks, or designing agent-to-agent communication patterns.
>
> **For AI Agents**: Apply A2A protocol patterns for inter-agent communication. Use these specifications when implementing agent discovery, message routing, capability negotiation, and secure agent-to-agent interactions.

## Overview

The Agent2Agent (A2A) protocol is an open standard developed under the Linux Foundation that enables secure, intelligent communication between AI agents from different frameworks and providers. It provides a common language for agents to discover, negotiate capabilities, and collaborate on complex tasks.

## Core Components

### Protocol Stack

```
┌─────────────────────────────┐
│   Application Layer         │ ← Agent logic
├─────────────────────────────┤
│   Capability Layer          │ ← Service discovery
├─────────────────────────────┤
│   Message Layer              │ ← A2A messages
├─────────────────────────────┤
│   Security Layer             │ ← Authentication
├─────────────────────────────┤
│   Transport Layer            │ ← HTTP/WebSocket
└─────────────────────────────┘
```

### Message Format

```json
{
  "version": "1.0",
  "id": "msg_123456",
  "type": "request",
  "from": "agent://provider/agent-id",
  "to": "agent://provider/target-id",
  "timestamp": "2025-11-01T10:00:00Z",
  "headers": {
    "content-type": "application/json",
    "authorization": "Bearer token"
  },
  "body": {
    "action": "collaborate",
    "params": {
      "task": "data_analysis",
      "context": {}
    }
  },
  "signature": "..."
}
```

## Agent Discovery

### Registry Service

```python
from a2a import Registry, Agent

class A2ARegistry:
    """Agent discovery and registration."""

    def __init__(self, registry_url: str):
        self.registry = Registry(registry_url)

    async def register_agent(self, agent: Agent):
        """Register agent with the network."""
        manifest = {
            "id": agent.id,
            "name": agent.name,
            "capabilities": agent.capabilities,
            "endpoints": {
                "primary": agent.endpoint,
                "websocket": agent.ws_endpoint
            },
            "metadata": {
                "version": agent.version,
                "provider": agent.provider
            }
        }

        return await self.registry.register(manifest)

    async def discover_agents(self, capability: str):
        """Find agents with specific capabilities."""
        return await self.registry.search(
            filters={"capabilities": capability}
        )
```

### Capability Advertisement

```yaml
# agent-manifest.yaml
agent:
  id: "data-analyst-001"
  name: "Data Analysis Agent"
  version: "1.0.0"

capabilities:
  - name: "data_processing"
    description: "Process and analyze datasets"
    inputs:
      - type: "csv"
      - type: "json"
      - type: "parquet"
    outputs:
      - type: "report"
      - type: "visualization"

  - name: "statistical_analysis"
    description: "Perform statistical computations"
    methods:
      - "regression"
      - "clustering"
      - "classification"
```

## Communication Patterns

### Request-Response

```python
class RequestResponseAgent:
    """Basic request-response pattern."""

    async def send_request(self, target: str, request: dict):
        """Send request to another agent."""
        message = self.create_message(
            type="request",
            to=target,
            body=request
        )

        response = await self.transport.send(message)
        return self.parse_response(response)

    async def handle_request(self, message: dict):
        """Process incoming request."""
        result = await self.process(message["body"])

        return self.create_message(
            type="response",
            to=message["from"],
            body=result,
            correlation_id=message["id"]
        )
```

### Publish-Subscribe

```python
class PubSubAgent:
    """Publish-subscribe pattern."""

    async def publish(self, topic: str, data: dict):
        """Publish to a topic."""
        message = self.create_message(
            type="publish",
            topic=topic,
            body=data
        )

        await self.broker.publish(message)

    async def subscribe(self, topic: str, handler):
        """Subscribe to a topic."""
        await self.broker.subscribe(
            topic=topic,
            callback=handler
        )
```

### Streaming

```python
class StreamingAgent:
    """Streaming communication pattern."""

    async def stream_to(self, target: str, data_source):
        """Stream data to another agent."""
        async with self.create_stream(target) as stream:
            async for chunk in data_source:
                await stream.send(chunk)

    async def receive_stream(self, stream_id: str):
        """Receive streaming data."""
        async with self.accept_stream(stream_id) as stream:
            async for chunk in stream:
                await self.process_chunk(chunk)
```

## Security

### Authentication

```python
from a2a.security import JWTAuth, OAuth2

class SecureAgent:
    """Agent with authentication."""

    def __init__(self, credentials):
        self.auth = JWTAuth(
            issuer=credentials["issuer"],
            private_key=credentials["private_key"]
        )

    def create_signed_message(self, message: dict):
        """Create cryptographically signed message."""
        token = self.auth.create_token(
            subject=self.agent_id,
            claims={"capabilities": self.capabilities}
        )

        message["headers"]["authorization"] = f"Bearer {token}"
        message["signature"] = self.auth.sign(message)

        return message

    def verify_message(self, message: dict):
        """Verify message authenticity."""
        token = message["headers"]["authorization"].split()[1]

        if not self.auth.verify_token(token):
            raise SecurityError("Invalid token")

        if not self.auth.verify_signature(message):
            raise SecurityError("Invalid signature")

        return True
```

### Authorization

```python
class AuthorizationPolicy:
    """Define authorization policies."""

    def __init__(self):
        self.policies = {}

    def add_policy(self, resource: str, rules: dict):
        """Add authorization policy."""
        self.policies[resource] = rules

    def check_permission(self, agent_id: str, action: str, resource: str):
        """Check if agent has permission."""
        policy = self.policies.get(resource)

        if not policy:
            return False

        agent_role = self.get_agent_role(agent_id)
        allowed_actions = policy.get(agent_role, [])

        return action in allowed_actions
```

## Orchestration

### Workflow Coordination

```python
class WorkflowOrchestrator:
    """Coordinate multi-agent workflows."""

    async def execute_workflow(self, workflow_def: dict):
        """Execute defined workflow."""
        workflow = self.parse_workflow(workflow_def)
        context = {}

        for step in workflow.steps:
            agent = await self.discover_agent(step.capability)

            result = await self.execute_step(
                agent=agent,
                action=step.action,
                inputs=self.resolve_inputs(step.inputs, context)
            )

            context[step.name] = result

            if step.condition and not self.evaluate(step.condition, context):
                break

        return context
```

### Task Distribution

```python
class TaskDistributor:
    """Distribute tasks across agents."""

    async def distribute_task(self, task: dict):
        """Distribute task to capable agents."""
        # Find capable agents
        agents = await self.registry.discover(
            capability=task["required_capability"]
        )

        # Select optimal agent
        selected = self.select_agent(
            agents,
            criteria={
                "load": "minimal",
                "latency": "lowest",
                "cost": "optimal"
            }
        )

        # Assign task
        return await selected.execute(task)
```

## Implementation Examples

### Python Implementation

```python
from a2a import Agent, Protocol

class DataAnalysisAgent(Agent):
    """Example A2A-compliant agent."""

    def __init__(self):
        super().__init__(
            id="data-analyst-001",
            name="Data Analysis Agent",
            capabilities=["data_processing", "visualization"]
        )

        self.protocol = Protocol(version="1.0")

    async def start(self):
        """Start agent and register."""
        await self.register()
        await self.listen()

    async def handle_message(self, message: dict):
        """Process incoming A2A messages."""
        if message["type"] == "request":
            return await self.handle_request(message)
        elif message["type"] == "stream":
            return await self.handle_stream(message)

    async def collaborate(self, partner_id: str, task: dict):
        """Collaborate with another agent."""
        partner = await self.discover(partner_id)

        # Negotiate capabilities
        capabilities = await self.negotiate_capabilities(partner)

        # Execute collaborative task
        result = await self.execute_collaborative(
            partner=partner,
            task=task,
            shared_capabilities=capabilities
        )

        return result
```

### TypeScript Implementation

```typescript
import { Agent, Protocol, Message } from '@a2a/sdk';

class DataAnalysisAgent extends Agent {
  constructor() {
    super({
      id: 'data-analyst-001',
      name: 'Data Analysis Agent',
      capabilities: ['data_processing', 'visualization']
    });
  }

  async handleMessage(message: Message): Promise<Message> {
    switch (message.type) {
      case 'request':
        return this.handleRequest(message);
      case 'stream':
        return this.handleStream(message);
      default:
        throw new Error(`Unknown message type: ${message.type}`);
    }
  }

  async collaborate(partnerId: string, task: Task): Promise<Result> {
    const partner = await this.discover(partnerId);
    const capabilities = await this.negotiateCapabilities(partner);

    return this.executeCollaborative({
      partner,
      task,
      sharedCapabilities: capabilities
    });
  }
}
```

## Integration with Platforms

### Google Vertex AI

```python
from google.cloud import aiplatform
from a2a import VertexAIAdapter

class VertexA2AAgent:
    """A2A agent integrated with Vertex AI."""

    def __init__(self, project: str, location: str):
        aiplatform.init(project=project, location=location)
        self.adapter = VertexAIAdapter()

    async def deploy(self):
        """Deploy as Vertex AI endpoint."""
        endpoint = aiplatform.Endpoint.create(
            display_name="a2a-agent",
            description="A2A-compliant agent"
        )

        # Deploy model
        model = aiplatform.Model.upload(
            display_name="a2a-agent-model",
            serving_container_image_uri="gcr.io/project/a2a-agent"
        )

        endpoint.deploy(model)

        # Register with A2A network
        await self.adapter.register_endpoint(endpoint)
```

### Integration with MCP

```python
class A2AMCPBridge:
    """Bridge between A2A and MCP protocols."""

    def __init__(self):
        self.a2a_agent = A2AAgent()
        self.mcp_server = MCPServer()

    async def translate_a2a_to_mcp(self, a2a_message: dict):
        """Translate A2A message to MCP format."""
        return {
            "jsonrpc": "2.0",
            "method": a2a_message["body"]["action"],
            "params": a2a_message["body"]["params"],
            "id": a2a_message["id"]
        }

    async def translate_mcp_to_a2a(self, mcp_message: dict):
        """Translate MCP message to A2A format."""
        return {
            "version": "1.0",
            "type": "request",
            "body": {
                "action": mcp_message["method"],
                "params": mcp_message["params"]
            }
        }
```

## Testing

### Protocol Compliance

```python
import pytest
from a2a.testing import ProtocolTester

class TestA2ACompliance:
    @pytest.fixture
    def agent(self):
        return DataAnalysisAgent()

    def test_message_format(self, agent):
        """Test message format compliance."""
        message = agent.create_message(
            type="request",
            body={"action": "test"}
        )

        tester = ProtocolTester()
        assert tester.validate_message(message)

    async def test_discovery(self, agent):
        """Test agent discovery."""
        await agent.register()

        discovered = await agent.registry.discover(
            capability="data_processing"
        )

        assert agent.id in [a.id for a in discovered]
```

## Best Practices

### Message Design

1. **Keep messages small** - Under 1MB per message
2. **Use correlation IDs** - Track request-response pairs
3. **Include versioning** - Support protocol evolution
4. **Add timestamps** - Enable message ordering
5. **Sign messages** - Ensure authenticity

### Error Handling

```python
class RobustA2AAgent:
    """Agent with comprehensive error handling."""

    async def send_with_retry(self, message: dict, max_retries: int = 3):
        """Send message with retry logic."""
        for attempt in range(max_retries):
            try:
                return await self.send(message)
            except NetworkError as e:
                if attempt == max_retries - 1:
                    raise
                await self.exponential_backoff(attempt)
            except ProtocolError as e:
                # Don't retry protocol errors
                raise
```

### Performance

1. **Connection pooling** - Reuse connections
2. **Message batching** - Group small messages
3. **Async processing** - Non-blocking operations
4. **Caching** - Cache discovery results
5. **Circuit breaking** - Fail fast on errors

## See Also

- [ADK](./adk.md) - Agent Development Kit
- [MCP](../../tools/mcp.md) - Model Context Protocol
- [AGENTS.md](../../../AGENTS.md) - Agent operations

## References

- [R1] A2A Protocol Specification. https://a2a-protocol.org/dev/specification/ (accessed 2025-10-23)
- [R2] A2A GitHub Repository. https://github.com/a2aproject/A2A (accessed 2025-10-23)
- [R3] Linux Foundation A2A Announcement. https://www.linuxfoundation.org/press/linux-foundation-launches-the-agent2agent-protocol-project-to-enable-secure-intelligent-communication-between-ai-agents (accessed 2025-10-23)
- [R4] Google Developers Blog: A2A Protocol. https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/ (accessed 2025-10-23)
