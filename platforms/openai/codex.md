---
title: OpenAI Code Generation Guide (Post-Codex)
slug: openai-code-generation
status: living
last_updated: 2025-10-23
tags: [openai, code-generation, codex, gpt-4, responses-api]
summary: "Modern code-assistance patterns using OpenAI Responses API after Codex deprecation."
sources: []
---

# OpenAI Code Generation Guide (Post-Codex)

> **For Humans**: Codex-specific models (`code-davinci-*`) are deprecated. This guide explains how to deliver code-assistance features with the modern OpenAI Responses API and GPT-4.x models. Legacy behaviour is retained in marked sections for teams still migrating.
>
> **For AI Agents**: Use current API surfaces (`client.responses.create`, `responses.stream`) for all new work. Only reference the legacy section when maintaining historical integrations, and flag any remaining Codex usage to the team.

---

## 1. Recommended Models (2025-10-20)

| Use Case | Suggested Model | Notes |
|----------|-----------------|-------|
| Deterministic code completion | `gpt-4.1-mini` with `temperature=0.1-0.2` | Balance of quality and latency |
| Multi-language refactoring | `gpt-4.1` | Higher reasoning score |
| Lightweight edits / inline suggestions | `gpt-4o-mini` | Lower cost, faster |
| Natural-language → unit tests | `gpt-4.1` or `gpt-4.1-mini` | Provide explicit formatting instructions |

Always consult the [OpenAI model index](https://platform.openai.com/docs/models) for the latest IDs.

---

## 2. TypeScript Example (Responses API)

```typescript
import OpenAI from "openai";

const client = new OpenAI();

type CodeCompletionInput = {
  language: "typescript" | "python" | "go";
  prompt: string;
  cursorContext?: string;
};

export async function completeCode(input: CodeCompletionInput) {
  const { language, prompt, cursorContext } = input;

  const response = await client.responses.create({
    model: "gpt-4.1-mini",
    input: [
      {
        role: "system",
        content: [
          { type: "text", text: "You are a careful senior engineer. Produce only code." },
        ],
      },
      {
        role: "user",
        content: [
          {
            type: "text",
            text: [
              `Language: ${language}`,
              `Context:\n${cursorContext ?? "None"}`,
              `Prompt:\n${prompt}`,
            ].join("\n\n"),
          },
        ],
      },
    ],
    temperature: 0.15,
    max_output_tokens: 256,
  });

  return response.output_text.trim();
}
```

### Streaming to IDEs

```typescript
export async function streamSuggestion(prompt: string, onDelta: (chunk: string) => void) {
  const stream = await client.responses.stream({
    model: "gpt-4o-mini",
    input: [{ role: "user", content: prompt }],
  });

  for await (const event of stream) {
    if (event.type === "response.output_text.delta") {
      onDelta(event.delta);
    }
  }

  const final = await stream.finalResponse();
  return final.output_text;
}
```

---

## 3. Python Example (Code Refactor)

```python
from openai import OpenAI

client = OpenAI()

def suggest_refactor(code_snippet: str, guidelines: str) -> str:
    response = client.responses.create(
        model="gpt-4.1",
        input=[
            {
                "role": "system",
                "content": [
                    {"type": "text", "text": "You are a meticulous refactoring assistant."}
                ],
            },
            {
                "role": "user",
                "content": [
                    {
                        "type": "text",
                        "text": f"Guidelines:\n{guidelines}\n\nCode:\n```python\n{code_snippet}\n```",
                    }
                ],
            },
        ],
        temperature=0.2,
        max_output_tokens=512,
    )

    return response.output_text.strip()
```

---

## 4. Editor Integration Checklist

1. **Request batching** – throttle suggestions to avoid sending every keystroke; debouncing ≥ 300 ms is common.
2. **Context window management** – include surrounding lines (e.g., previous 100 lines and following 50 lines).
3. **Diffing** – compute patches relative to current buffer; do not overwrite user edits blindly.
4. **Cancellation** – allow users to cancel streaming requests; abort fetch/SSE connection.
5. **Telemetry** – log latency, accept/decline rate, and token usage (redacted).

---

## 5. Performance Optimisations

| Strategy | Description |
|----------|-------------|
| Prompt caching | Store prompt → completion pairs (e.g., in Redis) to avoid redundant calls. |
| Temperature tuning | Lower temperatures (0.1–0.2) yield deterministic suggestions. |
| Structured prompts | Use explicit tags (`<selection>`, `<cursor>`) to reduce ambiguity. |
| Async pipelines | Fire-and-forget completions for background tasks (doc generation). |
| Chunked requests | For large contexts, summarise or send only the relevant diff. |

Sample prompt template:

```
<task>Generate unit tests</task>
<language>python</language>
<code>
{{SOURCE_CODE}}
</code>
<constraints>
- Use pytest
- Aim for branch coverage
- Do not include main guards
</constraints>
```

---

## 6. Safety & Compliance

- Redact secrets before sending prompts (regex checks for `api_key`, `password`, etc.).
- Post-process outputs to remove accidental credentials.
- Implement policy review for auto-applied diffs (manual approval, code owners).
- Log all AI-generated code in audit trails with model metadata.
- Document governance rules in SSOT.md (retention, review requirements).

---

## 7. Troubleshooting

| Issue | Likely Cause | Mitigation |
|-------|--------------|------------|
| Suggestions reference deprecated APIs | Training data staleness | Add reminders in prompts (`Context: use FastAPI v0.111`). |
| Non-deterministic output | Temperature too high | Reduce temperature, add few-shot exemplars. |
| Slow responses | Large prompt context | Trim context, cache embeddings, or switch to `gpt-4o-mini`. |
| Invalid syntax | Missing structure in prompt | Provide language-specific instructions and schema. |

Log findings in PLANS.md under “Surprises & Discoveries” and update AGENTS.md runbooks as mitigations are implemented.

---

## 8. Legacy Codex Section (Read-Only)

> **Warning:** The endpoints and models below are deprecated and retained solely for historical context. Migrate to the Responses API immediately.

```bash
curl https://api.openai.com/v1/completions \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "code-davinci-002",
    "prompt": "def add(a, b):",
    "max_tokens": 100
  }'
```

If you still support legacy integrations:

- Flag them in SSOT.md as “Legacy”.
- Create an ExecPlan to migrate by replacing `Completion.create` calls with `Responses API`.
- Update clients to use the modern SDK (`openai` ≥ 1.0).

---

## 9. Integration with Repository Docs

- **AGENTS.md** – include commands for running code-generation regression tests and linting AI-generated code.
- **SSOT.md** – store approved models, temperature ranges, and review policies.
- **PLANS.md** – track migration from Codex to GPT-4.x models; include validation criteria.
- **SKILL.md** – document when to load Skills that specialise in code review, refactoring, or doc generation.

---

## 10. Update Log

- 2025-10-20: Replaced Codex examples with Responses API patterns, added legacy deprecation notice, and refreshed performance/safety guidance.

---

**Remember:** Treat this guide as a living companion to OpenAI’s model releases. Update prompts, models, and safeguards whenever new capabilities or deprecations are announced.***
