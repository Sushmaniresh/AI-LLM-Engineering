# Agentic AI Complete Course (freeCodeCamp) · Notes

> Building production-ready single- and multi-agent systems with LangChain and LangGraph: workflows, memory, RAG, human-in-the-loop, and deployment to AWS and Render.

**Course:** [Agentic AI – Complete Course for Beginners](https://www.youtube.com/watch?v=Zy7EXDONlTY) (freeCodeCamp, taught by Boktiar Ahmed Bappy) · ~24 hours · [Code](https://github.com/entbappy/Complete-Agentic-AI-Course)
**Started:** Sept 29, 2026

## 📋 Progress

| # | Chapter | Timestamp | Status |
|---|---|---|---|
| 1 | Introduction & Planning | [0:00:00](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=0s) | ✅ |
| 2 | Evolution from LLMs to Agentic AI | [0:08:33](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=513s) | ✅ |
| 3 | Agentic AI: Core Characteristics & Components | [0:41:26](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=2486s) | 🟡 |
| 4 | Asynchronous Programming for AI Agents ([notes](async-programming.md)) | [1:33:18](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=5598s) | ⏳ |
| 5 | Pydantic for AI Agents: Data Validation ([notes](pydantic.md)) | [2:06:23](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=7583s) | 🟡 |
| 6 | End-to-End Single AI Agent System with LangChain | [3:27:08](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=12428s) | ⏳ |
| 7 | End-to-End Multi-Agent AI System with LangChain | [4:49:37](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=17377s) | ⏳ |
| 8 | What is LangGraph & Why It's Required (LangChain vs LangGraph) | [6:30:27](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=23427s) | ⏳ |
| 9 | LangGraph Core Components | [7:44:43](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=27883s) | ⏳ |
| 10 | Sequential Workflows in LangGraph | [8:29:07](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=30547s) | ⏳ |
| 11 | Parallel Workflows in LangGraph | [9:19:04](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=33544s) | ⏳ |
| 12 | Conditional Workflows in LangGraph | [10:15:27](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=36927s) | ⏳ |
| 13 | Iterative Workflows in LangGraph | [11:06:05](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=39965s) | ⏳ |
| 14 | First Agentic Chatbot with LangGraph | [11:35:51](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=41751s) | ⏳ |
| 15 | Why Persistence is Required in LangGraph | [12:43:49](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=45829s) | ⏳ |
| 16 | Streaming Responses in the Chatbot | [13:51:00](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=49860s) | ⏳ |
| 17 | Chat Threading in the Chatbot | [14:10:02](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=51002s) | ⏳ |
| 18 | Permanent Chat Memory with LangGraph + Database | [14:42:02](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=52922s) | ⏳ |
| 19 | Monitoring with LangSmith | [15:16:31](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=54991s) | ⏳ |
| 20 | Integrating Tools in the Chatbot | [15:40:24](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=56424s) | ⏳ |
| 21 | RAG in the Chatbot | [16:54:17](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=60857s) | ⏳ |
| 22 | Human-in-the-Loop (HITL) | [17:50:27](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=64227s) | ⏳ |
| 23 | CI/CD Deployment on AWS with Docker & GitHub Actions | [18:48:09](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=67689s) | ⏳ |
| 24 | Deploy on Render for Free with Docker | [19:52:05](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=71525s) | ⏳ |
| 25 | Project: Build Your Own ChatGPT Agent (LangGraph, FastAPI, LangSmith, ChromaDB, SQLAlchemy, AWS) | [20:06:27](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=72387s) | ⏳ |
| 26 | Project: TripMate AI, a Multi-Agent Travel Planner (Groq, LangGraph, PostgreSQL, FastAPI) | [22:21:17](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=80477s) | ⏳ |

---

## 🧠 Notes

### 3. Core components of an agentic AI system

An agent is built from five components:

| Component | Capability | What it does |
|---|---|---|
| 🧠 **Brain** | Goal interpretation | Understands user instructions and turns them into objectives |
| | Planning | Breaks high-level goals into subgoals and ordered steps |
| | Reasoning | Makes decisions, resolves ambiguity, weighs trade-offs |
| | Tool selection | Chooses which tool(s) to use at each step |
| 🎼 **Orchestrator** | Task sequencing | Decides the order of actions (step 1 → step 2 → …) |
| | Conditional routing | Directs flow based on context (e.g., on failure, retry or escalate) |
| | Retry logic | Handles failed tool calls or reasoning attempts with backoff |
| | Looping & iteration | Repeats steps (e.g., keep checking job applications until 10 arrive) |
| | Delegation | Decides whether to hand work to tools, the LLM, or a human |
| 🔧 **Tools** | External actions | Makes API calls (e.g., post a job, send an email, trigger onboarding) |
| | Knowledge base access | Retrieves factual or domain-specific info via RAG or search |
| 💾 **Memory** | Short-term memory | Holds the current session's context: recent messages, tool calls, immediate decisions |
| | Long-term memory | Persists goals, past interactions, user preferences and decisions across sessions |
| | State tracking | Tracks progress: what's done and what's pending (e.g., "JD posted", "Offer sent") |
| 👤 **Supervisor** | Approval requests (HITL) | Checks with a human before high-risk actions (e.g., sending an offer) |
| | Guardrails enforcement | Blocks unsafe or non-compliant behavior |
| | Edge case escalation | Alerts a human when uncertainty or conflict arises |
