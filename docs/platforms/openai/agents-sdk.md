---
title: OpenAI Agents SDK
slug: openai-agents-sdk
status: living
last_updated: 2025-11-01
tags: [openai, agents, sdk, responses-api, tool-calling, mcp]
summary: "Hands-on patterns for building agents with OpenAI SDKs using Responses API and tool calling."
authors: []
sources:
  - { id: R1, title: "OpenAI Responses API Guide", url: "https://platform.openai.com/docs/guides/responses", accessed: "2025-10-24" }
  - { id: R2, title: "OpenAI Function Calling Guide", url: "https://platform.openai.com/docs/guides/function-calling", accessed: "2025-10-24" }
  - { id: R3, title: "openai-python - Official Python SDK", url: "https://github.com/openai/openai-python", accessed: "2025-10-24" }
  - { id: R4, title: "openai-node - Official Node.js SDK", url: "https://github.com/openai/openai-node", accessed: "2025-10-24" }
  - { id: R5, title: "OpenAI Streaming Guide", url: "https://platform.openai.com/docs/guides/streaming", accessed: "2025-10-24" }
  - { id: R6, title: "New tools and features in the Responses API", url: "https://openai.com/index/new-tools-and-features-in-the-responses-api/", accessed: "2025-10-24" }
---

# OpenAI Agents SDK

> **For Humans**: This guide documents hands-on patterns for building agents with the official OpenAI SDKs (Python & TypeScript) using the Responses API and tool calling. Examples favour clarity and production readiness—adapt them to your stack.
>
> **For AI Agents**: Apply these patterns when implementing OpenAI agent capabilities. Use the Responses API for structured outputs and tool calling for external integrations.

## Overview

The OpenAI Agents SDK provides tools for building autonomous agents using GPT models with the Responses API. It supports structured outputs, function calling, streaming responses, and integration with external tools through a standardized interface.

## Installation

### Python SDK

```bash
# Install latest
pip install openai

# Install with extras
pip install openai[datalib]  # For data utilities
pip install openai[wandb]    # For Weights & Biases integration

# Verify installation
python -c "import openai; print(openai.__version__)"
```

### Node.js/TypeScript SDK

```bash
# npm installation
npm install openai

# Yarn installation
yarn add openai

# TypeScript types included
npm install --save-dev @types/node

# Verify installation
node -e "console.log(require('openai').VERSION)"
```

## Basic Agent Setup

### Python Implementation

```python
from openai import OpenAI
import json
from typing import Dict, Any, List

class Agent:
    """Basic OpenAI agent with tool calling."""

    def __init__(self, api_key: str = None):
        self.client = OpenAI(api_key=api_key)
        self.conversation = []
        self.tools = []

    def add_tool(self, tool: Dict[str, Any]):
        """Register a tool for the agent."""
        self.tools.append(tool)

    async def run(self, prompt: str) -> str:
        """Execute agent with user prompt."""
        self.conversation.append({"role": "user", "content": prompt})

        response = self.client.chat.completions.create(
            model="gpt-4-turbo-preview",
            messages=self.conversation,
            tools=self.tools,
            tool_choice="auto"
        )

        # Handle tool calls if present
        if response.choices[0].message.tool_calls:
            await self.handle_tool_calls(response.choices[0].message)

        return response.choices[0].message.content
```

### TypeScript Implementation

```typescript
import OpenAI from 'openai';

interface Tool {
  type: 'function';
  function: {
    name: string;
    description: string;
    parameters: object;
  };
}

class Agent {
  private client: OpenAI;
  private conversation: OpenAI.ChatCompletionMessageParam[] = [];
  private tools: Tool[] = [];

  constructor(apiKey?: string) {
    this.client = new OpenAI({ apiKey });
  }

  addTool(tool: Tool): void {
    this.tools.push(tool);
  }

  async run(prompt: string): Promise<string> {
    this.conversation.push({ role: 'user', content: prompt });

    const response = await this.client.chat.completions.create({
      model: 'gpt-4-turbo-preview',
      messages: this.conversation,
      tools: this.tools,
      tool_choice: 'auto'
    });

    const message = response.choices[0].message;

    if (message.tool_calls) {
      await this.handleToolCalls(message);
    }

    return message.content || '';
  }
}
```

