---
title: MCP
slug: mcp
status: living
last_updated: 2025-11-01
tags: [mcp, protocol, integration, anthropic]
summary: "Model Context Protocol for seamless LLM integration with external data sources and tools."
authors: []
sources:
  - { id: R1, title: "Model Context Protocol Documentation", url: "https://modelcontextprotocol.io/", accessed: "2025-10-23" }
  - { id: R2, title: "MCP GitHub Repository", url: "https://github.com/modelcontextprotocol/servers", accessed: "2025-10-23" }
---

# MCP

> **For Humans**: Model Context Protocol (MCP) provides a standard way for LLMs to interact with external data sources and tools through a unified interface.
>
> **For AI Agents**: MCP enables you to access external systems through standardized server connections. Use MCP servers to read databases, call APIs, and execute tools beyond your native capabilities.

## Overview

Model Context Protocol (MCP) is an open protocol that standardizes how LLMs connect with external data sources and tools. It provides a consistent interface for context retrieval, tool execution, and system integration.

## Architecture

### Core Components

```
┌─────────────┐     MCP      ┌─────────────┐
│   LLM/AI    │◄────────────►│  MCP Server │
│  Assistant  │   Protocol   │   (Tools)   │
└─────────────┘              └─────────────┘
       ▲                            ▲
       │                            │
       └────────────────────────────┘
              User Interface
```

### Protocol Layers

1. **Transport Layer**: WebSocket, HTTP, IPC
2. **Message Layer**: JSON-RPC 2.0
3. **Capability Layer**: Resources, tools, prompts
4. **Security Layer**: Authentication, authorization

## Server Implementation

### Basic Server Structure

```python
from mcp import Server, Resource, Tool

class MyMCPServer(Server):
    """Custom MCP server implementation."""

    async def initialize(self):
        """Initialize server resources."""
        self.register_resource(Resource(
            uri="db://users",
            name="User Database",
            description="Access user information"
        ))

        self.register_tool(Tool(
            name="query_database",
            description="Execute SQL queries",
            parameters={
                "type": "object",
                "properties": {
                    "query": {"type": "string"}
                }
            }
        ))

    async def handle_resource(self, uri: str):
        """Handle resource requests."""
        if uri == "db://users":
            return await self.fetch_users()

    async def handle_tool(self, name: str, params: dict):
        """Handle tool executions."""
        if name == "query_database":
            return await self.execute_query(params["query"])
```

### Configuration

```json
{
  "mcpServers": {
    "database": {
      "command": "python",
      "args": ["mcp_database_server.py"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    },
    "filesystem": {
      "command": "mcp-server-filesystem",
      "args": ["--root", "/data"]
    }
  }
}
```

## Client Integration

### Connecting to Servers

```python
from mcp import Client

async def connect_mcp():
    client = Client()

    # Connect to server
    await client.connect("ws://localhost:8765")

    # List available resources
    resources = await client.list_resources()

    # Fetch resource content
    content = await client.get_resource("db://users")

    # Execute tool
    result = await client.call_tool(
        "query_database",
        {"query": "SELECT * FROM users LIMIT 10"}
    )

    return result
```

### Claude Desktop Integration

```json
// Claude Desktop config
{
  "mcp": {
    "servers": {
      "github": {
        "command": "npx",
        "args": ["@modelcontextprotocol/server-github"],
        "env": {
          "GITHUB_TOKEN": "${GITHUB_TOKEN}"
        }
      }
    }
  }
}
```

## Resource Types

### File System Resources

```python
Resource(
    uri="file:///path/to/document.md",
    name="Document",
    mimeType="text/markdown",
    description="Markdown document"
)
```

### Database Resources

```python
Resource(
    uri="db://schema/table",
    name="Database Table",
    mimeType="application/sql",
    description="SQL table access"
)
```

### API Resources

```python
Resource(
    uri="api://service/endpoint",
    name="API Endpoint",
    mimeType="application/json",
    description="REST API endpoint"
)
```

## Tool Definitions

### Tool Schema

```json
{
  "name": "send_email",
  "description": "Send an email message",
  "inputSchema": {
    "type": "object",
    "properties": {
      "to": {
        "type": "string",
        "format": "email"
      },
      "subject": {
        "type": "string"
      },
      "body": {
        "type": "string"
      }
    },
    "required": ["to", "subject", "body"]
  }
}
```

