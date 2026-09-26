# Day 2 — AI Builder with n8n: Agents & Voice Agents · Notes

> A 3-week track to become an agentic AI builder with n8n. In progress: **Week 1, Day 1**.

📚 **Course:** [AI Builder: Create Agents, Voice Agents & Automations in n8n](https://www.udemy.com/course/ai-builder-with-n8n-create-agents-voice-agents/) (Udemy · Ed Donner) · Started Sept 25, 2026
🔗 **Resources:** [Course resources page](https://edwarddonner.com/2026/01/04/ai-builder-with-n8n-create-agents-and-voice-agents/) · [Slides (Google Drive)](https://drive.google.com/drive/folders/1NIQD4azfKQkH3iWClcOoRHk6Yxu53VKq?usp=drive_link)

---

## 🗺️ 3 Weeks to Agentic AI Builder

Legend: 🟪 Core skills · 🟨 Integrations · 🟦 Projects

### Week 1 — Automate with workflows in n8n cloud
- [x] 🟪 First n8n AI Agent Live — n8n cloud + OpenRouter setup, LLM background
- [ ] 🟪 Foundations: Agentic AI and n8n
- [ ] 🟨 Integrations with docs and email — Gmail
- [ ] 🟨 Data and integrations — Slack, Google Sheets
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
