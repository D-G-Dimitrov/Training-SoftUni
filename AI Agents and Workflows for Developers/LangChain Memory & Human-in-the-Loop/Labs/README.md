# LangChain Memory & Human-in-the-Loop — Labs

This folder contains hands-on notebooks covering how to give LangChain/LangGraph agents **memory** and **human oversight**. Below is the recommended reading order, starting from the simplest concepts and building up to a full real-world example.

---

## Recommended Learning Order

### 1. [`Reusable Prompts.ipynb`](./Reusable%20Prompts.ipynb)

**Start here.** This notebook introduces the foundation of structured LLM communication — **prompt templates**.

- **What you'll learn:** How to define reusable prompt templates with placeholders, and how to construct multi-message chat prompts (system + history + user turns).
- **Key classes:** `PromptTemplate`, `ChatPromptTemplate`, `MessagesPlaceholder`, `HumanMessagePromptTemplate`, `AIMessagePromptTemplate`
- **No agent or API key needed** — great for getting familiar with the concept before touching live models.
- **Why this first:** Every agent and chain uses prompt templates internally. Understanding them makes everything else much clearer.

---

### 2. [`Middlewares (Part 3).ipynb`](./Middlewares%20%28Part%203%29.ipynb)

**Introduces the concept of extending agent state through middlewares.**

- **What you'll learn:** How to hook custom logic into the agent's execution loop using `@after_model` middleware. Specifically: tracking token usage and counting model calls. Also introduces `TodoListMiddleware` for short-term multi-step planning within a single invocation.
- **Key concepts:** `AgentState` subclassing, `Annotated[int, operator.add]` (accumulating values across steps), `@after_model` decorator, `TodoListMiddleware`
- **Memory type covered:** Short-term memory **within a single `agent.invoke()` call** (no persistence between calls).
- **Why this second:** Sets the stage for Checkpointers (Part 3), which combines what you learn here with persistent memory.

---

### 3. [`Checkpointers (Part 1).ipynb`](./Checkpointers%20%28Part%201%29.ipynb)

**Introduces checkpointers — the core mechanism for multi-turn conversation memory.**

- **What you'll learn:** How to give an agent memory across multiple messages in the same conversation thread using `InMemorySaver`. How `thread_id` links messages together. How to inspect raw checkpoint data.
- **Key concepts:** `InMemorySaver`, `thread_id`, `RunnableConfig`, `explore_checkpoints()`
- **Memory type covered:** Short-term thread memory **across multiple `agent.invoke()` calls** (lost when the process ends).
- **Why this third:** The simplest form of multi-turn memory. In-memory, no setup required.

---

### 4. [`Checkpointers (Part 2).ipynb`](./Checkpointers%20%28Part%202%29.ipynb)

**Replaces the RAM-based checkpointer with a real SQLite database.**

- **What you'll learn:** How to use `SqliteSaver` to persist agent state to a `.db` file on disk. How to inspect checkpoint tables with `pandas`. The difference between the `checkpoints` table and the `writes` table.
- **Key concepts:** `SqliteSaver`, `sqlite3`, `pandas` DataFrame, LangGraph's internal DB schema
- **Memory type covered:** Short-term thread memory that **survives process restarts** (stored in a file).
- **Why this fourth:** A direct upgrade from Part 1 — same agent code, different storage backend. Shows you can swap checkpointers without changing agent logic.

---

### 5. [`Checkpointers (Part 3).ipynb`](./Checkpointers%20%28Part%203%29.ipynb)

**Combines the custom middlewares from Middlewares (Part 3) with a checkpointer.**

- **What you'll learn:** How checkpointers persist not just message history but also custom state fields defined by middlewares (token counts, call counts, TODO list). Also includes a corrected (bug-fixed) version of the `track_usage` middleware.
- **Key concepts:** All concepts from Middlewares (Part 3) + Checkpointers (Part 1), combined. The `thread_id` must always be a string.
- **Memory type covered:** Persistent state including custom middleware fields.
- **Why this fifth:** Brings together the two previous tracks (middlewares + checkpointers). Required knowledge before the final two notebooks.

