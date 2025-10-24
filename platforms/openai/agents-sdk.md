---
title: OpenAI Agents SDK (Responses API)
slug: openai-agents-sdk
status: living
last_updated: 2025-10-24
tags: [openai, agents, sdk, responses-api, tool-calling, mcp]
summary: "Hands-on patterns for building agents with OpenAI SDKs using Responses API and tool calling."
sources:
  - { id: R1, title: "OpenAI Responses API Guide", url: "https://platform.openai.com/docs/guides/responses", accessed: "2025-10-24" }
  - { id: R2, title: "OpenAI Function Calling Guide", url: "https://platform.openai.com/docs/guides/function-calling", accessed: "2025-10-24" }
  - { id: R3, title: "openai-python - Official Python SDK", url: "https://github.com/openai/openai-python", accessed: "2025-10-24" }
  - { id: R4, title: "openai-node - Official Node.js SDK", url: "https://github.com/openai/openai-node", accessed: "2025-10-24" }
  - { id: R5, title: "OpenAI Streaming Guide", url: "https://platform.openai.com/docs/guides/streaming", accessed: "2025-10-24" }
  - { id: R6, title: "New tools and features in the Responses API", url: "https://openai.com/index/new-tools-and-features-in-the-responses-api/", accessed: "2025-10-24" }
---

# OpenAI Agents SDK (Responses API)

> **For Humans**: This guide documents hands-on patterns for building agents with the official OpenAI SDKs (Python & TypeScript) using the Responses API and tool calling. Examples favour clarity and production readiness—adapt them to your stack and keep an eye on OpenAI's release notes.
>
> **For AI Agents**: Follow these instructions literally only after confirming the project dependency versions. Use the linked resources to verify model IDs and APIs before editing code.

---

## 1. Prerequisites

