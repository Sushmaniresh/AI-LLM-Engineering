# Day 2 — AI Builder with n8n: Agents & Voice Agents · Notes

> A 3-week track to become an agentic AI builder with n8n. In progress: **Week 1, Day 3**.

📚 **Course:** [AI Builder: Create Agents, Voice Agents & Automations in n8n](https://www.udemy.com/course/ai-builder-with-n8n-create-agents-voice-agents/) (Udemy · Ed Donner) · Started Sept 25, 2026
🔗 **Resources:** [Course resources page](https://edwarddonner.com/2026/01/04/ai-builder-with-n8n-create-agents-and-voice-agents/) · [Slides (Google Drive)](https://drive.google.com/drive/folders/1NIQD4azfKQkH3iWClcOoRHk6Yxu53VKq?usp=drive_link)

---

## 🗺️ 3 Weeks to Agentic AI Builder

Legend: 🟪 Core skills · 🟨 Integrations · 🟦 Projects

### Week 1 — Automate with workflows in n8n cloud
- [x] 🟪 First n8n AI Agent Live — n8n cloud + OpenRouter setup, LLM background
- [x] 🟪 Foundations: Agentic AI and n8n
- [x] 🟨 Integrations with docs and email — Gmail
- [ ] 🟨 Data and integrations — Slack, Google Sheets *(Sheets ✅, Slack pending)*
- [ ] 🟦 **Project 1: Daily Portfolio Rebalancer** — autonomous agent monitors MarketStack prices and rebalances a Google Sheets portfolio with OpenAI ([sample sheet](https://docs.google.com/spreadsheets/d/1ON3WSXGeh6Qt9UVUduJiKU54KoEUMXVoeMOPflHu8tA/edit?usp=sharing))

### Week 2 — Accelerate with Voice Agents and RAG
- [ ] 🟪 Voice Agents with ElevenLabs
- [ ] 🟨 Integrating ElevenLabs and n8n
- [ ] 🟪 RAG and Agentic RAG — Gemini / OpenAI embeddings
- [ ] 🟨 Supabase Vectors and Data ingest
- [ ] 🟦 **Project 2: Product Expert Voice Agent** — conversational voice agent over ElevenLabs + Twilio with Supabase RAG

### Week 3 — Amplify with multi-agent systems and MCP 🏆
- [ ] 🟪 Self-hosted n8n and Local LLMs — Docker + Ollama
- [ ] 🟨 Advanced Integrations — Pipedrive CRM, FireCrawl
- [ ] 🟪 MCP (Model Context Protocol)
- [ ] 🟪 Context Engineering, Sub-Agents — DeepSeek
- [ ] 🟦 **Capstone: Amplify your business** — multi-agent go-to-market system: scrape leads, enrich data, book meetings

---

## 🧰 Setup & Accounts

| Tool | Needed for | Cost |
|---|---|---|
| [n8n Cloud](https://n8n.io) | Weeks 1–2 | Free trial (≥ 2 weeks) |
| [OpenRouter](https://openrouter.ai) | LLM access | Free tier / pay-as-you-go |
| Google Sheets, Gmail, Slack | Week 1 integrations | Free |
| ElevenLabs, Twilio | Week 2 voice agent | Free trial |
| Supabase | Week 2 RAG vector store | Free tier |
| Docker + [Ollama](https://ollama.com) | Week 3 self-hosting | Free |
| Pipedrive, FireCrawl | Week 3 capstone | Free trial |

**Self-hosted n8n (Week 3):**
```bash
docker run -it --rm --name n8n -p 5678:5678 \
  -e GENERIC_TIMEZONE="Europe/London" -e TZ="Europe/London" \
  -e N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true -e N8N_RUNNERS_ENABLED=true \
  -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

**Further reading:** [Context Engineering (Phil Schmid)](https://www.philschmid.de/context-engineering) · [The Lethal Trifecta (Simon Willison)](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/)

---

## 🧠 Key Takeaways

### Week 1 · Day 1 — First n8n AI Agent Live ✅

**What I built:** a stock data agent in n8n that answers questions about a given stock by calling a live data tool.

```
Chat Trigger ──▶ AI Agent ──▶ reply
                   ├── Chat Model: OpenAI (the "brain" — decides what to do)
                   ├── Tool: MarketStack (fetches live stock prices)
                   └── Memory: keeps the conversation context between messages
```

- Asked, e.g., *"What's the latest price of AAPL?"*. The agent recognized it needed data, **called the MarketStack tool**, and answered using the returned data instead of guessing.
- Adding **memory** lets follow-ups work (*"and how about MSFT?"*) because the agent remembers the earlier turns.

**Concepts learned:**

| Term | Definition |
|---|---|
| **LLM** | Large Language Model: a model trained on huge amounts of text to predict the next token. It powers text generation, reasoning and summarization (e.g., GPT, Claude, Gemini). |
| **API** | Application Programming Interface: a defined way for programs to talk to each other. You send a request (e.g., a prompt or a stock symbol) and get a structured response back. |
| **AI Agent** | An LLM in a loop with **tools** and **memory**. It can plan, decide which tool to call, act, look at the result, and repeat until the task is done. |
| **Tool** | An external capability the agent can call, such as an API, a database or an app (here: MarketStack). |
| **Memory** | Stored conversation history or context that the agent sees on each turn. |

**Types of AI agents:**
- **Simple / reactive:** responds to input with one LLM call and no tools.
- **Tool-using:** calls APIs to fetch data or take actions (today's agent).
- **Planning / multi-step:** breaks a goal into steps and executes them in sequence.
- **Multi-agent:** several specialized agents coordinate, e.g., via sub-agents or MCP (Week 3).

**Use cases:** customer support bots, research assistants, financial monitoring (e.g., portfolio rebalancing), sales lead generation, email/document automation, and voice receptionists.

### Week 1 · Day 2 — Foundations: Agentic AI and n8n ✅

**What I built:** the same stock data agent from Day 1, with two additions:
- **System message:** set in the AI Agent node's options. It's the standing instruction the LLM sees on every turn (its role, tone, rules, when to use the MarketStack tool), separate from the user's chat message.
- **Checking executions:** used the **Executions** tab to inspect each run. It shows the input and output of every node, which tool calls the agent made and with what arguments, and where it failed. This is how you debug an agent and watch its loop step by step.

**The five building blocks of an agent:**

```
            ┌──────────────── Loop ────────────────┐
User ──▶ Memory + prompt ──▶ LLM (reasoning) ──▶ tool call? ──yes──▶ Tool ──▶ result
                                   │                                          │
                                   no ◀───────────── added to context ◀───────┘
                                   ▼
                                 answer
```

| Concept | What it means | In n8n |
|---|---|---|
| **Memory** | LLMs are **stateless**: every call starts from zero. "Memory" is the app re-sending past messages (or a summary) with each new prompt, so it uses context-window tokens. | Memory sub-node on the AI Agent (e.g., Simple Memory), with a context-window length setting |
| **Reasoning** | The model working through a problem step by step before answering (chain-of-thought). Reasoning models do this internally: better on multi-step problems, but slower and more expensive. | Pick a reasoning-capable model in the Chat Model node |
| **LLM chaining** | Splitting a task into several LLM calls where one call's output is the next call's input (e.g., extract → analyze → write). Each step is simpler and easier to test. | Several LLM / Basic LLM Chain nodes wired in sequence |
| **Tools** | The LLM can't run code or call APIs itself. It returns a structured request (tool name + arguments), the framework runs the tool, and the result goes back to the LLM. | Tool sub-nodes on the AI Agent (e.g., MarketStack, HTTP Request) |
| **Loop** | The agent repeats *think → act (call a tool) → observe the result* until it has enough to answer. The loop is what separates an **agent** from a fixed **workflow**. | Built into the AI Agent node (max iterations option) |

**Workflow vs. agent:**
- **Workflow (chaining):** the developer fixes the steps in advance. It's predictable, cheap and easy to debug.
- **Agent (loop + tools):** the LLM chooses the next step at runtime. It's more flexible but less predictable, so cap the iterations and watch costs.

### Week 1 · Day 3 — Integrations: Google Sheets + Gmail ✅

**What I built:** two separate agents.

**1. Stock agent + Google Sheets**

```
Chat Trigger ──▶ AI Agent ──▶ reply
                   ├── Chat Model: OpenAI
                   ├── Tool: Google Sheets (read) ── get the tickers listed in the sheet
                   ├── Tool: MarketStack ─────────── fetch the latest price for each ticker
                   ├── Tool: Google Sheets (update) ─ write the prices back to the sheet
                   └── Memory
```

- The agent reads the tickers from the sheet, looks up each price with MarketStack, and **updates** the price cells. It both reads and writes data.
- This is the base for **Project 1: Daily Portfolio Rebalancer**.

**2. Gmail assistant agent**

```
Chat Trigger ──▶ AI Agent ──▶ reply
                   ├── Chat Model: OpenAI
                   ├── Tool: Gmail (get messages) ── read the inbox
                   ├── Tool: Gmail (create draft) ── LLM writes a reply as a draft
                   └── Memory
```

- **Reads the inbox and flags what's important:** the LLM summarizes recent emails and says which ones need attention.
- **Drafts emails:** the LLM writes replies and saves them as Gmail **drafts**, so nothing is sent until I review it. This is a simple human-in-the-loop step.

**Concepts learned:**

| Term | Definition |
|---|---|
| **Integration** | A connection between n8n and an external app (Sheets, Gmail, Slack) so a workflow or agent can read data from it or act in it. |
| **Credentials / OAuth** | Authorizing n8n to access your Google account once. The token is stored in n8n's credentials, not in the workflow. |
| **Read vs. write tools** | Read tools (look up rows, read emails) are low risk. Write tools (update rows, send email) change things in the real world, so test them on a copy first. Creating a **draft** instead of sending the email adds a human approval step. |
| **Row matching** | To update a row, the Sheets node needs a column to match on (e.g., `Ticker`) so each price lands on the correct row. |

**Tips:**
- Give each tool a clear **name and description**. The agent picks tools based on the description.
- Check the **Executions** tab to confirm which rows were updated and which emails the agent read or drafted.
- An inbox agent reads untrusted text: an email could contain instructions aimed at the LLM (prompt injection). Keep it to drafts, not sending.

---

## 📺 Extra Learning

### Agentic AI Frameworks Explained: Workflows, Multi-Agent & Production
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