## Tool Calling

### Defining Tools

```python
# Tool definition schema
calculator_tool = {
    "type": "function",
    "function": {
        "name": "calculate",
        "description": "Perform mathematical calculations",
        "parameters": {
            "type": "object",
            "properties": {
                "expression": {
                    "type": "string",
                    "description": "Mathematical expression to evaluate"
                }
            },
            "required": ["expression"]
        }
    }
}

# Database query tool
database_tool = {
    "type": "function",
    "function": {
        "name": "query_database",
        "description": "Execute SQL queries on the database",
        "parameters": {
            "type": "object",
            "properties": {
                "query": {
                    "type": "string",
                    "description": "SQL query to execute"
                },
                "database": {
                    "type": "string",
                    "description": "Target database name",
                    "enum": ["users", "products", "orders"]
                }
            },
            "required": ["query", "database"]
        }
    }
}
```

### Implementing Tool Handlers

```python
class ToolHandler:
    """Handle tool execution for the agent."""

    async def execute(self, tool_call):
        """Route tool calls to appropriate handlers."""
        function_name = tool_call.function.name
        arguments = json.loads(tool_call.function.arguments)

        if function_name == "calculate":
            return await self.calculate(arguments)
        elif function_name == "query_database":
            return await self.query_database(arguments)
        else:
            return {"error": f"Unknown tool: {function_name}"}

    async def calculate(self, args):
        """Execute mathematical calculations."""
        try:
            # Use safe evaluation in production
            result = eval(args["expression"])
            return {"result": result}
        except Exception as e:
            return {"error": str(e)}

    async def query_database(self, args):
        """Execute database queries."""
        # Implement secure database access
        query = args["query"]
        database = args["database"]

        # Validate and execute query
        # Return results
        return {"data": "Query results here"}
```

## Responses API

### Structured Output

```python
from pydantic import BaseModel
from typing import List, Optional

class TaskOutput(BaseModel):
    """Structured response format."""
    task_id: str
    status: str
    steps: List[str]
    result: Optional[Dict[str, Any]]
    confidence: float

# Using structured output
response = client.chat.completions.create(
    model="gpt-4-turbo-preview",
    messages=[
        {"role": "system", "content": "Generate structured task output"},
        {"role": "user", "content": "Plan a code review process"}
    ],
    response_format={"type": "json_object"},
    functions=[{
        "name": "output_task",
        "parameters": TaskOutput.schema()
    }]
)
```

### Streaming Responses

```python
async def stream_response(prompt: str):
    """Stream responses for real-time output."""
    stream = client.chat.completions.create(
        model="gpt-4-turbo-preview",
        messages=[{"role": "user", "content": prompt}],
        stream=True
    )

    collected_messages = []
    for chunk in stream:
        chunk_message = chunk.choices[0].delta.content or ""
        collected_messages.append(chunk_message)
        print(chunk_message, end="")

    return "".join(collected_messages)
```

## Advanced Patterns

### Multi-Agent Orchestration

```python
class OrchestratorAgent:
    """Coordinate multiple specialized agents."""

    def __init__(self):
        self.agents = {
            "researcher": ResearchAgent(),
            "coder": CodingAgent(),
            "reviewer": ReviewAgent()
        }

    async def execute_workflow(self, task: str):
        """Execute multi-agent workflow."""
        # Research phase
        research = await self.agents["researcher"].run(
            f"Research requirements for: {task}"
        )

        # Coding phase
        code = await self.agents["coder"].run(
            f"Implement based on research: {research}"
        )

        # Review phase
        review = await self.agents["reviewer"].run(
            f"Review this implementation: {code}"
        )

        return {
            "research": research,
            "implementation": code,
            "review": review
        }
```

### Context Management

