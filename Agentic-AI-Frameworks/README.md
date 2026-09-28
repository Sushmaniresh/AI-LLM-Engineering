# Agentic AI Frameworks · Notes

> How to choose an agent framework for workflows, multi-agent systems and production.

## 📺 Agentic AI Frameworks Explained: Workflows, Multi-Agent & Production
[Video (IBM Technology, ~12 min)](https://www.youtube.com/watch?v=ZVPlLaehjLk) · How to choose between agent frameworks such as LangChain, AutoGen and CrewAI, based on your system design and real-world needs.

**Framework cheat sheet** (general comparison, my summary):

| Framework | Style | Best for |
|---|---|---|
| **n8n** | Visual, low-code workflows with an AI Agent node | Fast automations and integrations (Sheets, Gmail, Slack), which is what this course uses |
| **LangChain / LangGraph** | Code (Python/JS). LangGraph models an agent as a **graph of steps with shared state**. | Workflows where you need fine control: branches, loops, retries, human-in-the-loop |
| **CrewAI** | Code. **Role-based** agents (e.g., researcher, writer) work as a "crew" on tasks. | Multi-agent systems that are easy to picture as a team |
| **AutoGen** (Microsoft) | Code. Agents **talk to each other in a conversation** to solve a task. | Multi-agent collaboration, research and coding agents |

**How to choose:**
1. **Workflow or agent?** If the steps are known, use a workflow. Only use an agent when the LLM really needs to decide the next step.
2. **Single agent or multi-agent?** Start with one agent plus tools. Split into multiple agents only when roles or context become too large for one.
3. **Production needs:** observability/tracing, saving state between steps, error handling and retries, guardrails, human approval, cost control.