- Python 3.9+ or Node.js 18+.
- `openai` SDK (`pip install "openai>=1.50.0"` or `npm install openai`).
- `OPENAI_API_KEY` set (optionally `OPENAI_ORG_ID`, `OPENAI_PROJECT_ID`).
- Familiarity with [Responses API](https://platform.openai.com/docs/guides/responses) and [Tool Calling](https://platform.openai.com/docs/guides/function-calling).

> **Tip:** Run `python -m openai health-check` / `npx openai health-check` to verify credentials.

---

## 2. Python Quickstart

```python
from openai import OpenAI

client = OpenAI()

def create_session_metadata(user_id: str) -> dict:
    return {"session_user": user_id}

def run_agent(user_id: str, message: str) -> str:
    response = client.responses.create(
        model="gpt-4.1-mini",
        input=[{"role": "user", "content": message}],
        metadata=create_session_metadata(user_id),
    )
    return response.output_text

if __name__ == "__main__":
    print(run_agent("user-123", "Outline a release plan for version 2.0"))
```

### Tool Registration

```python
from typing import Callable, Any

def make_search_tool(fn: Callable[[str], str]) -> dict[str, Any]:
    return {
        "type": "function",
        "function": {
            "name": "search_docs",
            "description": "Search support articles",
            "parameters": {
                "type": "object",
                "properties": {
                    "query": {"type": "string"},
                    "locale": {"type": "string", "enum": ["en", "ja", "es"]},
                },
                "required": ["query"],
            },
        },
    }

def search_docs(query: str, locale: str = "en") -> str:
    """Replace with actual search implementation."""
    return f"Searched {locale} docs for: {query}"

TOOLS = [make_search_tool(search_docs)]

def run_agent_with_tools(message: str) -> str:
    response = client.responses.create(
        model="gpt-4.1",
        input=[{"role": "user", "content": message}],
        tools=TOOLS,
        temperature=0.2,
    )

    # Dispatch tool calls
    outputs = []
    for item in response.output:
        if item.type == "tool_call":
            fn_name = item.tool_call.name
            args = item.tool_call.arguments
            if fn_name == "search_docs":
                result = search_docs(**args)
                outputs.append({"tool": fn_name, "result": result})
    return response.output_text, outputs
```

### Streaming

```python
with client.responses.stream(
    model="gpt-4.1-mini",
    input=[{"role": "user", "content": "Stream a haiku about observability"}],
) as stream:
    for event in stream:
        if event.type == "response.output_text.delta":
            print(event.delta, end="", flush=True)
    final = stream.get_final_response()
```

---

## 3. Node.js Quickstart

```typescript
import OpenAI from "openai";

const client = new OpenAI();

export async function runAgent(userId: string, message: string) {
  const response = await client.responses.create({
    model: "gpt-4.1-mini",
    input: [{ role: "user", content: message }],
    metadata: { session_user: userId },
  });

  return response.output_text;
}
```

### Tool Calling

```typescript
const tools = [
  {
    type: "function",
    function: {
      name: "fetch_issue",
      description: "Fetch GitHub issue details",
      parameters: {
        type: "object",
        properties: {
          repo: { type: "string" },
          issueNumber: { type: "integer" },
        },
        required: ["repo", "issueNumber"],
      },
    },
  },
];

export async function runWithTools(message: string) {
  const response = await client.responses.create({
    model: "gpt-4.1",
    input: [{ role: "user", content: message }],
    tools,
  });

  for (const item of response.output ?? []) {
    if (item.type === "tool_call" && item.tool_call?.name === "fetch_issue") {
      const { repo, issueNumber } = item.tool_call.arguments as {
        repo: string;
        issueNumber: number;
      };
      // Invoke your integration here
      console.log(`Would fetch issue ${issueNumber} from ${repo}`);
    }
  }

  return response.output_text;
}
```

### Streaming (SSE)

```typescript
const stream = await client.responses.stream({
  model: "gpt-4.1-mini",
  input: [{ role: "user", content: "Stream step-by-step deployment checks" }],
});

for await (const event of stream) {
  if (event.type === "response.output_text.delta") {
    process.stdout.write(event.delta);
  }
}

const final = await stream.finalResponse();
```

---

## 4. Session & State Management

The Responses API is stateless. Persist conversation history yourself:

```python
def load_history(session_id: str) -> list[dict]:
    # Fetch from database / cache
    ...

def save_history(session_id: str, messages: list[dict]) -> None:
    ...

def run_session(session_id: str, user_message: str) -> str:
    history = load_history(session_id)
    enriched_input = history + [{"role": "user", "content": user_message}]
    response = client.responses.create(model="gpt-4.1-mini", input=enriched_input)
    history.extend([
        {"role": "user", "content": user_message},
        {"role": "assistant", "content": response.output_text},
    ])
    save_history(session_id, history)
    return response.output_text
```

Persist state in Redis, Postgres, DynamoDB, etc. Enforce TTLs defined in SSOT.md.

---

## 5. Reliability Patterns

| Concern | Pattern |
|---------|---------|
| Rate limits (429) | Exponential backoff with jitter; inspect `response.rate_limit` metadata. |
| Timeouts | Configure `timeout` in SDK, ensure your HTTP client respects it. |
| Retries | Wrap `client.responses.create` with retry logic using Tenacity (Python) or `p-retry` (Node). |
| Logging | Capture prompts, responses, tool calls, token usage (redact secrets). |
| Monitoring | Emit metrics (latency, errors, token counts) via OpenTelemetry / StatsD. |

Example retry wrapper (Python):

```python
from tenacity import retry, stop_after_attempt, wait_exponential, retry_if_exception_type
from openai import OpenAIError

@retry(
    reraise=True,
    stop=stop_after_attempt(5),
    wait=wait_exponential(multiplier=1, min=1, max=20),
    retry=retry_if_exception_type(OpenAIError),
)
def safe_response(**kwargs):
    return client.responses.create(**kwargs)
```

---

## 6. Integration with Repo Conventions

- **AGENTS.md**: Document commands (linting, tests) for agent services, including how to run streaming demos.
- **SSOT.md**: Capture approved models (`gpt-4.1`, `gpt-4.1-mini`), timeout defaults, retention policies, and tool schemas.
- **PLANS.md**: For each new agent initiative, log decisions (model choice, tool set, safety mitigations) and validation steps.
- **SKILL.md**: If packaging special-purpose Skills, note when to leverage the low-level SDK vs. AgentKit.

---

## 7. Troubleshooting

| Symptom | Diagnosis | Resolution |
|---------|-----------|------------|
| `OpenAIError: model_not_found` | Using deprecated ID | Replace with current model (e.g., `gpt-4.1`) |
| Empty tool call args | Schema mismatch | Ensure `parameters` JSON schema matches function signature |
| Streaming stops mid-response | Client closed SSE stream | Keep connection open; set appropriate timeouts |
| Unexpected PII in logs | Raw prompts stored | Add redaction layer before logging |

Document any incidents in PLANS.md under “Surprises & Discoveries”.

---

## 8. Advanced Features (May 2025 Updates)

### 8.1 Model Context Protocol (MCP) Support [R6]

The Responses API now supports remote MCP servers, enabling agents to connect to external tools and services with minimal configuration.

**Supported MCP Services**:
- Stripe (payment processing)
- Shopify (e-commerce)
- Twilio (communications)
- Custom MCP servers

**Example**:
```python
# Connect to remote MCP server
response = client.responses.create(
    model="gpt-4.1",
    input=[{"role": "user", "content": "Process a payment for $50"}],
    mcp_servers=[
        {
            "type": "remote",
            "url": "https://api.example.com/mcp/stripe",
            "api_key": "your-api-key"
        }
    ]
)
```

**Key Benefits**:
- Seamless integration with external services
- Standardized tool interface via MCP protocol
- Reduced custom integration code

### 8.2 Native Image Generation

GPT-4o can now generate images directly within the Responses API using the `gpt-image-1` model.

**Features**:
- Real-time streaming previews
- Multi-turn refinement
- Direct integration in agent workflows

**Example**:
```python
response = client.responses.create(
    model="gpt-image-1",
    input=[{
        "role": "user",
        "content": "Generate a professional logo for a tech startup"
    }],
    stream=True
)

# Stream preview updates
for event in response:
    if event.type == "image.preview":
        display_preview(event.preview_url)
    elif event.type == "image.final":
        save_image(event.image_url)
```

### 8.3 Code Interpreter Integration

The Code Interpreter tool is now built into the Responses API, enabling models to handle data analysis, complex math, and logic-based tasks.

**Capabilities**:
- Execute Python code
- Data analysis and visualization
- Complex mathematical computations
- File processing

**Example**:
```python
response = client.responses.create(
    model="gpt-4.1",
    input=[{
        "role": "user",
        "content": "Analyze this CSV data and create a visualization"
    }],
    tools=[
        {"type": "code_interpreter"}
    ],
    files=["data.csv"]
)
```

### 8.4 Enhanced File Search

File search capabilities have been upgraded with:
- Multi-vector store searches
- Array-based attribute filtering
- Improved relevance ranking

**Example**:
```python
response = client.responses.create(
    model="gpt-4.1",
    input=[{"role": "user", "content": "Find all Q4 financial reports"}],
    tools=[{
        "type": "file_search",
        "vector_stores": ["store-1", "store-2"],
        "filters": {
            "quarter": ["Q4"],
            "year": [2024, 2025]
        }
    }]
)
```

### 8.5 Reasoning Models (o3, o4-mini)

The o-series reasoning models now support tool calling and function execution within their chain-of-thought process.

**Key Features**:
- Tools called directly within reasoning
- Preserved reasoning tokens across requests
- More contextually relevant answers

**Example**:
```python
response = client.responses.create(
    model="o4-mini",
    input=[{
        "role": "user",
        "content": "Research the latest AI safety guidelines and summarize"
    }],
    tools=TOOLS,
    temperature=1.0  # Reasoning models benefit from temperature=1
)
```

**Available Models**:
- `o3`: Most capable reasoning model
- `o4-mini`: Efficient reasoning for common tasks

---

## 9. Resources (2025-10-24)

- [OpenAI Responses API](https://platform.openai.com/docs/guides/responses)
- [Tool Calling Guide](https://platform.openai.com/docs/guides/function-calling)
- [Python SDK Reference](https://github.com/openai/openai-python)
- [Node.js SDK Reference](https://github.com/openai/openai-node)
- [Streaming Examples](https://platform.openai.com/docs/guides/streaming)

---

## 10. Adoption Checklist

- [ ] Install/update OpenAI SDKs; run health checks.
- [ ] Define storage strategy for conversation history and tool outputs.
- [ ] Implement safety/modeation guardrails; document in SSOT.md.
- [ ] Register tools with validated JSON schemas and unit tests.
- [ ] Instrument retries, logging, and tracing.
- [ ] Write integration tests covering tool calling and streaming flows.
- [ ] Update AGENTS.md with runbooks and CI commands.
- [ ] Launch to staging; monitor latency, cost, error rate.
- [ ] Promote to production with rollback plan.

---

## 11. Update Log

- 2025-10-24: Added May 2025 features: MCP support, native image generation (gpt-image-1), Code Interpreter integration, enhanced file search, and o-series reasoning models (o3, o4-mini).
- 2025-10-23: Added official OpenAI documentation references for Responses API, Function Calling, SDKs, and Streaming.
- 2025-10-20: Rebuilt guide around the Responses API, updated code samples for `openai` SDK ≥ 1.50, and aligned reliability/safety recommendations with repository conventions.

---

**Remember:** The official SDK is your source of truth. Keep dependencies up-to-date, watch for deprecation notices, and update this guide whenever OpenAI ships a new agent capability.***
