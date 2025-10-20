# OpenAI AgentKit (Conceptual Patterns)

> **For Humans**: This document consolidates community practices for building higher-level “AgentKit” style frameworks on top of the official OpenAI SDKs. OpenAI has showcased AgentKit concepts in blog posts, but the API surface is not yet a formal, versioned product. Treat every code sample as illustrative and adapt it to your organisation’s implementation.
>
> **For AI Agents**: Use these instructions as a conceptual template only. Confirm actual class names, package versions, and deployment scripts with the project’s repository before you attempt modifications.

---

## 1. Why AgentKit?

OpenAI’s Responses API and tool calling provide low-level capabilities. Teams often build an “AgentKit” layer to:

- Standardise **tool orchestration** across projects.
- Provide reusable **memory abstractions** (Redis, vector stores, SQL).
- Enforce **safety policies** and moderation by default.
- Offer batteries-included **logging, auditing, and evaluation** hooks.
- Accelerate **multi-agent** and **background task** workflows.

> **Status (2025-10-20):** OpenAI has previewed AgentKit concepts, but official SDK packages may differ. This guide documents extensibility points you can recreate today.

---

## 2. Reference Architecture (Sample)

```
┌────────────┐     ┌──────────────┐     ┌───────────────┐
│  HTTP/API  │────▶│  Agent Hub   │────▶│  Tool Registry │
└────────────┘     └──────┬───────┘     └──────┬────────┘
                           │                   │
                           ▼                   ▼
                    ┌──────────────┐   ┌──────────────┐
                    │ Memory Layer │   │ Safety Layer │
                    └──────────────┘   └──────────────┘
                           │
                           ▼
                    ┌──────────────┐
                    │ OpenAI SDK   │ (Responses / Assistants)
                    └──────────────┘
```

Your implementation typically provides:
- A registry for tools (Python callables, HTTP endpoints, workflows).
- Memory drivers (conversation window, long-term vector memory, per-user store).
- Safety policies (moderation thresholds, PII scrubbing, audit logging).
- Hooks for telemetry (Prometheus, OpenTelemetry, DataDog, etc.).

---

## 3. Core Building Blocks

### 3.1 Agent Wrapper (Sample)

```python
# Sample only. The classes below are placeholders—create equivalents in your codebase.
from dataclasses import dataclass
from typing import Iterable, Protocol
from openai import OpenAI

client = OpenAI()

class SafetyPolicy(Protocol):
    def validate(self, prompt: str) -> None: ...

class MemoryStore(Protocol):
    def load_context(self, session_id: str) -> list[dict]: ...
    def persist(self, session_id: str, messages: list[dict]) -> None: ...

class ToolRegistry(Protocol):
    def resolve(self, tool_name: str): ...

@dataclass
class AgentConfig:
    model: str = "gpt-4.1-mini"
    temperature: float = 0.2

class Agent:
    def __init__(
        self,
        name: str,
        safety_policy: SafetyPolicy,
        memory_store: MemoryStore,
        tool_registry: ToolRegistry,
        config: AgentConfig | None = None,
    ) -> None:
        self.name = name
        self.safety_policy = safety_policy
        self.memory_store = memory_store
        self.tool_registry = tool_registry
        self.config = config or AgentConfig()

    def run(self, session_id: str, user_message: str) -> str:
        self.safety_policy.validate(user_message)
        history = self.memory_store.load_context(session_id)
        request = history + [{"role": "user", "content": user_message}]

        response = client.responses.create(
            model=self.config.model,
            input=request,
            tools=self.tool_registry.resolve("all"),
            temperature=self.config.temperature,
            metadata={"agent": self.name, "session": session_id},
        )

        reply = response.output_text
        history.append({"role": "assistant", "content": reply})
        self.memory_store.persist(session_id, history)
        return reply
```

> **Adaptation Checklist:** Implement concrete `SafetyPolicy`, `MemoryStore`, and `ToolRegistry` classes. Log prompts/responses per your compliance policy.

### 3.2 Tool Orchestration (Sample)

```python
from collections.abc import Callable

class DictToolRegistry:
    def __init__(self, tools: dict[str, Callable]) -> None:
        self._tools = tools

    def resolve(self, tool_name: str | None) -> list[dict]:
        """Return OpenAI tool definitions."""
        selected = (
            self._tools.values() if tool_name in (None, "all") else [self._tools[tool_name]]
        )
        tool_specs = []
        for tool in selected:
            spec = getattr(tool, "openai_tool_spec", None)
            if not spec:
                raise ValueError(f"Tool {tool.__name__} missing OpenAI spec")
            tool_specs.append(spec)
        return tool_specs


def openai_tool(name: str, parameters: dict):
    """Decorator to add JSON schema metadata required by Responses tool calling."""
    def decorator(fn: Callable):
        fn.openai_tool_spec = {
            "type": "function",
            "function": {
                "name": name,
                "description": fn.__doc__ or "",
                "parameters": parameters,
            },
        }
        return fn
    return decorator


@openai_tool(
    name="search_docs",
    parameters={
        "type": "object",
        "properties": {"query": {"type": "string"}},
        "required": ["query"],
    },
)
def search_docs(query: str) -> str:
    # Implement your retrieval logic here
    return "Sample docs result"
```