```python
class ContextManager:
    """Manage conversation context efficiently."""

    def __init__(self, max_tokens: int = 8000):
        self.max_tokens = max_tokens
        self.messages = []

    def add_message(self, message: dict):
        """Add message with context pruning."""
        self.messages.append(message)
        self.prune_context()

    def prune_context(self):
        """Remove old messages to stay within token limit."""
        total_tokens = self.estimate_tokens()

        while total_tokens > self.max_tokens and len(self.messages) > 2:
            # Keep system message and recent messages
            if self.messages[1]["role"] != "system":
                self.messages.pop(1)
            else:
                self.messages.pop(2)
            total_tokens = self.estimate_tokens()

    def estimate_tokens(self):
        """Estimate token count for messages."""
        # Rough estimation: 1 token ~= 4 characters
        char_count = sum(len(str(msg)) for msg in self.messages)
        return char_count // 4
```

### Error Handling

```python
from tenacity import retry, stop_after_attempt, wait_exponential

class RobustAgent:
    """Agent with comprehensive error handling."""

    @retry(
        stop=stop_after_attempt(3),
        wait=wait_exponential(multiplier=1, min=4, max=10)
    )
    async def call_api(self, messages: List[dict]):
        """Make API call with retry logic."""
        try:
            response = self.client.chat.completions.create(
                model="gpt-4-turbo-preview",
                messages=messages,
                timeout=30
            )
            return response
        except openai.RateLimitError as e:
            print(f"Rate limit hit: {e}")
            raise
        except openai.APIError as e:
            print(f"API error: {e}")
            raise
        except Exception as e:
            print(f"Unexpected error: {e}")
            raise
```

## MCP Integration

### MCP Server Connection

```python
class MCPIntegratedAgent:
    """Agent with MCP server integration."""

    def __init__(self):
        self.openai_client = OpenAI()
        self.mcp_servers = {}

    async def connect_mcp_server(self, name: str, url: str):
        """Connect to MCP server."""
        # Establish WebSocket connection
        import websockets
        self.mcp_servers[name] = await websockets.connect(url)

    async def call_mcp_tool(self, server: str, tool: str, params: dict):
        """Execute tool via MCP server."""
        request = {
            "jsonrpc": "2.0",
            "method": "tools/call",
            "params": {
                "name": tool,
                "arguments": params
            },
            "id": 1
        }

        ws = self.mcp_servers[server]
        await ws.send(json.dumps(request))
        response = await ws.recv()
        return json.loads(response)
```

## Testing

### Unit Testing

```python
import pytest
from unittest.mock import Mock, patch

class TestAgent:
    @pytest.fixture
    def agent(self):
        return Agent(api_key="test-key")

    @patch('openai.OpenAI')
    def test_basic_response(self, mock_openai, agent):
        # Mock API response
        mock_response = Mock()
        mock_response.choices = [Mock(message=Mock(content="Test response"))]
        mock_openai.return_value.chat.completions.create.return_value = mock_response

        result = agent.run("Test prompt")
        assert result == "Test response"

    @pytest.mark.asyncio
    async def test_tool_calling(self, agent):
        # Add test tool
        agent.add_tool(calculator_tool)

        # Mock tool execution
        with patch.object(agent, 'handle_tool_calls') as mock_handler:
            await agent.run("Calculate 2 + 2")
            assert mock_handler.called
```

### Integration Testing

```python
@pytest.mark.integration
class TestIntegration:
    async def test_full_workflow(self):
        """Test complete agent workflow."""
        agent = Agent()
        agent.add_tool(calculator_tool)
        agent.add_tool(database_tool)

        # Execute complex task
        result = await agent.run(
            "Calculate the average order value from the database"
        )

        assert "average" in result.lower()
        assert agent.conversation[-1]["role"] == "assistant"
```

## Performance Optimization

### Batching Requests

```python
async def batch_process(prompts: List[str], batch_size: int = 5):
    """Process multiple prompts in batches."""
    results = []

    for i in range(0, len(prompts), batch_size):
        batch = prompts[i:i + batch_size]

        # Process batch concurrently
        tasks = [process_single(prompt) for prompt in batch]
        batch_results = await asyncio.gather(*tasks)
        results.extend(batch_results)

    return results
```

### Caching Responses

