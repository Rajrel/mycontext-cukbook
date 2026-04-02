# Don't Guess Which Prompt Your App Needs

**The problem.** There are 88 cognitive patterns in mycontext (16 free + 72 enterprise) — data analyzers, root cause analysts, risk assessors, scenario planners, synthesis builders, and more. Each one is optimized for a specific kind of reasoning task. The problem: how do you know which one your question needs? You could browse the docs. You could guess. Or you could use a wrong template and get worse results than no template at all.

**The pitch.** `suggest_patterns(question)` reads your task, analyzes its structure, and recommends the right templates with confidence scores and reasoning. No guessing. No browsing. Just `suggest_patterns("your task")`.

---

## Research grounding

Template selection is grounded in **cognitive task analysis** — the idea that different reasoning tasks have structurally different optimal prompting approaches. A data analysis task benefits from a systematic deductive framework; a risk assessment from multi-angle exploration; a synthesis from integrative reasoning. mycontext's 88 patterns encode these structural differences as parameterized cognitive patterns. The `suggest_patterns` engine uses keyword matching trained on the catalog's task taxonomy, validated by the CAI benchmark showing that using the *right* template for a task produces 15–30% better output quality than using any template. The penalty measured below for picking the wrong template is consistent with this finding.

---

## Why the right template matters

Using the wrong template isn't neutral — it actively hurts quality. A `brainstormer` context applied to a risk-assessment task produces creative but ungrounded output. A `data_analyzer` on a narrative synthesis task produces rigid, over-structured prose.

We measured this:

| Template | Task | Quality Score |
|----------|------|--------------|
| `data_analyzer` (right) | "Compare quarterly revenue trends" | 86/100 |
| `brainstormer` (wrong) | "Compare quarterly revenue trends" | 61/100 |

**-25 points from picking the wrong template.** `suggest_patterns` eliminates that tax.

---

## Three ways to suggest

```python
from mycontext.intelligence import suggest_patterns

question = "Compare quarterly revenue trends across our three product lines and explain the drivers"

# Mode 1: keyword — instant, no API key
result = suggest_patterns(question, mode="keyword", max_patterns=5)
print(result.to_markdown())
# → data_analyzer (0.91), synthesis_builder (0.74), question_analyzer (0.68)

# Mode 2: hybrid — keyword first, LLM reranks
result = suggest_patterns(question, mode="hybrid", llm_provider="openai")

# Mode 3: suggest full pipeline
chain_result = suggest_patterns(
    "How will rising interest rates affect our mortgage portfolio? What should we tell the board?",
    suggest_chain=True,
)
print(chain_result.suggested_chain)
# → ['question_analyzer', 'data_analyzer', 'risk_assessor', 'synthesis_builder', 'audience_adapter']
```

---

## Complexity routing: do you even need a template?

```python
from mycontext.intelligence import assess_complexity

# Simple → raw (no template overhead)
assess_complexity("What does HTTP 429 mean?").tier        # "raw"

# Analytical → single template
assess_complexity("What drives churn in B2B SaaS?").tier  # "single_template"

# Multi-angle → integrated pipeline
assess_complexity("Analyze Q3 pipeline, identify top 3 risk factors, recommend board response").tier
# "integrated"
```

The heuristic runs without an API key and takes under 1ms.

---

## Zero-config: `transform`

```python
from mycontext.intelligence import transform

# Input → right template → fully assembled Context
ctx = transform("Compare quarterly revenue trends across our three product lines")
print(ctx.metadata["template"])  # "data_analyzer"
print(ctx.assemble())             # Full structured prompt, ready to use
```

`transform` is `suggest_patterns` + `build_context` in one call. Use it when you want zero configuration.

---

## The full catalog

88 patterns across 6 categories — analysis, reasoning, creative, communication, planning, specialized (16 ship in the free tier; 72 require an enterprise license).

```python
from mycontext.intelligence.pattern_catalog import NAME_TO_CATEGORY
from collections import Counter

cats = Counter(NAME_TO_CATEGORY.values())
# Analysis: 18, Reasoning: 14, Creative: 12, Communication: 11, ...
```

---

## Try it

The companion notebook [`04_template_selection.ipynb`](04_template_selection.ipynb) demos all three suggestion modes, pipeline chaining, complexity routing, and the right-vs-wrong template quality comparison.

```bash
cd mycontext-cukbook
uv sync
cd content-series
uv run --directory .. jupyter notebook 04_template_selection.ipynb
```

**Next:** [10 — Pre-Flight Check Every Prompt Before It Costs You Tokens](04_quality_gates.md) — score your prompt before execution, score the output after, and put a hard number on how much templates help.