### Tool Implementation

```python
async def handle_send_email(params):
    """Handle email sending tool."""
    email_client = EmailClient()

    try:
        result = await email_client.send(
            to=params["to"],
            subject=params["subject"],
            body=params["body"]
        )
        return {"success": True, "messageId": result.id}
    except Exception as e:
        return {"success": False, "error": str(e)}
```

## Security

### Authentication

```python
class SecureMCPServer(Server):
    async def authenticate(self, token: str):
        """Validate authentication token."""
        if not self.validate_token(token):
            raise AuthenticationError("Invalid token")

    async def authorize(self, action: str, resource: str):
        """Check authorization for action."""
        if not self.check_permission(action, resource):
            raise AuthorizationError("Permission denied")
```

### Rate Limiting

```python
from functools import wraps
import time

def rate_limit(calls_per_minute=60):
    def decorator(func):
        last_called = {}

        @wraps(func)
        async def wrapper(self, *args, **kwargs):
            now = time.time()
            key = f"{self.__class__.__name__}.{func.__name__}"

            if key in last_called:
                elapsed = now - last_called[key]
                if elapsed < 60 / calls_per_minute:
                    await asyncio.sleep(60 / calls_per_minute - elapsed)

            last_called[key] = time.time()
            return await func(self, *args, **kwargs)

        return wrapper
    return decorator
```

## Common Servers

### Official Servers

| Server | Description | Installation |
|--------|-------------|--------------|
| filesystem | File system access | `npm install @modelcontextprotocol/server-filesystem` |
| github | GitHub repository access | `npm install @modelcontextprotocol/server-github` |
| postgres | PostgreSQL database | `npm install @modelcontextprotocol/server-postgres` |
| sqlite | SQLite database | `npm install @modelcontextprotocol/server-sqlite` |

### Community Servers

```bash
# Search servers
mcp search database

# Install server
mcp install mcp-server-mongodb

# List installed
mcp list
```

## Best Practices

### Server Design

1. **Single Responsibility**: One domain per server
2. **Stateless Operations**: Don't maintain session state
3. **Error Handling**: Return clear error messages
4. **Timeout Management**: Set reasonable timeouts
5. **Resource Efficiency**: Stream large responses

### Client Usage

1. **Connection Pooling**: Reuse connections
2. **Retry Logic**: Handle transient failures
3. **Caching**: Cache resource metadata
4. **Batch Operations**: Group related requests
5. **Progress Reporting**: For long operations

## Debugging

### Enable Debug Logging

```python
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

# MCP specific logger
mcp_logger = logging.getLogger('mcp')
mcp_logger.setLevel(logging.DEBUG)
```

### Connection Testing

```bash
# Test server connection
mcp test ws://localhost:8765

# Validate server manifest
mcp validate ./mcp-server.json

# Monitor messages
mcp monitor --server database
```

## Examples

### Weather Server

```python
class WeatherMCPServer(Server):
    """MCP server for weather data."""

    async def initialize(self):
        self.register_tool(Tool(
            name="get_weather",
            description="Get current weather",
            parameters={
                "type": "object",
                "properties": {
                    "location": {"type": "string"}
                },
                "required": ["location"]
            }
        ))

    async def handle_tool(self, name, params):
        if name == "get_weather":
            weather_api = WeatherAPI()
            data = await weather_api.get_current(params["location"])
            return {
                "temperature": data.temp,
                "condition": data.condition,
                "humidity": data.humidity
            }
```

### Database Query Server

```python
class DatabaseMCPServer(Server):
    """MCP server for database access."""

    async def initialize(self):
        self.db = await connect_database()

        self.register_resource(Resource(
            uri="db://schema",
            name="Database Schema",
            description="Database structure"
        ))

    async def handle_resource(self, uri):
        if uri == "db://schema":
            return await self.db.get_schema()
```

## See Also

- [AGENTS.md](../AGENTS.md) - AI agent operations
- [Skill.md](./skill.md) - Agent skills specification
- [API Design](./api-design.md) - API best practices

## References

- [R1] Model Context Protocol Documentation. https://modelcontextprotocol.io/ (accessed 2025-10-23)
- [R2] MCP GitHub Repository. https://github.com/modelcontextprotocol/servers (accessed 2025-10-23)