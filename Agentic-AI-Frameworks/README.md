# Agentic AI Frameworks · Notes

> How to choose an agent framework for workflows, multi-agent systems and production.

## 📺 Agentic AI Frameworks Explained: Workflows, Multi-Agent & Production
[Video (IBM Technology, ~12 min)](https://www.youtube.com/watch?v=ZVPlLaehjLk) · How to choose between agent frameworks based on your system design and real-world needs.

### 🧠 Key takeaways: 5 types of agentic systems

The video groups agentic systems into five types. For each type, the frameworks are listed in order of increasing complexity.

| # | Type of system | Frameworks (simpler → more complex) |
|---|---|---|
| 1 | **Linear workflow** | LangChain → LlamaIndex → LangGraph |
| 2 | **Autonomous agent** | AutoGen → BabyAGI → CrewAI |
| 3 | **Role-based agents** | CrewAI → AutoGen → ChatDev |
| 4 | **Production orchestration** | Microsoft Agent Framework (Semantic Kernel + AutoGen) → LangGraph |
| 5 | **Rapid prototyping** | LangFlow → Flowise |

**In plain terms:**
- **Linear workflow:** the steps run in a fixed order (e.g., retrieve → summarize → answer). A chain is enough.
- **Autonomous agent:** the LLM decides the next step itself, looping until the goal is met.
- **Role-based agents:** several agents with roles (e.g., planner, coder, reviewer) work together like a team.
- **Production orchestration:** running agents reliably at scale, with saved state, retries, monitoring and enterprise integration.
- **Rapid prototyping:** visual drag-and-drop builders for testing an idea quickly. n8n, used in this course, is in the same spirit.

---

## 📋 Framework cheat sheet
General comparison, my summary:

| Framework | Style | Best for |
|---|---|---|
| **n8n** | Visual, low-code workflows with an AI Agent node | Fast automations and integrations (Sheets, Gmail, Slack), which is what the n8n course uses |
| **LangFlow / Flowise** | Visual drag-and-drop builders for LLM apps | Rapid prototyping |
| **LangChain** | Code library of building blocks (models, prompts, tools, retrievers) | Linear chains and simple agents |
| **LlamaIndex** | Code, focused on data and retrieval | RAG over your own documents |
| **LangGraph** | Code. Models an agent as a **graph of steps with shared state**. | Fine control: branches, loops, retries, human-in-the-loop; production |
| **AutoGen** (Microsoft) | Code. Agents **talk to each other in a conversation** to solve a task. | Autonomous and multi-agent collaboration |
| **CrewAI** | Code. **Role-based** agents work as a "crew" on tasks. | Multi-agent teams that are easy to picture |
| **BabyAGI** | Minimal experimental autonomous task loop | Learning how autonomous agents work |
| **ChatDev** | Simulated software company where role agents write code together | Research and demos of role-based agents |
| **Microsoft Agent Framework** | Merges Semantic Kernel (enterprise SDK) and AutoGen (multi-agent) | Enterprise production agents, especially on Azure |

## 🧭 How to choose
1. **Workflow or agent?** If the steps are known, use a workflow. Only use an agent when the LLM really needs to decide the next step.
2. **Single agent or multi-agent?** Start with one agent plus tools. Split into multiple agents only when roles or context become too large for one.
3. **Prototype or production?** Prototype visually (n8n, LangFlow, Flowise), then move to code (LangGraph, Agent Framework) when you need reliability.
4. **Production needs:** observability/tracing, saving state between steps, error handling and retries, guardrails, human approval, cost control.

## 💼 Job-market link
From entry-level AI Engineer postings (Sept 2026): **LangChain** was asked for most often, then **LangGraph** and **LlamaIndex**. CrewAI, AutoGen and Semantic Kernel came up less often. Learn LangChain + LangGraph first.