---

### 6. [`Stores (Part 1).ipynb`](./Stores%20%28Part%201%29.ipynb)

**Introduces long-term cross-conversation memory using Stores.**

- **What you'll learn:** The difference between a checkpointer (thread-scoped) and a store (user-scoped). How to use `InMemoryStore` to persist facts about a user that survive across different conversation threads. How to give tools access to the store via `ToolRuntime` and `AgentContext`.
- **Key concepts:** `InMemoryStore`, `ToolRuntime[AgentContext]`, namespace tuples, `store.put()`, `store.search()`, `TypedDict`
- **Memory type covered:** Long-term **cross-thread** memory (facts about a user persist across all conversations).
- **Why this sixth:** This is the most powerful memory concept. Only makes sense after understanding checkpointers and the difference between thread-scoped vs user-scoped memory.

---

### 7. [`Human-in-the-Loop.ipynb`](./Human-in-the-Loop.ipynb)

**Introduces human approval before irreversible tool calls.**

- **What you'll learn:** How to pause an agent before it executes a specific tool, review the pending action, and either approve or reject it. How to resume a paused agent using `Command(resume=...)`.
- **Key concepts:** `HumanInTheLoopMiddleware`, `interrupt_on`, `__interrupt__`, `Command`, `Interrupt`, thread continuity across pause/resume
- **Memory type covered:** Uses a checkpointer to save state while the agent is paused waiting for human input.
- **Why this seventh:** A new concept (control flow, not memory). Best understood once you're comfortable with checkpointers, since HITL depends on them.

---

### 8. [`Observability.ipynb`](./Observability.ipynb)

**Puts it all together: HITL + LangSmith tracing + programmatic feedback.**

- **What you'll learn:** How to enable automatic LangSmith tracing with just a few environment variables. How to assign a `run_id` to each `agent.invoke()` call. How to submit ratings/feedback to LangSmith programmatically. A realistic multi-tool booking scenario with two simultaneous approval requests.
- **Key concepts:** LangSmith (`LANGSMITH_TRACING`, `LANGSMITH_API_KEY`, `LANGSMITH_PROJECT`), `uuid.uuid4()` as `run_id`, `LangSmithClient.create_feedback()`
- **Memory type covered:** Combines checkpointer (for HITL) with LangSmith as an external observability store.
- **Why this last:** The capstone. Requires understanding of all previous notebooks. Introduces monitoring/evaluation, which is the final step in going from a working agent to a production-grade one.

---

## Concept Map

```
Prompt Templates  →  How to structure instructions
        ↓
Middlewares (Part 3)  →  How to extend agent state + short-term planning memory
        ↓
Checkpointers (Part 1)  →  Multi-turn memory in RAM
        ↓
Checkpointers (Part 2)  →  Multi-turn memory in a real database
        ↓
Checkpointers (Part 3)  →  Checkpointers + Middlewares combined
        ↓
Stores (Part 1)  →  Long-term cross-conversation user memory
        ↓
Human-in-the-Loop  →  Human approval before irreversible actions
        ↓
Observability  →  Tracing, monitoring and evaluating agents in production
```

---

## Memory Types at a Glance

| Memory Type | Notebook | Scope | Persists? |
|---|---|---|---|
| Short-term (single call) | Middlewares (Part 3) | One `agent.invoke()` | No |
| Short-term (per thread, RAM) | Checkpointers (Part 1) | One `thread_id` | No (RAM) |
| Short-term (per thread, disk) | Checkpointers (Part 2) | One `thread_id` | Yes (SQLite file) |
| Short-term (per thread) + custom state | Checkpointers (Part 3) | One `thread_id` | No (RAM) |
| Long-term (per user, cross-thread) | Stores (Part 1) | One `user_id` | No (RAM) |
| Paused state (HITL) | Human-in-the-Loop | One `thread_id` | No (RAM) |
| External traces (LangSmith) | Observability | Cloud | Yes (LangSmith) |