```python
from functools import lru_cache
import hashlib

class CachedAgent:
    """Agent with response caching."""

    def __init__(self):
        self.cache = {}

    def get_cache_key(self, messages: List[dict]):
        """Generate cache key from messages."""
        content = json.dumps(messages, sort_keys=True)
        return hashlib.md5(content.encode()).hexdigest()

    async def run(self, prompt: str):
        """Run with caching."""
        cache_key = self.get_cache_key(self.conversation + [{"role": "user", "content": prompt}])

        if cache_key in self.cache:
            return self.cache[cache_key]

        response = await self.call_api(prompt)
        self.cache[cache_key] = response
        return response
```

## Deployment

### Docker Container

```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy application
COPY . .

# Environment variables
ENV OPENAI_API_KEY=${OPENAI_API_KEY}

# Run agent
CMD ["python", "agent.py"]
```

### Serverless Function

```python
# AWS Lambda handler
import json

def lambda_handler(event, context):
    """AWS Lambda handler for agent."""
    agent = Agent()

    # Process request
    prompt = event.get('prompt')
    result = agent.run(prompt)

    return {
        'statusCode': 200,
        'body': json.dumps({
            'result': result
        })
    }
```

## Monitoring

### Logging

```python
import logging
from datetime import datetime

class MonitoredAgent:
    """Agent with comprehensive logging."""

    def __init__(self):
        self.logger = logging.getLogger(__name__)
        self.metrics = {
            "requests": 0,
            "errors": 0,
            "total_tokens": 0
        }

    async def run(self, prompt: str):
        """Run with monitoring."""
        start_time = datetime.now()
        self.metrics["requests"] += 1

        try:
            response = await self.call_api(prompt)

            # Log metrics
            duration = (datetime.now() - start_time).total_seconds()
            self.logger.info(f"Request completed in {duration}s")

            # Track token usage
            if hasattr(response, 'usage'):
                self.metrics["total_tokens"] += response.usage.total_tokens

            return response
        except Exception as e:
            self.metrics["errors"] += 1
            self.logger.error(f"Error: {e}")
            raise
```

## Best Practices

### API Key Management

```python
import os
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

# Secure API key handling
class SecureAgent:
    def __init__(self):
        api_key = os.getenv("OPENAI_API_KEY")
        if not api_key:
            raise ValueError("OPENAI_API_KEY not found in environment")

        self.client = OpenAI(
            api_key=api_key,
            organization=os.getenv("OPENAI_ORG_ID")
        )
```

### Rate Limiting

```python
import time
from collections import deque

class RateLimitedAgent:
    """Agent with rate limiting."""

    def __init__(self, requests_per_minute: int = 60):
        self.rpm_limit = requests_per_minute
        self.request_times = deque()

    async def check_rate_limit(self):
        """Check and enforce rate limits."""
        now = time.time()

        # Remove requests older than 1 minute
        while self.request_times and self.request_times[0] < now - 60:
            self.request_times.popleft()

        # Check if limit reached
        if len(self.request_times) >= self.rpm_limit:
            sleep_time = 60 - (now - self.request_times[0])
            if sleep_time > 0:
                await asyncio.sleep(sleep_time)

        self.request_times.append(now)
```

## See Also

- [Agent Kit](./agent-kit.md) - OpenAI Agent Kit framework
- [Codex](./codex.md) - Code generation patterns
- [AGENTS.md](../../../AGENTS.md) - Operational documentation

## References

- [R1] OpenAI Responses API Guide. https://platform.openai.com/docs/guides/responses (accessed 2025-10-24)
- [R2] OpenAI Function Calling Guide. https://platform.openai.com/docs/guides/function-calling (accessed 2025-10-24)
- [R3] openai-python - Official Python SDK. https://github.com/openai/openai-python (accessed 2025-10-24)
- [R4] openai-node - Official Node.js SDK. https://github.com/openai/openai-node (accessed 2025-10-24)
- [R5] OpenAI Streaming Guide. https://platform.openai.com/docs/guides/streaming (accessed 2025-10-24)
- [R6] New tools and features in the Responses API. https://openai.com/index/new-tools-and-features-in-the-responses-api/ (accessed 2025-10-24)