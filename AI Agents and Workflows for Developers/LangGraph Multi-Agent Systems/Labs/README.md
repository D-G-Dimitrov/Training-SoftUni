# LangGraph Multi-Agent Systems — Labs

This folder contains a series of Jupyter notebooks that teach you how to build **stateful, graph-based AI workflows** using [LangGraph](https://github.com/langchain-ai/langgraph). Each notebook introduces a new concept and builds on the previous one, so the recommended reading order below matters.

---

## Recommended Learning Order

Work through the notebooks in the order listed below. Each entry explains what concept it introduces and what you will learn from it.

---

### 1. Simple Graphs (Part 1).ipynb

**Concepts:** State, nodes, linear graphs, reducers

Your starting point. Introduces the core LangGraph building blocks:

- **State** (`TypedDict`) — the shared data dictionary that flows through the entire graph
- **Nodes** — Python functions that receive the state and return partial updates
- **Edges** — connections that define execution order
- **Reducers** (`Annotated[list, operator.add]`) — how LangGraph merges state updates from multiple nodes

The example builds a simple text-processing pipeline: normalize → tokenize → count words → report.

---

### 2. Simple Graphs (Part 2).ipynb

**Concepts:** Conditional edges, routing, branching

Extends Part 1 by introducing **conditional (branching) edges** — the ability to route execution to different nodes based on the current state. You will learn:

- Routing functions that return the name of the next node
- `add_conditional_edges()` — the LangGraph API for branching logic
- How to build a support-ticket triage system with categories (billing, technical, spam) and escalation

---

### 3. AI Agent (Part 1).ipynb

**Concepts:** LLM tools, the ReAct loop, `AgentState`, `ToolNode`

Introduces the most important AI agent pattern — **ReAct** (Reason + Act). You will learn:

- How to define Python functions as **LangChain tools** using `@tool`
- How to bind tools to an OpenAI model so it can decide when to call them
- How to build the **model ↔ tools feedback loop** in LangGraph: the model calls a tool, gets a result, then reasons again — looping until it has a final answer
- How `AgentState` works as a message list

Requires an OpenAI API key.

---

### 4. AI Agent (Part 2).ipynb

**Concepts:** Custom state, lifecycle hooks, observability

Extends Part 1 by wrapping the model with **lifecycle hook nodes** for logging and observability. You will learn:

- How to extend `AgentState` with custom fields (`model_calls` counter)
- How to add `on_start`, `on_end`, `before_model`, and `after_model` nodes around the core agent loop
- How this pattern is useful for debugging, monitoring, and adding cross-cutting concerns without changing the core logic

---

### 5. Checkpointers.ipynb

**Concepts:** Persistent memory, threads, conversation history, state snapshots

Extends AI Agent Part 2 by adding a **checkpointer** — the mechanism that gives the graph persistent memory across multiple invocations. You will learn:

- What a checkpointer is and why it's needed (`InMemorySaver`)
- The concept of **thread IDs** — how the same graph can maintain separate conversation histories for different sessions
- How to send follow-up messages that the model can understand in context (e.g. "And what about London?" after asking about Tokyo)
- How to inspect saved checkpoints and walk through the full state history with `explore_checkpoints()` and `explore_state_history()`

---

### 6. Stores.ipynb

**Concepts:** Cross-thread storage, context, `ToolRuntime`, namespaced key-value store

Extends Checkpointers by introducing two new ideas that work alongside the checkpointer:

- **Context** — read-only per-invocation metadata (e.g. `user_id`) passed to `graph.invoke(..., context=...)` and injected into tools via `ToolRuntime`. Unlike state, it never changes as nodes run.
- **`InMemoryStore`** — a global key-value store shared **across all threads**. Checkpoints are scoped to one conversation; the store is not. Use it for data that should persist beyond a single session (user preferences, activity logs, etc.)
- **Namespaces** — store entries are organised by tuple-based namespaces like `("users", "troeff_1", "recent_activity")`
- **`ToolRuntime`** — a special parameter tools declare to receive the context and store automatically at runtime (LangGraph injects it; you never pass it manually)

---

### 7. Parallel Execution (Part 1).ipynb

**Concepts:** Fan-out, fan-in, parallel node execution, custom reducers

Introduces **parallel execution** — running multiple nodes simultaneously. You will learn:

- How to create a **fan-out** (multiple edges from the same source node all firing at once)
- How to create a **fan-in** (a node that waits for all parallel predecessors to finish before running)
- Why custom reducers are essential when parallel nodes write to the same state field
- How to build a parallel research pipeline (Wikipedia + Live News + ArXiv → merge)

---

### 8. Parallel Execution (Part 2).ipynb

**Concepts:** Verification step, combining parallel execution with post-processing

Extends Part 1 by adding a **verifier node** that runs after some of the parallel branches. You will learn:

- How to route only certain parallel branches through additional processing
- How `operator.or_` merges sets and `operator.add` accumulates lists across parallel writes
- How to build a `merge` node that guards against running before all verifications are done
- How verification labels (`(verified)` / `(blocked)`) appear in the final summary

---

### 9. Interrupts.ipynb

**Concepts:** Human-in-the-loop, `interrupt()`, `Command(resume=...)`, pausing and resuming graphs

The most advanced notebook. Replaces the automated verifier from Part 2 with **real human approval**. You will learn:

- How `interrupt()` pauses graph execution mid-node and surfaces a prompt to the user
- How `Command(resume=True/False)` resumes the graph with the human's decision
- Why a checkpointer is *required* for interrupts to work (state must be persisted between pauses)
- How to iterate through multiple pending approvals one at a time
- How interrupted state appears in checkpoints and state history

---

## Quick Reference

| # | Notebook | Key concepts |
|---|---|---|
| 1 | Simple Graphs (Part 1) | State, nodes, linear graphs, reducers |
| 2 | Simple Graphs (Part 2) | Conditional edges, branching, routing |
| 3 | AI Agent (Part 1) | LLM tools, ReAct loop, `ToolNode` |
| 4 | AI Agent (Part 2) | Custom state, lifecycle hooks |
| 5 | Checkpointers | Persistent memory, threads, conversation history |
| 6 | Stores | Cross-thread storage, context, `ToolRuntime` |
| 7 | Parallel Execution (Part 1) | Fan-out/fan-in, parallel nodes |
| 8 | Parallel Execution (Part 2) | Verification step, combining parallel + post-processing |
| 9 | Interrupts | Human-in-the-loop, pausing/resuming graphs |

---

## Key Concepts Glossary

| Term | What it means |
|---|---|
| **State** | A TypedDict dictionary shared across all nodes in the graph |
| **Node** | A Python function that reads state and returns a partial update dict |
| **Edge** | A directed connection between two nodes defining execution order |
| **Conditional edge** | An edge that uses a routing function to pick the next node dynamically |
| **Reducer** | A function (e.g. `operator.add`) that tells LangGraph how to merge state updates from multiple nodes |
| **Checkpointer** | A backend (e.g. `InMemorySaver`) that saves state snapshots after each step |
| **Thread ID** | A unique identifier for a conversation session; enables separate memory per user |
| **Fan-out** | Multiple edges from the same source node — causes parallel execution |
| **Fan-in** | A node that waits for all its predecessors to finish before running |
| **ReAct loop** | Model → (call tool?) → tools → model → repeat until done |
| **Interrupt** | Pauses the graph mid-execution to wait for human input |
| **`Command(resume=...)`** | Resumes a paused graph, passing the human's decision back to the interrupted node |
| **Store** | A global key-value store (`InMemoryStore`) shared across all threads — unlike checkpoints which are per-thread |
| **Namespace** | A tuple used to organise store entries, e.g. `("users", "user_id", "recent_activity")` |
| **Context** | Read-only per-invocation metadata passed to `graph.invoke(..., context=...)` — not part of the mutable state |
| **`ToolRuntime`** | A parameter tools can declare to receive the context and store automatically at runtime |
