# vLLM · Notes

> High-throughput, memory-efficient LLM inference and serving: PagedAttention, continuous batching, and an OpenAI-compatible server.

**Docs:** [docs.vllm.ai](https://docs.vllm.ai) · **Repo:** [vllm-project/vllm](https://github.com/vllm-project/vllm)
**Started:** Sept 29, 2026

## 📺 vLLM: Easily Deploying & Serving LLMs
[Video (NeuralNine, ~15 min)](https://www.youtube.com/watch?v=q5IF2PHA5SA) · Using vLLM to run Hugging Face models in Python, serve them behind an OpenAI-compatible API, and serve your own fine-tuned GGUF model.

### 📋 Progress

| # | Section | Timestamp | Status |
|---|---|---|---|
| 1 | Intro: the 3 use cases | [0:00](https://www.youtube.com/watch?v=q5IF2PHA5SA&t=0s) | ⏳ |
| 2 | Setup: `pip install vllm` (or `uv add vllm`) | [1:20](https://www.youtube.com/watch?v=q5IF2PHA5SA&t=80s) | ⏳ |
| 3 | Use case 1: run a Hugging Face model in code (`LLM` + `SamplingParams`) | [2:16](https://www.youtube.com/watch?v=q5IF2PHA5SA&t=136s) | ⏳ |
| 4 | Use case 2: `vllm serve` + call it with the OpenAI client | [5:54](https://www.youtube.com/watch?v=q5IF2PHA5SA&t=354s) | ⏳ |
| 5 | Use case 3: serve a custom fine-tuned GGUF model | [10:28](https://www.youtube.com/watch?v=q5IF2PHA5SA&t=628s) | ⏳ |

### 🧠 Notes

---

## 🔭 Beyond the video
The video covers usage. These are the topics to study next, to understand *why* vLLM is fast:
- **Core ideas:** PagedAttention, continuous batching, prefix caching, chunked prefill
- **Performance:** quantization (AWQ, GPTQ, FP8), speculative decoding, tensor/pipeline parallelism
- **Deployment:** Docker, Kubernetes, and how it compares to TGI, TensorRT-LLM, SGLang and Ollama