### 3.3 Memory Patterns

| Memory Type | Description | Implementation Notes |
|-------------|-------------|----------------------|
| Conversation window | Last N turns | Keep in Redis / Postgres keyed by session |
| Long-term knowledge | Vector store entries | Use embeddings (`text-embedding-3-large`) and similarity thresholds |
| Structured state | Key-value context | JSON blob per session (ensure size bounds) |

All memory writes should include retention policies (e.g., TTL) and comply with your SSOT.md data policies.

### 3.4 Safety Policy Skeleton

```python
class ModerationSafetyPolicy:
    def __init__(self, client: OpenAI, categories: Iterable[str]):
        self.client = client
        self.categories = set(categories)

    def validate(self, prompt: str) -> None:
        mod = self.client.moderations.create(
            model="omni-moderation-latest",
            input=prompt,
        )
        flagged = any(result.flagged for result in mod.results)
        if flagged:
            raise PermissionError("Prompt blocked by moderation policy")
```

Extend this with PII detection, domain allowlists, or custom heuristics.

---

## 4. Multi-Agent Patterns

### 4.1 Coordinator + Specialist Agents

```python
def orchestrate_request(issue: str):
    coordinator = Agent(
        name="coordinator",
        safety_policy=CoordinatorSafetyPolicy(),
        memory_store=CoordinatorMemory(),
        tool_registry=CoordinatorTools(),
    )

    plan = coordinator.run(session_id="issue-123", user_message=issue)

    if "code change" in plan.lower():
        return code_agent.run("issue-123", plan)
    elif "customer" in plan.lower():
        return support_agent.run("issue-123", plan)
    return plan
```

> **Logging:** Record routing decisions and agent outputs in an audit trail for traceability.

### 4.2 Background Tasks

Use background workers (Celery, Dramatiq, Temporal) to offload long-running agent workflows. Ensure:
- Task IDs map back to session IDs.
- Memory stores are thread/process safe.
- Retry policies follow your incident playbook.

---

## 5. Evaluation & Monitoring

| Domain | Suggested Metrics / Tools |
|--------|---------------------------|
| Quality | Satisfaction surveys, rubric-based grading via Responses API |
| Safety | Moderation false positives/negatives, override counts |
| Performance | Latency (P50/P95), tool call durations, retry counts |
| Cost | Token usage by model, tool call volume |

Set up dashboards (Grafana, DataDog) and alert thresholds. Include evaluation steps in ExecPlans (`PLANS.md`) before major releases.

---

## 6. Deployment Considerations

1. **Infrastructure** – containerise the agent hub; depend on managed Redis/vector DB services where possible.
2. **Secrets** – use Vault / AWS Secrets Manager; rotate `OPENAI_API_KEY` regularly.
3. **Rollout** – adopt canary deployments and capture feedback. Use feature flags per agent.
4. **Compliance** – document data retention, access control, and audit logging in SSOT.md; ensure AGENTS.md references this guide.

Sample docker compose fragment (edit to match your stack):

```yaml
services:
  agent-gateway:
    build: .
    environment:
      OPENAI_API_KEY: ${OPENAI_API_KEY}
      REDIS_URL: redis://redis:6379/0
    depends_on:
      - redis
  redis:
    image: redis:7
    command: ["redis-server", "--save", "", "--appendonly", "no"]
```

---

## 7. Security Checklist

- [ ] Validate every user input (length limits, profanity filters, PII checks).
- [ ] Apply moderation to both user prompts and model outputs where mandated.
- [ ] Redact secrets from stored histories.
- [ ] Implement role-based access control for tool invocation (admin vs. user tools).
- [ ] Audit log prompt/response/tool call metadata (redact sensitive content).
- [ ] Document safeguards in SSOT.md and ensure AGENTS.md references them.

---

## 8. Integration with Repository Docs

- **AGENTS.md** – list AgentKit commands, testing routines, and deployment steps.
- **SSOT.md** – formalise safety policies, retention periods, and tool definitions.
- **PLANS.md** – use ExecPlans to capture agent enhancements, surprises, and retro notes.
- **SKILL.md** – create Skills describing when to load AgentKit patterns for domain-specific agents.

---

## 9. Troubleshooting

| Symptom | Possible Cause | Suggested Fix |
|---------|----------------|---------------|
| Agent replies with stale info | Memory not pruned or updated | Implement TTLs; refresh vector indexes |
| Tool call fails | Schema mismatch or timeout | Validate JSON schema, add retries/backoff |
| Safety policy blocks valid prompts | Moderation thresholds too strict | Log decisions, tweak thresholds, whitelist patterns |
| High latency | Large context window or synchronous tools | Optimise prompts; move blocking tools to async workers |

Log incidents in project PLANS.md and update AGENTS.md once resolved.

---

## 10. Update Log

- 2025-10-20: Reframed AgentKit as a conceptual layer, swapped hypothetical APIs for sample interfaces, and aligned safety/memory patterns with the 2025 OpenAI Responses API.

---

**Remember:** AgentKit is not a drop-in SDK. Use it as inspiration for structuring your own agent frameworks. Validate implementation details against the official OpenAI SDK reference before shipping changes.***
