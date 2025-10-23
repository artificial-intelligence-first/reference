# OpenAI SDK Reference (2025 Edition)

> **For Humans**: Use this guide to understand how current OpenAI SDKs map to agent orchestration and code-generation workflows. Every section calls out which pieces are official, which are community patterns, and what must be validated against the latest platform docs.
>
> **For AI Agents**: Treat these instructions as a high-level playbook. Before executing commands, confirm model names and API surfaces against the OpenAI Platform docs linked in each section—APIs evolve rapidly.

## Overview

The `platforms/` directory captures three complementary ways to build with OpenAI today:

1. **AgentKit (Conceptual scaffold)** – community patterns that layer structure, memory, and guardrails *on top of* the official SDKs. This guide is intentionally marked as conceptual because the exact API surface will differ across internal or third-party implementations.
2. **Agents SDK (Low-level APIs)** – practical usage of the official OpenAI Python and JavaScript SDKs powering the [Responses API](https://platform.openai.com/docs/guides/responses) and tool calling. Focus here if you need fine-grained control.
3. **Code Generation (Post-Codex)** – modern code-focused workflows using general models such as `gpt-4.1` and `gpt-4.1-mini`. Historical Codex behaviour is documented for context with prominent deprecation notices.

> **Version Note (2025-10-20):** Codex-specific endpoints (`/v1/completions` with `code-davinci-*`) are deprecated. All runnable examples below target the current OpenAI SDKs (`openai` Python/TypeScript packages ≥ 1.50) and the Responses API unless marked as *Legacy*.

## SDK Comparison (2025-10-20)

| Capability | AgentKit (Conceptual) | Agents SDK (Responses API) | Code Generation (Responses) |
|------------|-----------------------|----------------------------|-----------------------------|
| **Abstraction Level** | High-level scaffolding | Low-level primitives | Task-specific patterns |
| **Primary Audience** | Teams wanting batteries-included workflows | Engineers needing granular control | Developers enhancing editors/CI/CD |
| **State & Memory** | Opinionated wrappers (Redis/vector DB etc.) | Manual via your storage layer | Not applicable / external |
| **Safety** | Policy templates + moderation hooks (sample) | Implemented explicitly with SDK hooks | Prompt/output filtering |
| **Tool Orchestration** | Declarative registry + scheduler | Explicit tool registration with SDK | Usually not required |
| **Streaming** | Depends on underlying SDK | Supported (`client.responses.stream`) | Supported |
| **Recommended Models** | `gpt-4.1`, `gpt-4.1-mini` for general agents | Same as AgentKit | `gpt-4.1-mini`, `gpt-4o-mini` for code |
| **Status** | Conceptual/Community | Official | Official |

## Quick Decision Guide

- **Choose AgentKit patterns when…**
  - You want pre-built conventions (memory, audit logging, policy enforcement).
  - Your organisation already wraps the OpenAI SDK with internal tooling.
  - You need a repeatable structure for multiple autonomous agents.
- **Choose the Agents SDK guide when…**
  - You require direct control of sessions, tool invocation, retries, and telemetry.
  - You are building bespoke workflows or integrating with existing orchestration layers.
  - You plan to deploy to production and must own reliability characteristics.
- **Choose the Code Generation guide when…**
  - You are embedding AI into IDEs, linters, documentation pipelines, or CI.
  - You need deterministic code edits, refactorings, or test generation.
  - You are migrating from legacy Codex-based integrations and require up-to-date practices.

## Documentation Map

- **[agent-kit.md](openai/agent-kit.md)** – conceptual scaffolding, memory models, safety guardrails (marked as *Sample* wherever APIs are hypothetical).
- **[agents-sdk.md](openai/agents-sdk.md)** – Python/TypeScript usage of `openai` SDK (`client.responses.create`, tool calling, streaming).
- **[codex.md](openai/codex.md)** – modern code-assistance flows plus legacy Codex notes, performance tuning, and editor integration.

Each guide flags historical content with “Legacy” callouts and links back to the official platform documentation.

## Integration Patterns (Samples)

### Hybrid Agent (AgentKit + Agents SDK)

```python
# Sample only – replace with your framework abstractions.
from openai import OpenAI

client = OpenAI()

def plan_with_responses(user_goal: str) -> str:
    """Leverage low-level Responses API for precise control."""
    reply = client.responses.create(
        model="gpt-4.1-mini",
        input=[{"role": "user", "content": user_goal}],
        metadata={"purpose": "agent-planning"}
    )
    return reply.output_text

# Pseudo AgentKit wrapper (conceptual):
class HybridAgent:
    def __init__(self, memory_store, safety_policy):
        self.memory = memory_store      # e.g., Redis or vector DB
        self.safety_policy = safety_policy

    def run(self, user_goal: str) -> str:
        self.safety_policy.assert_allowed(user_goal)
        plan = plan_with_responses(user_goal)
        self.memory.store_interaction(user_goal, plan)
        return plan
```

### Agents SDK + Code Generation

```python
from openai import OpenAI

client = OpenAI()

def build_code_plan(task_description: str) -> str:
    """Plan the work using structured tool output."""
    response = client.responses.create(
        model="gpt-4.1",
        input=[{
            "role": "user",
            "content": [
                {"type": "text", "text": f"Plan the steps to {task_description}."}
            ]
        }],
        tools=[]  # register custom tools if needed
    )
    return response.output_text

def generate_code(task_description: str) -> str:
    plan = build_code_plan(task_description)
    completion = client.responses.create(
        model="gpt-4o-mini",
        input=[{
            "role": "user",
            "content": [
                {"type": "text", "text": f"{plan}\n\nProduce TypeScript code for this."}
            ]
        }]
    )
    return completion.output_text
```

### Full Stack (AgentKit concepts + Agents SDK + Code Generation)

```python
# Sample pipeline tying memory, planning, and code generation together.
def orchestrate_agentic_code_change(issue_summary: str, repo_state: dict) -> dict:
    plan = build_code_plan(issue_summary)
    proposed_code = generate_code(issue_summary)

    return {
        "issue": issue_summary,
        "plan": plan,
        "diff": proposed_code,
        "metadata": {"source": "gpt-4o-mini"}
    }
```

> **Sample Notice:** The snippets above illustrate interactions. Add validation, rate limiting, and telemetry before production use.

## Installation Snapshot (2025-10-20)

```bash
# Python
pip install "openai>=1.50.0"

# JavaScript/TypeScript
npm install openai
# or
pnpm add openai
```

Environment variables:

```bash
OPENAI_API_KEY=sk-your-key
OPENAI_API_BASE=https://api.openai.com/v1  # default
```

AgentKit-specific tooling (if you adopt a community framework) will include additional dependencies such as Redis clients or vector stores—see `openai/agent-kit.md` for "Sample" configuration tables.

## Integration with topics/ Guides

- **AGENTS.md** – update the "Dev Environment / Testing / PR Instructions" sections with the commands from `openai/agents-sdk.md`.
- **SSOT.md** – record canonical SDK versions, approved models, and safety policies established in `openai/agent-kit.md`.
- **PLANS.md** – when executing ExecPlans involving SDK integrations, reference validation steps defined in the relevant SDK guide.
- **SKILL.md** – if packaging Skills around these SDKs, ensure triggers point to the latest API names (`Responses API`, `Tool calling`) instead of legacy "Codex".

## Troubleshooting (Shared Themes)

| Issue | Mitigation |
|-------|------------|
| **401 / Auth errors** | Confirm `OPENAI_API_KEY`, verify the key has the “Responses API” entitlement. |
| **429 / Rate limits** | Implement backoff + jitter, monitor `response.rate_limit` headers. |
| **Model not found** | Replace legacy model IDs (e.g., `code-davinci-002`) with current releases such as `gpt-4.1` or `gpt-4o-mini`. |
| **Tool invocation errors** | Validate JSON schema for tools, ensure arguments are serialisable, log Requests/Responses for debugging. |
| **Streaming glitches** | Use SDK-provided streaming helpers (`client.responses.stream`) and close server-sent-event connections explicitly. |

## Resources (Verified 2025-10-20)

- [OpenAI Platform Documentation](https://platform.openai.com/docs)
- [Responses API Guide](https://platform.openai.com/docs/guides/responses)
- [Tool Calling](https://platform.openai.com/docs/guides/function-calling)
- [OpenAI Python SDK](https://github.com/openai/openai-python)
- [OpenAI Node.js SDK](https://github.com/openai/openai-node)

Historical Codex documentation is linked in `openai/codex.md` with deprecation notices.

## Adoption Checklist

- [ ] Confirm organisational policy for model usage (record in SSOT.md).
- [ ] Install the latest OpenAI SDKs and run health checks (`python -m openai health-check` / `npx openai health-check`).
- [ ] Prototype workflows in staging using the `openai/agents-sdk.md` examples.
- [ ] If using conceptual AgentKit patterns, adapt them to your internal frameworks and annotate differences.
- [ ] Define validation and rollback procedures in project ExecPlans (`PLANS.md`).
- [ ] Update AGENTS.md with testing commands, linting, and deployment steps.
- [ ] Roll out to production with monitoring dashboards (latency, token usage, error rates).

## Update Log

- 2025-10-20: Rebuilt SDK overview to align with the Responses API, added Codex deprecation warnings, and marked AgentKit sections as conceptual samples.

---

**Remember:** Always verify model IDs, API endpoints, and SDK versions against the official OpenAI release notes before committing changes.***
