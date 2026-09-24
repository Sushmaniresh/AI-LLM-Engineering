# Day 1 — LLM Token Optimization · Cheatsheet

> A one-page summary of the Day 1 PDFs. The detail is in each PDF.

| # | Topic | PDF |
|---|---|---|
| 1 | Token Economics & Cost of Scale | [Section 1](Section+1+Token+Economics+and+the+Cost+of+Scale.pdf) |
| 2 | Prompt Compression & Structured Outputs | [Section 2](Section+2+Prompt+Compression+and+Structured+Outputs.pdf) |
| 3 | Context Compaction & Memory Management | [Section 3](Section+3+Context+Compaction+and+Memory+Management.pdf) |
| 4 | Semantic Caching | [Section 4](Section+4+Semantic+Caching+for+Redundancy+Reduction.pdf) |
| 5 | Dynamic Model Routing & Orchestration | [Section 5](Section+5+Dynamic+Model+Routing+and+Orchestration.pdf) |
| ★ | Pricing Matrix & ROI Calculator | [Reference](LLM+Token+Pricing+and+ROI+Calculator.pdf) |

---

## 🧠 The 5 Levers (TL;DR)

| Lever | What it does | Typical win |
|---|---|---|
| **Semantic cache** | Serves a stored answer when a new query means the same as an old one | 30–65% of traffic never reaches the LLM |
| **Model routing** | Sends each task to the cheapest model that can handle it | 40–85% lower blended cost |
| **RAG pruning + reranking** | Keeps only relevant chunks | Context size 8k → 1.5k tokens |
| **Prompt minification / compression** | Removes words that carry no meaning | 12% (minify) to 50%+ (LLMLingua) fewer input tokens |
| **Native structured output** | Enforces the JSON schema in the API (constrained decoding), not in prompt text | Case study: 45% lower cost, zero parse errors |

**Combined case study:** 2M requests/day, all five levers → **88% lower blended cost**.

---

## 1️⃣ Token Economics

- **Output costs 3–4× input** (1:3 or 1:4 ratio). Keep outputs short first.
- **Multi-turn:** each turn re-sends the whole history, so you pay for it again every turn.
- **Agents:** chain-of-thought, tool schemas, re-reading tool results, and the final summary mean several generations per user request.
- **Multimodal:** an image is split into tiles of hundreds of tokens each. Audio becomes large token blocks.
- **Where tokens are wasted:** overlapping RAG chunks, a badly set top-k, polite filler, repeated rules, extra whitespace, and memory buffers that store every raw turn.
- **Model tiers:** lightweight models (8–14B) for routing, classification, and extraction. Frontier models (1T+) only for deep reasoning.
- **Telemetry pipeline:** gateway proxy → read usage headers → tag with user, feature, and session → send to an observability DB.
- **Baseline metrics:** average input tokens per transaction type, value per 1k output tokens, % of tokens spent on redundant RAG chunks, cost per session.

## 2️⃣ Prompt Compression & Structured Outputs

- **Minification pipeline:** raw prompt → regex out extra whitespace → drop stop words → send.
- **Use shorthand instead of prose:**
  - ❌ "Please ensure that the response is formatted as a comma-separated values file and sorted by the date created." (22 tok)
  - ✅ `Output: CSV. Sort: created_date ASC.` (9 tok)
- Write rules as booleans and bullets, not prose. Use domain terms.
- **Calibrate:** raise compression step by step and score output against an eval rubric. Too much compression causes hallucinations and skipped instructions.
- **Constrained decoding:** the engine blocks any token that would break the schema, so valid output is guaranteed. Don't also write "please output valid JSON" in the prompt.
- **Shrinking schemas:** short keys (`dob`, not `date_of_birth`), no `description` fields, flat structure (no deep nesting), no rarely used optional fields.
- **Rules:** use native JSON mode for system-to-system calls · minify schemas in code review · no markdown or filler on programmatic endpoints.

## 3️⃣ Context Compaction & Memory

- **Rolling summarization:** when the buffer reaches a token threshold → take the oldest turns → summarize them with a cheap model → put the summary at the top of the context.
- **RAG filtering:** drop chunks below a similarity threshold · remove navigation and other boilerplate · remove duplicate text from overlapping chunks.
- **Semantic pruning is better than static chunking:** pass only the sentences that answer the query.
- **Cross-encoder rerank** of the top-k results → fewer, better chunks.
- **Context budget:** set a fixed token budget → sort items by relevance → add until the budget is full → drop the rest. This keeps cost per request predictable.
- **Modular system prompts:** intent classifier → core persona and safety rules → only the modules this task needs → join them.
- **Batching:** put many short docs in one prompt so the system prompt is paid for once.
- **Remove redundancy:** don't restate a schema that JSON mode already enforces · merge duplicate tool descriptions · delete rules for edge cases the model handles anyway.
- **Governance:** keep prompts in version control · run a token count in CI on every PR · A/B rollout · roll back on cost spikes.

## 4️⃣ Semantic Caching

- Embed the query → cosine similarity against cached queries → **above the threshold, serve the cached answer; below it, call the LLM and cache the result.**
- **Threshold trade-off:**

  | Threshold | Hit rate | Savings | Risk |
  |---|---|---|---|
  | ~0.94 | ~38% | 35–40% | ~10% false positives |
  | 0.95–0.96 | — | — | Course default |
  | ~0.97 | ~20% | 15–20% | Almost none |

