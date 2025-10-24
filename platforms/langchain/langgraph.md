---
title: LangGraph
slug: langgraph
status: living
last_updated: 2025-10-24
tags: [agents, orchestration, planning, tool-calling, best-practices]
summary: "Graph-based orchestration framework for building stateful, multi-agent workflows with human-in-the-loop support and persistent state management."
sources:
  - { id: R1, title: "LangGraph Official Website", url: "https://www.langchain.com/langgraph", accessed: "2025-10-24" }
  - { id: R2, title: "LangGraph GitHub Repository", url: "https://github.com/langchain-ai/langgraph", accessed: "2025-10-24" }
  - { id: R3, title: "LangGraph Documentation", url: "https://langchain-ai.github.io/langgraph/", accessed: "2025-10-24" }
  - { id: R4, title: "LangGraph AI Framework 2025 Guide", url: "https://latenode.com/blog/langgraph-ai-framework-2025-complete-architecture-guide-multi-agent-orchestration-analysis", accessed: "2025-10-24" }
  - { id: R5, title: "AWS Prescriptive Guidance - LangGraph", url: "https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-frameworks/langchain-langgraph.html", accessed: "2025-10-24" }
  - { id: R6, title: "LangGraph Release Week Recap", url: "https://blog.langchain.com/langgraph-release-week-recap/", accessed: "2025-10-24" }
  - { id: R7, title: "LangGraph Workflow Updates", url: "https://changelog.langchain.com/announcements/langgraph-workflow-updates-python-js", accessed: "2025-10-24" }
---

# LangGraph

> **For Humans**
> This document explains LangGraph, a framework for building stateful, multi-agent workflows using graph-based orchestration. Use this when developing complex agent systems that require conditional logic, parallel execution, human oversight, or persistent state across sessions.

> **For AI Agents**
> This document defines LangGraph capabilities, architecture patterns, and best practices. Apply these patterns when implementing multi-agent systems, building workflows with conditional branching, or creating agents that require human-in-the-loop approval. Cross-reference with LangChain documentation for foundational concepts.

---

## Table of Contents

