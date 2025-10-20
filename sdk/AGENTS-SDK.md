# OpenAI Agents SDK (Responses API)

> **For Humans**: This guide documents hands-on patterns for building agents with the official OpenAI SDKs (Python & TypeScript) using the Responses API and tool calling. Examples favour clarity and production readiness—adapt them to your stack and keep an eye on OpenAI’s release notes.
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

## 8. Resources (2025-10-20)

- [OpenAI Responses API](https://platform.openai.com/docs/guides/responses)
- [Tool Calling Guide](https://platform.openai.com/docs/guides/function-calling)
- [Python SDK Reference](https://github.com/openai/openai-python)
- [Node.js SDK Reference](https://github.com/openai/openai-node)
- [Streaming Examples](https://platform.openai.com/docs/guides/streaming)

---

## 9. Adoption Checklist

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

## 10. Update Log

- 2025-10-20: Rebuilt guide around the Responses API, updated code samples for `openai` SDK ≥ 1.50, and aligned reliability/safety recommendations with repository conventions.

---

**Remember:** The official SDK is your source of truth. Keep dependencies evergreen, watch for deprecation notices, and update this guide whenever OpenAI ships a new agent capability.***
