# Pydantic for AI Agents · Notes

> Chapter 5 of the [Agentic AI Complete Course (freeCodeCamp)](README.md) · [Video 2:06:23 – 3:27:08](https://www.youtube.com/watch?v=Zy7EXDONlTY&t=7583s)

## 🤔 Why agents need Pydantic
LLMs return **text**, but the rest of the system needs **data**: a tool needs its arguments, a database needs typed rows, the next agent needs fields it can trust. LLM output is never guaranteed: keys get renamed, dates come in different formats, numbers arrive as strings, and fields get invented or dropped.

Pydantic sits at that boundary:
- **Defines the shape** of tool inputs, agent state and LLM outputs as Python classes
- **Validates** the data and gives a clear error listing every problem
- **Converts** compatible values (`"25"` → `25`, `"2026-09-30"` → `date`)
- **Generates JSON Schema** that the LLM is given, so the same class drives both the prompt and the check

Agent frameworks use it everywhere: LangChain `with_structured_output()`, tool argument schemas, LangGraph state, FastAPI request/response bodies.

## 📖 Definition
Pydantic is a **data-validation library driven by Python type hints**. You describe data as a class that inherits from `BaseModel`; creating an instance validates and converts the input, or raises `ValidationError`.

**Analogy:** a customs checkpoint. Every value that crosses into your system gets its papers checked: right type, required fields present, values in range. Anything that doesn't pass is stopped at the border with a list of reasons, instead of causing trouble deep inside the program.

## 💻 Code: the basics
```python
from datetime import date
from pydantic import BaseModel, Field, ValidationError

class JobPosting(BaseModel):
    title: str = Field(min_length=3)
    company: str
    salary_usd: int | None = Field(default=None, ge=0)
    remote: bool = False
    skills: list[str] = []
    posted_on: date

job = JobPosting(title="AI Engineer", company="Acme",
                 salary_usd="150000", remote="yes", posted_on="2026-09-30")
print(job.salary_usd, job.remote, job.posted_on)
# 150000 True 2026-09-30   ← strings converted to int, bool, date

try:
    JobPosting(title="AI", company="Acme", salary_usd=-5, posted_on="tomorrow")
except ValidationError as e:
    print(e.error_count(), "errors")   # 3 errors: title too short, salary < 0, bad date
```

## 💻 Code: custom validators
```python
from pydantic import BaseModel, field_validator, model_validator
from datetime import date

class Interview(BaseModel):
    candidate: str
    starts: date
    ends: date

    @field_validator("candidate")
    @classmethod
    def clean_name(cls, v: str) -> str:      # one field
        return v.strip().title()

    @model_validator(mode="after")
    def in_order(self):                      # across fields
        if self.ends < self.starts:
            raise ValueError("ends is before starts")
        return self
```
- `mode="before"` runs on the **raw** input (good for normalising messy LLM values)
- `mode="after"` runs on the **already-typed** model (good for cross-field rules)

## 💻 Code: validating LLM output
```python
import json
from pydantic import BaseModel, ConfigDict, Field, AliasChoices, ValidationError

class Extraction(BaseModel):
    model_config = ConfigDict(extra="forbid")   # an invented key is an error, not silently kept
    job_title: str = Field(validation_alias=AliasChoices("job_title", "title", "role"))
    years_experience: int = Field(ge=0, le=50)

schema = Extraction.model_json_schema()        # give this to the LLM as its output format

fence = "`" * 3                                # LLMs often wrap JSON in a markdown fence
llm_text = fence + 'json\n{"role": "Data Engineer", "years_experience": "3"}\n' + fence
clean = llm_text.strip().removeprefix(fence + "json").removesuffix(fence)
try:
    result = Extraction.model_validate_json(clean)
    print(result)            # job_title='Data Engineer' years_experience=3
except ValidationError as e:
    print(e.errors())        # send back to the LLM to retry, or set the row aside
```
In LangChain the same class plugs in directly: `llm.with_structured_output(Extraction)`.

**Keywords:**
| Keyword | Meaning |
|---|---|
| `BaseModel` | Base class for a schema |
| `Field(...)` | Defaults, constraints (`ge`, `le`, `min_length`), aliases, descriptions |
| `ValidationError` | Raised with **all** failing fields at once |
| `@field_validator` / `@model_validator` | Custom checks on one field / the whole model |
| `ConfigDict(extra="forbid")` | Reject unknown keys |
| `model_validate(dict)` / `model_validate_json(str)` | Input → validated model |
| `model_dump()` / `model_dump_json()` | Model → dict / JSON |
| `model_json_schema()` | Model → JSON Schema (for LLM structured output and tool definitions) |

## ⚖️ Pydantic vs alternatives
| Option | Validates at runtime? | Use when |
|---|---|---|
| Type hints alone | ❌ only for editors and type checkers | Internal code you control |
| `dataclasses` | ❌ | Simple containers, no untrusted input |
| `TypedDict` | ❌ | Typing plain dicts |
| **Pydantic** | ✅ with conversion and clear errors | Anything from outside: LLMs, APIs, users, files |

## ⚠️ Gotchas
- **v1 vs v2:** many tutorials use v1 names. v2 (2023+): `.dict()` → `.model_dump()`, `.parse_obj()` → `.model_validate()`, `@validator` → `@field_validator`, `class Config` → `model_config = ConfigDict(...)`.
- **Conversion can hide mistakes:** by default `"3"` becomes `3`. Use `strict=True` (per field or model) when you need exact types.
- **A schema guides the LLM; it doesn't guarantee it.** Even with structured output, still validate the response, because a value can have the right shape and still be wrong.
- **Valid ≠ correct:** Pydantic can confirm a code is 10 digits, but not that it's the *right* code. Pair schema checks with business checks (e.g. "is this ID one I actually sent?" via validation `context`) and evals.
- **Optional is not the same as defaulted:** `x: int | None` is still **required** unless you give it a default (`= None`).
- **Mutable defaults:** use `Field(default_factory=list)` for lists and dicts in nested models.

## 📝 From the video
_Add the course's own examples and takeaways here while watching._
