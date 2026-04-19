# LangChain Agents & Tools — Labs

This folder contains hands-on Jupyter notebooks covering the **LangChain Agents & Tools** module. The labs progress from foundational LLM integration all the way to building autonomous agents with RAG pipelines and advanced middleware patterns.

---

## Recommended Learning Order

Work through the notebooks in the order below. Each builds on concepts introduced in the previous ones.

### Stage 1 — Foundations: Models & Messages

Start here if you are new to LangChain or LLM APIs in Python.

| # | Notebook | What You'll Learn |
|---|----------|-------------------|
| 1 | [Models & Messages (OpenAI)](./Models%20%26%20Messages%20(OpenAI).ipynb) | Connect to OpenAI models, understand message types (`HumanMessage`, `AIMessage`, `SystemMessage`), track token usage and prompt caching |
| 2 | [Models & Messages (Anthropic)](./Models%20%26%20Messages%20(Anthropic).ipynb) | Same concepts but with Anthropic Claude — multi-modal content (text + images), extended-thinking models, prompt caching |

> **Tip:** Pick one to start with (OpenAI is more common), then skim the other to see the differences. The LangChain interface is nearly identical.

---

### Stage 2 — Building Pipelines: LCEL

| # | Notebook | What You'll Learn |
|---|----------|-------------------|
| 3 | [LCEL](./LCEL.ipynb) | LangChain Expression Language — how to compose chains using the `\|` pipe operator, `Runnable` interface, `RunnablePassthrough`, and build reusable pipelines |

---

### Stage 3 — Tool Calling

| # | Notebook | What You'll Learn |
|---|----------|-------------------|
| 4 | [Tool Calling](./Tool%20Calling.ipynb) | Define tools with the `@tool` decorator, let the model decide when to call them, handle `ToolMessage` responses, manage conversation state |
| 5 | [Forced Tool Calling](./Forced%20Tool%20Calling.ipynb) | Override the model's judgment with `tool_choice` to force specific tool execution — useful for structured extraction and guaranteed outputs |

---

### Stage 4 — Agents

| # | Notebook | What You'll Learn |
|---|----------|-------------------|
| 6 | [Agents](./Agents.ipynb) | Build autonomous agents that reason over tools in a loop (`create_agent`), handle multi-turn conversations, and decide which tools to invoke |

---

### Stage 5 — Document Loaders & RAG

These notebooks teach you how to feed external knowledge into your agents.

| # | Notebook | What You'll Learn |
|---|----------|-------------------|
| 7 | [Loaders, Splitters & Retrievers](./Loaders%2C%20Splitters%20%26%20Retrievers.ipynb) | Load documents (`DirectoryLoader`, `TextLoader`), split them with `RecursiveCharacterTextSplitter`, store in a Chroma vector DB, and build a full RAG retrieval pipeline |
| 8 | [GitHub Loader](./GitHub%20Loader.ipynb) | Load files and issues from a GitHub repository (`GithubFileLoader`, `GitHubIssuesLoader`) into your RAG pipeline — requires a GitHub personal access token |
| 9 | [Notion Loader](./Notion%20Loader.ipynb) | Load pages from a Notion database (`NotionDBLoader`) — requires a Notion integration token and database ID |

---

### Stage 6 — Middlewares (Advanced)

| # | Notebook | What You'll Learn |
|---|----------|-------------------|
| 10 | [Middlewares (Part 1)](./Middlewares%20(Part%201).ipynb) | Agent middleware architecture — lifecycle hooks (`@before_agent`, `@after_agent`, `@before_model`, `@after_model`), request/response interception, PII redaction middleware |
| 11 | [Middlewares (Part 2)](./Middlewares%20(Part%202).ipynb) | Advanced middleware — `@wrap_model_call` for full LLM interception, tool call limits to prevent infinite loops, integrating HuggingFace NER models into agent pipelines |

---

## Quick Reference

| Notebook | Difficulty | Key Concepts |
|----------|------------|--------------|
| Models & Messages (OpenAI) | Beginner | `ChatOpenAI`, message types, token tracking |
| Models & Messages (Anthropic) | Beginner | `ChatAnthropic`, multi-modal, extended thinking |
| LCEL | Intermediate | Chains, `Runnable`, pipelines |
| Tool Calling | Intermediate | `@tool`, `ToolMessage`, conversation state |
| Forced Tool Calling | Intermediate | `tool_choice`, structured outputs |
| Agents | Intermediate | `create_agent`, reasoning loops |
| Loaders, Splitters & Retrievers | Intermediate–Advanced | RAG, Chroma, `WikipediaRetriever` |
| GitHub Loader | Beginner–Intermediate | `GithubFileLoader`, PAT authentication |
| Notion Loader | Beginner–Intermediate | `NotionDBLoader`, Notion API |
| Middlewares (Part 1) | Advanced | Lifecycle hooks, PIIMiddleware |
| Middlewares (Part 2) | Advanced | `@wrap_model_call`, HuggingFace NER |

---

## Prerequisites

- Python 3.10+
- Basic Python knowledge (functions, classes, decorators)
- API keys: OpenAI and/or Anthropic (set as environment variables)
- Optional for later stages: GitHub PAT, Notion integration token