- False-positive examples: "reset password" vs "reset password **as admin**"; "cancel" vs "**pause** subscription".
- **Tuning:** normalize case and punctuation before embedding · set thresholds per task type · require exact matches on key entities · log false positives.
- **Speed:** a cache hit takes ms; an LLM call takes seconds (~7s → 25ms).
- **Exact-match caching** gets only a 10–15% hit rate on chatbots. Use semantic caching.
- **Architecture:** edge cache (Redis) → central vector DB → LLM, with each new answer written back to both cache layers.
  - In-memory: fast but costly RAM · Disk-backed: cheap, slightly slower.
- **Invalidation:** TTL based on how fast the data changes · purge when RAG sources or model weights change · purge answers users flag.
  - Static facts: cache indefinitely · time-sensitive facts (e.g. stock price): very short TTL.
- **Streaming:** *stream-then-cache* (store the answer after streaming it) vs *early-exit* (preload common answers).
- **Case study:** 0.96 threshold → 65% of traffic served from cache → **$12k/day → <$4.5k/day**.

## 5️⃣ Model Routing & Orchestration

- **Gateway flow:** classify → rewrite the request for the chosen provider → call → normalize the response.
- **Cheap classifiers:** Naive Bayes · a fine-tuned 8B model · keyword heuristics · embedding clusters. Cost about 10–430 ms and ~$0.001 per call.
- **Three-tier stack:**

  | Tier | Use for | Example models |
  |---|---|---|
  | Fast / Cheap | Extraction, formatting, simple turns | DeepSeek V4-Flash, Gemini 3.1 Flash-Lite |
  | Smart / Mid | Medium logic, standard coding | Claude Haiku 4.5, GPT-5.4-mini |
  | Power | Deep reasoning, agents, synthesis | Claude Sonnet 4.5, GPT-5.5 |

- **Typical workload mix:** 60% easy · 25% medium · 15% hard. RouteLLM keeps **~95% of flagship quality**.
- **Quality checks:** LLM-as-judge grades **5%** of cheap-tier responses · move tasks that keep failing up a tier · move queries that keep succeeding down a tier.
- **Safe rollout:** **shadow** test (copy traffic to the new model, don't show its output) → **canary** (10% of live traffic) → full rollout.
- **Dashboard:** blended cost per 1k tokens · cache hit ratio · error rate by tier · spend by feature. Review weekly.

---

## 🧮 ROI Formulas

These are rebuilt from the Reference PDF's definitions and example numbers. The equations themselves did not extract from the PDF.

| Metric | Formula |
|---|---|
| Baseline cost | `C_base = V × (T_in·P_in + T_out·P_out)` |
| Optimized cost | `C_opt = C_base × α` (α = 0.3 means 70% fewer tokens) |
| Routed cost | `C_route = V × (w_f·C_f + w_s·C_s + w_p·C_p + C_classify)`, where `w_f + w_s + w_p = 1` |
| Gross benefit (labor saved) | `GFB = N_tickets × Cost_per_ticket × Deflection%` |
| ROI | `(GFB − C_AI) / C_AI × 100` |

**Worked example:** 10k tickets × $5 × 70% deflection = **$35,000**. AI cost is $6,363, so net is $28,637 → **ROI ≈ 450%**.

**Output-limit trap:** 900k input + 100k output on GPT-5.5 costs $4.50 + $3.00. If the output limit forces 5 chunked calls, you pay **$22.50 in repeated input alone**.

---

## 💲 Price Snapshot (USD / 1M tokens, per the course PDF)

| Model | Input | Cached | Output | Notes |
|---|---|---|---|---|
| GPT-5.5 | 5.00 | 0.50 | 30.00 | Long context costs 2× · 128k max output |
| GPT-5.4-mini | 0.75 | 0.075 | 4.50 | |
| GPT-5.4-nano | 0.20 | 0.02 | 1.25 | |
| Claude Sonnet 4.5 | 3.00 | 0.30 | 15.00 | Cache write: 5-min 3.75 / 1-hr 6.00 |
| Claude Haiku 4.5 | 1.00 | 0.10 | 5.00 | |
| Gemini 3.5 Flash | 1.50 | 0.15 | 9.00 | Cache storage $1/M/hr |
| Gemini 3.1 Flash-Lite | 0.25 | 0.025 | 1.50 | |
| DeepSeek V4-Flash | 0.14 | 0.0028 | 0.28 | Concurrency cap 2500 |
| DeepSeek V4-Pro | 0.435 | 0.0036 | 0.87 | Concurrency cap 500 |

- **Batch API = 50% off** (OpenAI and Anthropic). **Priority tier ≈ 2×**.
- **Data residency** (OpenAI) adds **10%**.
- **Gemini:** the cache storage fee keeps billing until you delete the cache. Grounding costs $14 per 1k queries after 5k free.
- **Anthropic:** use the 5-min cache for bursty traffic and the 1-hr cache for long document sessions.

---

## ⚠️ Context-Window Reality Check

- Every major vendor now offers a 1M-token context window, but **recall degrades past ~300k tokens**. The best cost/quality range is **~150–250k**.
- At 520k+ tokens, models invent functions and dependencies that don't exist.
- Gemini chats lose coherence after ~30 messages. In practice, usable context is ~32–64k.
- **Rule:** just because the window fits it doesn't mean you should send it. Use RAG to send only what the task needs.

---

## ✅ Default Deployment Checklist

- [ ] Put a semantic cache in front of the gateway
- [ ] Route by complexity. Never use a frontier model for basic extraction
- [ ] Use native JSON mode with minified schemas
- [ ] Assemble system prompts from modules. Check token counts in CI
- [ ] Rerank and prune RAG chunks within a fixed token budget
- [ ] Use rolling summaries for chat memory
- [ ] Tag telemetry by feature and session. Review it weekly
- [ ] Have an LLM judge grade 5% of cheap-tier output
- [ ] Roll out new models via shadow → canary (10%) → full
