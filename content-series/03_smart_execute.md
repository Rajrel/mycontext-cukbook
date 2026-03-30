# Smart Execute: One Function Call from Question to Answer

**The problem.** You have a business question. You open ChatGPT and get a generic, surface-level answer. You try to write a better prompt — but prompt engineering is a skill you didn't sign up to learn. You just want a good answer.

**The pitch.** `smart_execute(question)` routes your question through mycontext's intelligence layer — assesses complexity, picks the right cognitive template from 85 options, assembles an expert-grade prompt, calls the model — and returns the response plus a full audit trail of what happened. You type one line. We make all the decisions.

---

## Research grounding

The three-tier routing model reflects two research findings. First, **task decomposition before generation** — routing a complex question through multiple reasoning templates produces significantly more structured outputs than a single-pass prompt (*Khot et al. 2022 — "Decomposed Prompting"*). Second, the **CAI (Context Amplification Index)** benchmark — mycontext's own evaluation methodology — shows that template-driven prompts produce 1.8–2.5x better outputs than raw prompts on analytical questions, while offering no benefit on simple factual queries. The three tiers exist to apply that lift only where it's warranted, keeping simple queries fast and cheap.

---

## The three tiers

Under the hood, every call to `smart_execute` goes through three decision gates:

**Tier 1 — Raw.** Simple, factual, or conversational questions. We send a clean direct prompt — no template overhead, fast and efficient.

**Tier 2 — Single template.** Analytical questions needing structured reasoning. We pick the best cognitive pattern from our catalog of 85 templates — root cause analysis, data analysis, risk assessment, synthesis, and more — and build a full structured prompt.

**Tier 3 — Integrated pipeline.** Multi-angle, complex questions. We chain 2–5 templates, integrate them into one unified prompt. Your board-deck question gets decomposed, analyzed, risk-assessed, synthesized, and audience-adapted — automatically.

The routing algorithm is heuristic-first (instant, no API cost), LLM-assisted for edge cases.

---

## The code

```python
from mycontext.intelligence import smart_execute, smart_prompt, assess_complexity

# Zero decisions: just ask
response, meta = smart_execute(
    "We're considering acquiring a Series B fintech. Analyze strategic fit, key risks, "
    "and what our integration roadmap should look like for a board presentation.",
    provider="openai"
)

print(response)
print(meta)
# meta: {"tier": "integrated", "templates_used": ["question_analyzer", "risk_assessor",
#         "synthesis_builder", "audience_adapter"], "quality_score": 87, ...}
```

The `meta` dict is the audit trail. It tells you which tier was used, which templates were selected, the assembled prompt's quality score, and token usage — so you can understand, reproduce, and improve every call.

---

## See the prompt before you pay for it

Sometimes you want to inspect the assembled prompt before sending it to the model. `smart_prompt` does all the routing and assembly — but stops before the LLM call:

```python
composed = smart_prompt("What risks should we consider before launching an AI feature in a regulated industry?")

print(composed.to_string())       # Full assembled prompt
print(composed.to_messages())     # OpenAI messages format
```

This is useful for debugging, cost estimation, or feeding the assembled prompt into your own pipeline.

---

## The proof

We ran the same question — *"What risks should we consider before launching an AI feature in a regulated industry?"* — through a raw generic assistant prompt versus `smart_execute`. Quality was measured with `QualityMetrics(mode="heuristic")` before any LLM call:

| Path | Quality Score |
|------|--------------|
| Raw generic assistant prompt | ~38/100 |
| `smart_execute` (auto-routed to `risk_assessor`) | ~84/100 |

That's a **+46 point lift from automatic template selection** — before a single token is sent to the model.

---

## When to use what

| You want | Use |
|----------|-----|
| The fastest path to an answer | `smart_execute(question, provider=...)` |
| To see the prompt before spending tokens | `smart_prompt(question)` |
| A fast heuristic prompt with no compile cost | `smart_generic_prompt(question)` |
| To understand the routing logic | `assess_complexity(question)` |

---

## Try it

The companion notebook [`03_smart_execute.ipynb`](03_smart_execute.ipynb) runs all three tiers end-to-end, shows the meta audit trail for each, and compares raw vs. templated prompt quality — with and without an API key.

```bash
cd mycontext-cukbook
uv sync
cd content-series
uv run --directory .. jupyter notebook 03_smart_execute.ipynb
```

**Next:** [08 — Build a Production Prompt in 60 Seconds](02_prompt_architect.md) — when you want to build, parse, or improve a prompt yourself rather than letting smart routing choose for you.
