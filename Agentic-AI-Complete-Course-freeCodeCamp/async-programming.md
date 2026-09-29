# Asynchronous Programming in Python · Notes

> Chapter 4 of the [Agentic AI Complete Course (freeCodeCamp)](README.md) · [Video 1:33:18 – 2:06:23](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=5598s)

## 🤔 Why agents need async
Agent orchestration frameworks run on async under the hood:
- **LangGraph:** state machine, built with first-class async support
- **AutoGen:** actor model, async at its core
- **CrewAI:** role-based, process-driven, supports async execution

In a multi-agent system, independent agents (e.g., one searches the web, one collects images, one downloads files) spend most of their time **waiting** on APIs, LLM calls and databases. Run one after another, their times add up. Run concurrently, the total is roughly the time of the slowest agent.

| Agent | Time |
|---|---|
| Agent 1 | 1 min |
| Agent 2 | 1.5 min |
| Agent 3 | 1 min |
| **Sync (one after another)** | **3.5 min** |
| **Async (overlapping)** | **~1.5 min** |

## 📖 Definition
Asynchronous programming lets code handle **multiple tasks concurrently without blocking** the program. It's mainly for **I/O-bound** work (network requests, file I/O, database queries): while one task waits on a slow external event, the program does other work.

**Analogy (cooking three dishes):**
- **Sync:** a stove with one burner. Cook dish 1, then dish 2, then dish 3. Total = T1 + T2 + T3.
- **Async:** start all three and switch between them while each one simmers. Total ≈ the longest dish.

## 🔁 Subroutine vs coroutine
- **Subroutine** (normal `def`): when called, it runs to the end before the caller continues. If it waits on an API, the whole program waits.
- **Coroutine** (`async def`): it can **pause** at an `await`, hand control back, and let other code run until its result is ready.

## 💻 Code: sync vs async

**Sync:** the two independent calls run back to back, taking **~6 s** (4 + 2).
```python
import time

def fetch_weather():
    print("Fetching weather data...")
    time.sleep(4)  # simulate network delay
    print("Weather data fetched")

def fetch_news():
    print("Fetching news data...")
    time.sleep(2)
    print("News data fetched")

def main():
    start = time.time()
    fetch_weather()
    fetch_news()
    print(f"Total time taken: {time.time() - start:.2f} s")

main()
```

**Async:** both calls wait at the same time, taking **~4 s** (the slower of the two).
```python
import asyncio
import time

async def fetch_weather():
    print("Fetching weather data...")
    await asyncio.sleep(4)  # NOT time.sleep: that would block everything
    print("Weather data fetched")
    return "weather"

async def fetch_news():
    print("Fetching news data...")
    await asyncio.sleep(2)
    print("News data fetched")
    return "news"

async def main():
    start = time.time()
    weather, news = await asyncio.gather(fetch_weather(), fetch_news())
    print(f"Total time taken: {time.time() - start:.2f} s")

asyncio.run(main())  # in Jupyter, use: await main()
```

**Keywords:**
| Keyword | Meaning |
|---|---|
| `async def` | Defines a coroutine |
| `await` | Pause here until the result is ready, and let other tasks run meanwhile |
| `asyncio.gather(...)` | Run several coroutines concurrently and collect their results in order |
| `asyncio.run(main())` | Start the event loop from a normal script |

## ⚖️ Concurrency vs parallelism
- **Concurrency:** managing multiple tasks whose lifetimes **overlap** (start, run and finish at overlapping times). `asyncio` does this on **one thread**, switching tasks at each `await`.
- **Parallelism:** tasks running **at literally the same moment** on multiple threads or processes (multiple CPU cores).

## ⚠️ Gotchas
- The course says async runs agents "in parallel". Strictly, `asyncio` is **concurrent, not parallel**: one thread switches between tasks while they wait. That's exactly what I/O-bound agent calls need.
- Async only helps **I/O-bound** work. CPU-heavy work (e.g., running a model locally) needs multiprocessing or threads.
- A blocking call like `time.sleep()` or `requests.get()` inside `async def` freezes every task. Use async versions instead: `asyncio.sleep()`, `httpx.AsyncClient`, `aiohttp`.
- Only independent tasks can run concurrently. If agent B needs agent A's output, B must `await` A first.
