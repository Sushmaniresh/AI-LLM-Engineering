# Interview Prep · AI Engineer (New Grad / Early Career)

## Typical loop (2026)
Recruiter screen → technical screen (LLM concepts + coding) → take-home → onsite (coding, system design, project deep-dive, behavioral) → offer. Usually takes 2–5 weeks.

## 1. LLM concept questions
- What are tokens, context windows, temperature and sampling? What does a token cost at scale?
- **RAG vs. fine-tuning:** use RAG for facts that change, fine-tuning for style or format.
- How do embeddings and vector search work? How do you pick a chunking strategy and a re-ranker?
- **How do you know your RAG system works?** The RAG triad:
  - **Faithfulness:** does the answer stick to the retrieved sources?
  - **Answer relevance:** does it answer the question that was asked?
  - **Context relevance:** was the retrieved text actually useful?
- Hallucinations: what causes them and how do you reduce them (grounding, citations, validation)?

## 2. Agent questions (almost every 2026 loop has one)
- Explain the agent loop: **think → act (tool call) → observe → repeat**.
- Chain vs. agent: when to use a fixed workflow and when to let the LLM decide.
- How does tool/function calling work under the hood? (The LLM emits JSON with a tool name and arguments, your code runs the tool, and the result goes back to the LLM.)
- Memory: short-term (message history) vs. long-term (vector store or summaries).
- Guarding against a runaway loop: max iterations, timeouts, cost caps.
- How would you debug an agent that has drifted from its plan? (Tracing, looking at intermediate steps, evals.)
- LangGraph vs. other frameworks; MCP; multi-agent vs. single agent.
- Safety: prompt injection, the "lethal trifecta" (private data + untrusted content + outbound actions), human-in-the-loop.

## 3. Coding
- **LeetCode-style (easy/medium):** still standard at big companies (TikTok, Dell, ServiceNow, AT&T). Focus on arrays, hashmaps, strings, two pointers, BFS/DFS, heaps.
- **Project-style (AI companies):** batch or parallelize LLM API calls with retries, fix a broken embedding pipeline, parse structured output, write a small eval script. An AI coding assistant is often allowed.

## 4. Take-home (2–4 hrs)
What companies ask for (from 100+ public take-home repos):

| Type | Share |
|---|---|
| RAG system | 40%+ |
| Agent with tool calling / multi-step reasoning | 30%+ |
| Conversational AI | 20%+ |
| Document processing | 15% |
| LLM-as-judge evaluation | 10%+ |

**Treat it like a small production system:** include tests, error handling, a few evals, config and secrets out of the code, and a README covering tradeoffs and next steps.

## 5. AI system design
Practice questions:
- Design a customer-support agent that can look up orders and issue refunds (with human approval).
- Design a RAG assistant over 10K internal PDFs. Cover how you'd ingest, chunk, retrieve, evaluate and keep it fresh.
- Design an insurance-claims agent under a fixed token budget.
- Design a batching and caching layer for LLM queries (cost and latency).
- Design a multi-step email or meeting-scheduling agent.

**Framework for answering:** requirements → architecture diagram → data and retrieval → agent and tool design → evals and monitoring → failure modes and guardrails → cost and latency tradeoffs.

## 6. Behavioral and project deep-dive
Prepare **3 STAR stories**, each with a number and a failure you learned from:
- **Tarifflo AI Engineer Co-op:** what I built, how I measured it, what broke.
- **Citi (Maveric):** production engineering, CI/CD, working with stakeholders.
- **ASU–Mayo research:** handling an open-ended problem and evaluating results.

---

## 🗓️ Prep plan, tied to the n8n course

| When | Do this |
|---|---|
| **Now (Week 1)** | Explain agents, tools, memory and the loop from first principles. Build **one agent in plain Python** with function calling. |
| **Week 2** | RAG on Supabase/pgvector **plus an eval** (a small test set scored on faithfulness and relevance). This matches the most common take-home. |
| **Week 3** | MCP and sub-agents. Rebuild one project in **LangGraph** with **Langfuse** tracing. |
| **Every week** | 3–5 LeetCode mediums, plus 1 AI system design question practiced out loud. |
| **Before applying** | STAR stories ready, resume keywords updated, GitHub projects with good READMEs. |

## 📚 Resources
- [AI engineering field guide: system design questions](https://github.com/alexeygrigorev/ai-engineering-field-guide/blob/main/interview/questions/04-ai-system-design.md)
- [AI engineering field guide: take-home assignments](https://github.com/alexeygrigorev/ai-engineering-field-guide/blob/main/interview/questions/06-home-assignments.md)
- [AI engineering interview Q&A cheat sheet](https://github.com/amitshekhariitbhu/ai-engineering-interview-questions)
- [50 AI Engineer Interview Questions 2026 (Let's Data Science)](https://letsdatascience.com/blog/50-llm-and-ai-engineer-interview-questions-for-2026)
- [Top 30 RAG Interview Questions (DataCamp)](https://www.datacamp.com/blog/rag-interview-questions)
- [45+ AI Engineer Interview Questions (Aced/Exponent)](https://www.tryexponent.com/blog/ai-engineer-interview-questions)
- [Sierra Agent Engineer interview guide (example of an AI-native loop)](https://www.tryexponent.com/guides/sierra-agent-engineer-interview)
- [Agentic AI System Design Interview Guide 2026 (Medium)](https://atul4u.medium.com/the-complete-agentic-ai-system-design-interview-guide-2026-f95d0cfeb7cf)
- [The Lethal Trifecta (Simon Willison)](https://simonwillison.net/2025/Jun/16/the-lethal-trifecta/)