- [TL;DR](#tldr)
- [Canonical Definitions](#canonical-definitions)
- [Core Patterns](#core-patterns)
- [Decision Checklist](#decision-checklist)
- [Anti-patterns / Pitfalls](#anti-patterns--pitfalls)
- [Evaluation](#evaluation)
- [Recent Updates (2025)](#recent-updates-2025)
- [Update Log](#update-log)
- [See Also](#see-also)
- [References](#references)

---

## TL;DR

- **What**: Python framework for building stateful, multi-agent workflows using directed graph architectures with nodes representing actions and edges defining execution flow
- **Why**: Enables complex agent orchestration with conditional logic, parallel execution, persistent state, human-in-the-loop controls, and time-travel debugging
- **When**: Building multi-agent systems, workflows requiring conditional branching, agents needing human approval, long-running tasks that pause/resume, or applications requiring state persistence
- **How**: Define graph nodes as functions, specify edges for control flow, manage state with reducers, deploy via LangGraph Cloud or self-host
- **Watch out**: Steeper learning curve than LangChain, requires understanding of graph concepts, state management complexity, potential for circular dependencies

---

## Canonical Definitions

### LangGraph

**Definition**: A Python-based framework for building stateful, multi-agent applications using directed graph architectures, where actions are organized as nodes and execution flow is controlled by edges, with built-in support for state persistence, human-in-the-loop workflows, and streaming [R1][R2][R3].

**Scope**:
- **Includes**: Graph-based workflow orchestration, state management, persistence, human-in-the-loop, time-travel debugging, streaming, multi-agent coordination, conditional branching, parallel execution
- **Excludes**: LLM implementations, vector databases, basic chain composition (use LangChain for simpler workflows)

**Key Concepts**:
1. **Nodes**: Individual functions or agents that perform actions
2. **Edges**: Connections that define execution flow (normal, conditional, parallel)
3. **State**: Shared context passed between nodes with reducer functions
4. **Checkpoints**: Snapshots of state enabling pause/resume and time-travel

**Platform Support** [R1][R3]:
- **Languages**: Python (primary), JavaScript (supported)
- **Deployment**: LangGraph Cloud (managed), Self-hosted, VPC deployment
- **Tooling**: LangGraph Studio (visual debugging), LangGraph Platform (production deployment)

**Related Concepts**:
- **Built on**: LangChain framework components
- **Complementary**: LangSmith (observability), LangMem (long-term memory)
- **Similar**: Apache Airflow (workflow orchestration), Prefect (task orchestration)

**Sources**: [R1][R2][R3][R4]

### StateGraph

**Definition**: The core construct in LangGraph that defines workflow structure with typed state objects passed between nodes [R3].

**Scope**: Graph construction, node registration, edge definition, state schema definition.

**Example**:
```python
from langgraph.graph import StateGraph, END
from typing import TypedDict, Annotated
import operator

class State(TypedDict):
    messages: Annotated[list, operator.add]
    next_step: str

graph = StateGraph(State)
graph.add_node("agent", agent_node)
graph.add_node("tools", tool_node)
graph.add_edge("agent", "tools")
graph.add_conditional_edges(
    "tools",
    should_continue,
    {"continue": "agent", "end": END}
)
graph.set_entry_point("agent")
```

**Sources**: [R3]

### Checkpoints

**Definition**: Snapshots of workflow state that enable persistence, pause/resume functionality, and time-travel debugging [R1][R3].

**Capabilities** [R1][R3]:
- **Persistence**: Store entire state in external storage (PostgreSQL, Redis, etc.)
- **Pause/Resume**: Workflows can pause and resume later, even in different environments
- **Time-Travel**: Roll back to previous states and explore alternative execution paths
- **Human-in-the-Loop**: Await human approval before continuing execution

**Example**:
```python
from langgraph.checkpoint.postgres import PostgresSaver

# Configure checkpointer
checkpointer = PostgresSaver.from_conn_string(
    "postgresql://user:pass@localhost/db"
)

# Create graph with checkpointing
app = graph.compile(checkpointer=checkpointer)

# Execute with thread ID for persistence
result = app.invoke(
    {"messages": [user_message]},
    {"configurable": {"thread_id": "conversation-123"}}
)

# Resume later
continued = app.invoke(
    {"messages": []},
    {"configurable": {"thread_id": "conversation-123"}}
)
```

**Sources**: [R1][R3]

### Human-in-the-Loop

**Definition**: Built-in capability for agents to collaborate with humans by pausing execution, requesting approval, or awaiting input before proceeding [R1][R3].

**Use Cases** [R1]:
- Review agent-generated drafts before sending
- Approve high-stakes actions (financial transactions, deployments)
- Provide additional context when agent is uncertain
- Manually override agent decisions

**Sources**: [R1][R3]

---

## Core Patterns

### Pattern 1: ReAct Agent with Tool Calling

**Intent**: Build an agent that reasons about which tools to call, executes them, and iterates until task completion.

**Context**: When creating agents that need to dynamically decide which actions to take based on the current state and available tools.

**Implementation**:

```python
from langgraph.graph import StateGraph, END
from langgraph.prebuilt import ToolExecutor, ToolInvocation
from langchain_openai import ChatOpenAI
from langchain.tools import Tool
from typing import TypedDict, Annotated, Sequence
import operator

# Define state
class AgentState(TypedDict):
    messages: Annotated[Sequence, operator.add]
    agent_outcome: dict

# Define tools
def search_web(query: str) -> str:
    """Search the web for information."""
    return f"Search results for: {query}"

def calculate(expression: str) -> str:
    """Perform mathematical calculations."""
    try:
        result = eval(expression)
        return f"Result: {result}"
    except Exception as e:
        return f"Error: {e}"

tools = [
    Tool(name="Search", func=search_web, description="Search the web"),
    Tool(name="Calculate", func=calculate, description="Perform calculations")
]

tool_executor = ToolExecutor(tools)

# Define agent node
llm = ChatOpenAI(model="gpt-4", temperature=0)
llm_with_tools = llm.bind_tools(tools)

def call_agent(state: AgentState):
    """Agent decides next action."""
    messages = state["messages"]
    response = llm_with_tools.invoke(messages)
    return {"messages": [response], "agent_outcome": response}

def call_tools(state: AgentState):
    """Execute tools selected by agent."""
    last_message = state["messages"][-1]
    tool_calls = last_message.tool_calls

    tool_messages = []
    for tool_call in tool_calls:
        tool_result = tool_executor.invoke(
            ToolInvocation(
                tool=tool_call["name"],
                tool_input=tool_call["args"]
            )
        )
        tool_messages.append({
            "role": "tool",
            "content": str(tool_result),
            "tool_call_id": tool_call["id"]
        })

    return {"messages": tool_messages}

def should_continue(state: AgentState):
    """Determine if agent should continue or finish."""
    last_message = state["messages"][-1]
    if hasattr(last_message, "tool_calls") and last_message.tool_calls:
        return "continue"
    return "end"

# Build graph
workflow = StateGraph(AgentState)
workflow.add_node("agent", call_agent)
workflow.add_node("tools", call_tools)

workflow.set_entry_point("agent")
workflow.add_conditional_edges(
    "agent",
    should_continue,
    {"continue": "tools", "end": END}
)
workflow.add_edge("tools", "agent")

app = workflow.compile()

# Execute
result = app.invoke({
    "messages": [{"role": "user", "content": "What is 25 * 17?"}]
})
```

**Key Principles**:
- Use typed state dictionaries for clear data flow [R3]
- Implement conditional edges for dynamic routing [R3]
- Apply reducers (e.g., `operator.add`) for state merging [R3]
- Set max iterations to prevent infinite loops
- Log intermediate steps for debugging

**Trade-offs**:
- ✅ **Advantages**: Full control over agent behavior; clear execution flow; debuggable steps; supports complex tool sequences
- ⚠️ **Disadvantages**: More verbose than LangChain agents; requires graph construction knowledge
- 💡 **Alternatives**: Use LangChain agents for simpler use cases, LangGraph for complex orchestration

**Sources**: [R3][R4]

### Pattern 2: Multi-Agent Collaboration

**Intent**: Coordinate multiple specialized agents working together on complex tasks.

**Context**: When tasks require different expertise areas or roles (researcher, writer, reviewer, etc.).

**Implementation**:

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict, Annotated, Literal
import operator

class CollaborationState(TypedDict):
    task: str
    research_results: str
    draft_content: str
    review_feedback: str
    final_output: str
    next_agent: str

def research_agent(state: CollaborationState):
    """Agent specialized in research."""
    task = state["task"]
    # Perform research using tools
    results = f"Research findings for: {task}"
    return {
        "research_results": results,
        "next_agent": "writer"
    }

def writer_agent(state: CollaborationState):
    """Agent specialized in writing."""
    research = state["research_results"]
    # Generate content based on research
    draft = f"Draft based on: {research}"
    return {
        "draft_content": draft,
        "next_agent": "reviewer"
    }

def reviewer_agent(state: CollaborationState):
    """Agent specialized in review."""
    draft = state["draft_content"]
    # Review and provide feedback
    feedback = f"Feedback on: {draft}"

    # Determine if revision needed
    needs_revision = False  # Logic to determine this

    if needs_revision:
        return {
            "review_feedback": feedback,
            "next_agent": "writer"
        }
    else:
        return {
            "final_output": draft,
            "next_agent": "end"
        }

def route_to_next_agent(state: CollaborationState) -> Literal["researcher", "writer", "reviewer", "end"]:
    """Route to the next agent based on state."""
    next_agent = state.get("next_agent", "researcher")
    return next_agent

# Build graph
workflow = StateGraph(CollaborationState)
workflow.add_node("researcher", research_agent)
workflow.add_node("writer", writer_agent)
workflow.add_node("reviewer", reviewer_agent)

workflow.set_entry_point("researcher")
workflow.add_conditional_edges(
    "researcher",
    route_to_next_agent,
    {"writer": "writer", "end": END}
)
workflow.add_conditional_edges(
    "writer",
    route_to_next_agent,
    {"reviewer": "reviewer", "end": END}
)
workflow.add_conditional_edges(
    "reviewer",
    route_to_next_agent,
    {"writer": "writer", "end": END}
)

app = workflow.compile()

# Execute multi-agent workflow
result = app.invoke({
    "task": "Create a technical blog post about LangGraph",
    "research_results": "",
    "draft_content": "",
    "review_feedback": "",
    "final_output": "",
    "next_agent": "researcher"
})
```

**Key Principles**:
- Define clear agent roles and responsibilities [R4]
- Use state to communicate between agents [R3]
- Implement routing logic for dynamic workflows [R3]
- Add iteration limits to prevent infinite revision loops
- Consider using LangGraph Supervisor for hierarchical coordination [R1]

**Trade-offs**:
- ✅ **Advantages**: Specialized expertise; clear separation of concerns; iterative refinement; scalable to many agents
- ⚠️ **Disadvantages**: Complexity increases with agent count; coordination overhead; potential for loops
- 💡 **Alternatives**: Single general-purpose agent, sequential chains, hierarchical agent structures

**Sources**: [R1][R4]

### Pattern 3: Human-in-the-Loop Workflows

**Intent**: Build agents that pause for human review and approval before taking critical actions.

**Context**: When building agents that handle high-stakes decisions, financial transactions, or deployments requiring human oversight.

**Implementation**:

```python
from langgraph.graph import StateGraph, END
from langgraph.checkpoint.memory import MemorySaver
from typing import TypedDict

class ApprovalState(TypedDict):
    user_request: str
    agent_plan: str
    human_approved: bool
    execution_result: str

def plan_action(state: ApprovalState):
    """Agent creates execution plan."""
    request = state["user_request"]
    # Generate plan
    plan = f"Plan to execute: {request}"
    return {"agent_plan": plan}

def await_approval(state: ApprovalState):
    """Pause and wait for human approval."""
    # This is a special node that pauses execution
    # Human reviews state["agent_plan"] and updates state["human_approved"]
    return {}

def execute_action(state: ApprovalState):
    """Execute the approved action."""
    if not state.get("human_approved", False):
        return {"execution_result": "Action rejected by human"}

    plan = state["agent_plan"]
    # Execute the plan
    result = f"Executed: {plan}"
    return {"execution_result": result}

def should_execute(state: ApprovalState) -> str:
    """Check if human approved the action."""
    if state.get("human_approved", False):
        return "execute"
    return "end"

# Build graph with checkpointing
workflow = StateGraph(ApprovalState)
workflow.add_node("planner", plan_action)
workflow.add_node("approval", await_approval)
workflow.add_node("executor", execute_action)

workflow.set_entry_point("planner")
workflow.add_edge("planner", "approval")
workflow.add_conditional_edges(
    "approval",
    should_execute,
    {"execute": "executor", "end": END}
)
workflow.add_edge("executor", END)

# Compile with checkpointer for persistence
checkpointer = MemorySaver()
app = workflow.compile(
    checkpointer=checkpointer,
    interrupt_before=["approval"]  # Pause before approval node
)

# Initial execution - will pause at approval
config = {"configurable": {"thread_id": "task-123"}}
result = app.invoke({
    "user_request": "Deploy to production",
    "agent_plan": "",
    "human_approved": False,
    "execution_result": ""
}, config)

print(f"Agent plan: {result['agent_plan']}")
print("Waiting for human approval...")

# Human reviews and approves
# Resume with approval
result = app.invoke({
    "human_approved": True
}, config)

print(f"Execution result: {result['execution_result']}")
```

**Key Principles**:
- Use `interrupt_before` or `interrupt_after` to pause execution [R3]
- Implement checkpointing for state persistence [R1][R3]
- Use thread IDs to track conversations/tasks [R3]
- Provide clear context for human reviewers
- Support both approval and rejection paths

**Trade-offs**:
- ✅ **Advantages**: Human oversight for critical decisions; safety for high-stakes actions; ability to provide additional context
- ⚠️ **Disadvantages**: Increased latency; requires UI for human interaction; coordination complexity
- 💡 **Alternatives**: Confidence thresholds triggering approval, automated validation rules, audit-only mode

**Sources**: [R1][R3]

### Pattern 4: Persistent Long-Running Workflows

**Intent**: Build workflows that can pause, persist state, and resume later, potentially across different execution environments.

**Context**: When handling long-running tasks, batch processing, scheduled workflows, or multi-day processes.

**Implementation**:

```python
from langgraph.graph import StateGraph, END
from langgraph.checkpoint.postgres import PostgresSaver
from typing import TypedDict
import time

class WorkflowState(TypedDict):
    task_id: str
    input_data: list
    processed_items: list
    current_index: int
    status: str

def process_batch(state: WorkflowState):
    """Process a batch of items."""
    input_data = state["input_data"]
    current_index = state["current_index"]
    batch_size = 10

    # Process batch
    end_index = min(current_index + batch_size, len(input_data))
    batch = input_data[current_index:end_index]

    # Simulate processing
    processed = [f"processed_{item}" for item in batch]

    new_index = end_index
    is_complete = new_index >= len(input_data)

    return {
        "processed_items": state["processed_items"] + processed,
        "current_index": new_index,
        "status": "complete" if is_complete else "processing"
    }

def should_continue(state: WorkflowState) -> str:
    """Determine if processing should continue."""
    if state["status"] == "complete":
        return "end"
    return "continue"

# Build graph
workflow = StateGraph(WorkflowState)
workflow.add_node("processor", process_batch)

workflow.set_entry_point("processor")
workflow.add_conditional_edges(
    "processor",
    should_continue,
    {"continue": "processor", "end": END}
)

# Compile with PostgreSQL checkpointer
checkpointer = PostgresSaver.from_conn_string(
    "postgresql://user:pass@localhost/workflows"
)
app = workflow.compile(checkpointer=checkpointer)

# Start workflow
config = {"configurable": {"thread_id": "batch-job-456"}}
initial_state = {
    "task_id": "batch-job-456",
    "input_data": list(range(100)),  # 100 items to process
    "processed_items": [],
    "current_index": 0,
    "status": "processing"
}

# Process can be interrupted at any time
result = app.invoke(initial_state, config)

# Later (hours/days), resume from same state
# Even on a different server
resumed_result = app.invoke({}, config)
print(f"Processed {len(resumed_result['processed_items'])} items")
```

**Key Principles**:
- Use external checkpointers (PostgreSQL, Redis) for production [R3]
- Design state to be serializable [R3]
- Implement idempotent processing to handle retries [R3]
- Use thread IDs for workflow tracking [R3]
- Add monitoring and alerting for long-running tasks

**Trade-offs**:
- ✅ **Advantages**: Resilient to failures; can pause/resume; distributed execution; durable state
- ⚠️ **Disadvantages**: Requires external storage; increased complexity; potential consistency issues
- 💡 **Alternatives**: Traditional job queues (Celery, RQ), workflow engines (Temporal, Airflow)

**Sources**: [R1][R3]

### Pattern 5: Streaming Real-Time Updates

**Intent**: Provide real-time feedback to users by streaming agent reasoning, actions, and results as they happen.

**Context**: When building interactive applications where users need to see agent progress, not just final results.

**Implementation**:

```python
from langgraph.graph import StateGraph, END
from typing import TypedDict
import asyncio

class StreamState(TypedDict):
    query: str
    thoughts: list
    actions: list
    result: str

async def thinking_node(state: StreamState):
    """Agent reasoning step."""
    thought = f"Analyzing query: {state['query']}"
    # Yield intermediate updates
    yield {"thoughts": [thought]}
    await asyncio.sleep(0.5)  # Simulate thinking

async def action_node(state: StreamState):
    """Agent action step."""
    action = "Searching knowledge base"
    yield {"actions": [action]}
    await asyncio.sleep(0.5)  # Simulate action

    # Final result
    result = f"Answer to: {state['query']}"
    yield {"result": result}

# Build graph
workflow = StateGraph(StreamState)
workflow.add_node("think", thinking_node)
workflow.add_node("act", action_node)

workflow.set_entry_point("think")
workflow.add_edge("think", "act")
workflow.add_edge("act", END)

app = workflow.compile()

# Stream execution
async def run_with_streaming():
    async for event in app.astream({
        "query": "What is LangGraph?",
        "thoughts": [],
        "actions": [],
        "result": ""
    }):
        print(f"Event: {event}")
        # Send to frontend via WebSocket, SSE, etc.

# Run
asyncio.run(run_with_streaming())
```

**Key Principles**:
- Use `astream()` for async streaming [R1][R3]
- Stream intermediate updates, not just final results [R1]
- Implement token-level streaming for LLM outputs [R1]
- Use WebSockets or Server-Sent Events for frontend delivery
- Handle backpressure in streaming pipelines

**Trade-offs**:
- ✅ **Advantages**: Better user experience; perceived lower latency; transparency into agent reasoning
- ⚠️ **Disadvantages**: Implementation complexity; requires async programming; frontend streaming support needed
- 💡 **Alternatives**: Polling for updates, progress callbacks, batch updates

**Sources**: [R1][R3]

---

## Decision Checklist

### Use LangGraph when

- ✅ Building multi-agent systems with complex coordination
- ✅ Need conditional branching based on agent decisions or tool outputs
- ✅ Require human-in-the-loop approval for critical actions
- ✅ Building long-running workflows that need pause/resume capability
- ✅ Need state persistence across sessions or environments
- ✅ Want time-travel debugging to explore alternative execution paths
- ✅ Implementing parallel agent execution
- ✅ Building hierarchical agent structures (supervisors, workers)
- ✅ Need fine-grained control over agent orchestration
- ✅ Require streaming of intermediate results

### Use LangChain instead when

- ✅ Building simple sequential chains without complex branching
- ✅ Prototyping quickly without advanced orchestration needs
- ✅ Working on straightforward RAG applications
- ✅ Need basic conversational agents with memory
- ✅ Team is new to agent development (easier learning curve)

### Consider alternatives when

- ❌ Need orchestration in languages other than Python/JavaScript
- ❌ Working with traditional workflow engines (Airflow, Prefect more suitable)
- ❌ Building simple request-response APIs without stateful workflows
- ❌ Team lacks graph/state machine experience

**Framework Comparison**:

| Capability | LangGraph | LangChain | Temporal | Airflow |
|------------|-----------|-----------|----------|---------|
| **Multi-Agent** | ✅ Native | ⚠️ Limited | ❌ Not designed | ❌ Not designed |
| **State Management** | ✅ Built-in | ⚠️ Memory only | ✅ Durable | ⚠️ XCom |
| **Human-in-Loop** | ✅ Native | ❌ Manual | ✅ Activities | ⚠️ Manual |
| **Time-Travel** | ✅ Native | ❌ No | ✅ Replay | ⚠️ Limited |
| **LLM Focus** | ✅ Yes | ✅ Yes | ❌ General | ❌ General |
| **Learning Curve** | ⚠️ Steep | ✅ Gentle | ⚠️ Steep | ⚠️ Moderate |

---

## Anti-patterns / Pitfalls

### Anti-pattern 1: Overusing Graphs for Simple Flows

**Problem**: Using LangGraph for simple sequential workflows that don't require branching or state management.

**Why it's problematic**: Adds unnecessary complexity, overhead, and learning curve when simpler approaches would suffice.

**Example of anti-pattern**:
```python
# Overcomplicated for simple sequential flow
workflow = StateGraph(State)
workflow.add_node("step1", func1)
workflow.add_node("step2", func2)
workflow.add_node("step3", func3)
workflow.add_edge("step1", "step2")
workflow.add_edge("step2", "step3")
workflow.add_edge("step3", END)
```

**Better approach**:
```python
# Use LangChain for simple sequences
from langchain.chains import LLMChain

chain = LLMChain(llm=llm, prompt=prompt)
result = chain.run(input_data)
```

**When to use the pattern**: Reserve LangGraph for workflows with conditional logic, parallel execution, or state persistence requirements.

**Sources**: Best practices from production usage

### Anti-pattern 2: Circular Dependencies Without Exit Conditions

**Problem**: Creating graph edges that can loop indefinitely without proper exit conditions.

**Why it's problematic**: Leads to infinite loops, wasted tokens, and hung workflows.

**Example of anti-pattern**:
```python
# No exit condition
workflow.add_edge("agent", "tools")
workflow.add_edge("tools", "agent")  # Infinite loop!
```

**Better approach**:
```python
# Add conditional edge with exit condition
def should_continue(state):
    if state["iterations"] > 5:
        return "end"
    if state["task_complete"]:
        return "end"
    return "continue"

workflow.add_conditional_edges(
    "agent",
    should_continue,
    {"continue": "tools", "end": END}
)
workflow.add_edge("tools", "agent")
```

**When to use the pattern**: Always implement iteration limits and task completion checks for cyclic graphs.

**Sources**: [R3]

### Anti-pattern 3: Ignoring State Schema Design

**Problem**: Using untyped dictionaries for state without clear schema definition.

**Why it's problematic**: Makes debugging difficult, increases errors, and reduces code maintainability.

**Example of anti-pattern**:
```python
# Untyped state
def node_func(state):
    # What fields exist in state? No one knows!
    return {"some_field": "value"}
```

**Better approach**:
```python
# Typed state with clear schema
from typing import TypedDict, Annotated
import operator

class WorkflowState(TypedDict):
    """Clear schema for workflow state."""
    messages: Annotated[list, operator.add]  # Append messages
    user_input: str
    agent_response: str
    iteration_count: int
    status: Literal["processing", "complete", "failed"]

def node_func(state: WorkflowState) -> WorkflowState:
    # Type checking and autocompletion work!
    return {"status": "processing"}
```

**When to use the pattern**: Always define typed state schemas for production workflows.

**Sources**: [R3]

### Anti-pattern 4: Not Handling Checkpoint Failures

**Problem**: Assuming checkpointing always succeeds without implementing error handling or fallback strategies.

**Why it's problematic**: Database failures or network issues can cause data loss or workflow interruption.

**Example of anti-pattern**:
```python
# No error handling
checkpointer = PostgresSaver.from_conn_string(conn_string)
app = workflow.compile(checkpointer=checkpointer)
result = app.invoke(state, config)  # What if DB is down?
```

**Better approach**:
```python
# Implement fallback and retry logic
from langgraph.checkpoint.memory import MemorySaver

try:
    checkpointer = PostgresSaver.from_conn_string(conn_string)
except Exception as e:
    logger.error(f"PostgreSQL unavailable: {e}")
    # Fallback to in-memory
    checkpointer = MemorySaver()

# Add retry logic
from tenacity import retry, stop_after_attempt, wait_exponential

@retry(
    stop=stop_after_attempt(3),
    wait=wait_exponential(multiplier=1, min=2, max=10)
)
def invoke_with_retry(app, state, config):
    return app.invoke(state, config)

try:
    result = invoke_with_retry(app, state, config)
except Exception as e:
    logger.error(f"Workflow failed after retries: {e}")
    # Handle gracefully
```

**When to use the pattern**: Always implement error handling for production checkpointing.

**Sources**: Best practices from production usage

---

## Evaluation

### Success Metrics

**Workflow-Level Metrics**:
- **Task Completion Rate**: Percentage of workflows completing successfully
- **Average Execution Time**: Time from start to completion (including pauses)
- **Node Success Rate**: Success rate per individual node
- **Branching Accuracy**: Correctness of conditional routing decisions
- **Human Approval Rate**: Percentage of tasks approved vs. rejected

**Agent Coordination Metrics**:
- **Agent Utilization**: How efficiently agents are used
- **Handoff Success Rate**: Successful transitions between agents
- **Iteration Count**: Average iterations before task completion
- **Loop Detection**: Frequency of circular execution patterns

**State Management Metrics**:
- **Checkpoint Success Rate**: Successful state persistence operations
- **State Size**: Size of serialized state objects
- **Resume Success Rate**: Successful workflow resumptions
- **Time-Travel Usage**: How often debugging/rollback features are used

**Cost Metrics**:
- **Token Usage Per Workflow**: Total tokens consumed
- **Database Operations**: Checkpoint read/write frequency
- **Execution Duration**: Billable compute time
- **Storage Costs**: State persistence storage costs

### Evaluation Strategies

**1. LangGraph Studio** [R1]:
```python
# Visualize and debug workflows
# LangGraph Studio provides:
# - Visual graph representation
# - Step-by-step execution replay
# - State inspection at each node
# - Time-travel debugging UI
```

**2. Workflow Testing**:
```python
# Test individual nodes
def test_research_agent():
    state = {
        "task": "Research AI safety",
        "research_results": "",
        "next_agent": ""
    }
    result = research_agent(state)
    assert result["research_results"] != ""
    assert result["next_agent"] == "writer"

# Test routing logic
def test_should_continue():
    state = {"iterations": 6, "task_complete": False}
    assert should_continue(state) == "end"

# Integration tests
def test_full_workflow():
    app = workflow.compile()
    result = app.invoke(initial_state)
    assert result["status"] == "complete"
```

**3. State Validation**:
```python
# Validate state schema at runtime
from pydantic import BaseModel, ValidationError

class ValidatedState(BaseModel):
    messages: list
    status: Literal["processing", "complete"]
    iteration_count: int

def validate_node(state):
    try:
        ValidatedState(**state)
    except ValidationError as e:
        logger.error(f"Invalid state: {e}")
        raise
```

**4. A/B Testing Workflows**:
```python
# Test different orchestration strategies
import random

def get_workflow(version="A"):
    if version == "A":
        # Sequential workflow
        return sequential_workflow
    else:
        # Parallel workflow
        return parallel_workflow

version = "A" if random.random() < 0.5 else "B"
app = get_workflow(version).compile()
result = app.invoke(state, {"metadata": {"version": version}})
```

### Performance Benchmarks

**Typical Execution Times** (GPT-4, production workloads):
- Simple 3-node workflow: 2-5 seconds
- Multi-agent workflow (5 agents): 10-30 seconds
- Workflow with human-in-loop: Hours to days (paused)
- Long-running batch processing: Minutes to hours

**Optimization Strategies**:
- Parallelize independent nodes using `ParallelNode`
- Implement caching for expensive operations
- Use streaming for real-time feedback [R1]
- Choose appropriate checkpointer backend for scale
- Optimize state size (remove unnecessary data)
- Implement conditional execution to skip unnecessary nodes

**Sources**: [R1][R3] and production usage patterns

---

## Recent Updates (2025)

### LangGraph Platform General Availability [R6]

The LangGraph Platform is now generally available, bringing enterprise-grade agent deployment capabilities.

**Key Features**:
- **1-Click Agent Deployment**: Deploy agents to production with minimal configuration
- **Memory APIs**: Built-in APIs for managing agent memory across sessions
- **Human-in-the-Loop Controls**: Native support for human approval workflows
- **LangGraph Studio Integration**: Seamless integration with visual debugging tools

**Benefits**:
- Simplified deployment and scaling
- Production-ready infrastructure
- Built-in monitoring and observability

**Sources**: [R6]

### LangGraph Studio v2 [R6]

LangGraph Studio can now run locally without a desktop app, functioning as a complete agent IDE.

**New Capabilities**:
- **Local execution**: Run Studio directly in your development environment
- **Trace investigation**: Pull down and analyze execution traces
- **Dataset management**: Add examples to datasets for evaluations
- **Prompt editing**: Update prompts directly in the UI

**Benefits**:
- Faster development cycles
- Better debugging experience
- Integrated testing workflows

**Sources**: [R6]

### Workflow Enhancements (June 2025) [R7]

**Node Caching**:
- Skip redundant computation for improved performance
- Automatically reuse results from previous executions
- Configurable cache invalidation strategies

**Deferred Nodes**:
- Delay execution until all upstream paths complete
- Perfect for map-reduce patterns
- Enables collaborative agent workflows

**Pre/Post Model Hooks**:
- Add custom logic before or after model calls
- Control context bloat
- Insert guardrails and validation

**Built-in Provider Tools**:
- Web search integration
- RemoteMCP support
- Simplified external tool integration

**Sources**: [R7]

### Type Safety and Developer Experience [R7]

**LangGraph JS v0.3 Updates**:
- **Type-safe streaming**: `.stream()` method now fully type-safe
- **Simplified graph building**: `.addNode()` and `.addSequence()` methods for `StateGraph`
- **Improved error handling**: Interrupts now returned in `.invoke()` and "values" stream modes

**Sources**: [R7]

---

## Update Log

### 2025-10-24
- **Added**: Recent 2025 updates: LangGraph Platform GA, Studio v2, workflow enhancements (node caching, deferred nodes, pre/post hooks), and type safety improvements
- **Updated**: Sources with LangGraph Release Week and workflow update references
- **Initial version**: Created comprehensive LangGraph documentation
- **Added**: Canonical definitions for LangGraph, StateGraph, checkpoints, and human-in-the-loop
- **Added**: Core patterns for ReAct agents, multi-agent collaboration, human-in-loop workflows, persistent workflows, and streaming
- **Added**: Decision checklist and framework comparison with LangChain, Temporal, and Airflow
- **Added**: Anti-patterns covering graph overuse, circular dependencies, state schema design, and checkpoint failures
- **Added**: Evaluation strategies with LangGraph Studio, testing approaches, and performance benchmarks

---

## See Also

### Related Topics
- **[LangChain](./langchain.md)** - Foundational framework for LLM applications (prerequisite for LangGraph)
- **[AGENTS.md](../../topics/AGENTS.md)** - General agent operational documentation and conventions
- **[PLANS.md](../../topics/PLANS.md)** - ExecPlans workflow for complex multi-session tasks
- **[Context Engineering](../../engineering/context.md)** - Best practices for context management in agents
- **[Prompt Engineering](../../engineering/prompt.md)** - Prompt design patterns for agent instructions

### Platform Comparisons
- **[OpenAI Agents SDK](../openai/agents-sdk.md)** - OpenAI's native agent framework
- **[Claude Agent SDK](../anthropic/claude-agent-sdk.md)** - SDK for building custom agents with Claude
- **[Google A2A Protocol](../google/a2a.md)** - Agent-to-agent communication protocol

---

## References

- [R1] LangChain. "LangGraph Official Website." https://www.langchain.com/langgraph (accessed 2025-10-24)
- [R2] LangChain AI. "LangGraph GitHub Repository." https://github.com/langchain-ai/langgraph (accessed 2025-10-24)
- [R3] LangChain AI. "LangGraph Documentation." https://langchain-ai.github.io/langgraph/ (accessed 2025-10-24)
- [R4] Latenode. "LangGraph AI Framework 2025: Complete Architecture Guide + Multi-Agent Orchestration Analysis." https://latenode.com/blog/langgraph-ai-framework-2025-complete-architecture-guide-multi-agent-orchestration-analysis (accessed 2025-10-24)
- [R5] AWS. "LangChain and LangGraph - AWS Prescriptive Guidance." https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-frameworks/langchain-langgraph.html (accessed 2025-10-24)

---

**Document ID**: `platforms/langchain/langgraph`
**Last Updated**: 2025-10-24
