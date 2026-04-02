# Pre-Flight Check Every Prompt Before It Costs You Tokens

**The problem.** Most LLM pipelines are flying blind. You write a prompt, send it, and only know it was bad after you've burned tokens and gotten a mediocre response. You iterate. You burn more tokens. You still don't know which part of the prompt was the problem.

**The pitch.** mycontext gives you three measurement layers: `QualityMetrics` scores your prompt *before* you call the model. `OutputEvaluator` scores the response *after*. `ContextAmplificationIndex` puts a hard ratio on how much better a cognitive template performs versus a raw prompt — so you're not guessing, you're measuring.

---

## Research grounding

The quality measurement system is grounded in three research threads. **Prompt quality as a pre-execution signal:** structural features of a prompt — specificity of rules, presence of output contract, grounding protocol, recency-zone task placement — correlate strongly with output quality (*Zhou et al. 2022 — "Large Language Models Are Human-Level Prompt Engineers"*). `QualityMetrics` heuristic mode operationalises these structural features into a fast, free scoring signal. **Output quality dimensions:** the seven dimensions in `OutputEvaluator` (groundedness, relevance, completeness, structure, register fit, specificity, evidence citation) are drawn from the RAGAS framework and adapted for general-purpose LLM evaluation. **CAI methodology:** the Context Amplification Index is mycontext's own benchmark protocol — two parallel LLM calls, identical question, templated vs. raw prompt, outputs scored on the same dimensions and compared as a ratio. CAI results across 200+ test cases average 1.8–2.3x on analytical questions.

---

## Three layers, one pipeline

```
[Prompt]  ──── QualityMetrics ────► score/100  (before LLM call, free)
    │
    ▼
[LLM call]
    │
[Output]  ──── OutputEvaluator ───► score/100  (after LLM call)

CAI = output_with_template / output_without_template  (the lift ratio)
```

---

## QualityMetrics: score before you spend

```python
from mycontext.intelligence import QualityMetrics

qm = QualityMetrics(mode="heuristic")  # No API key needed

raw_score = qm.evaluate(raw_ctx)           # Generic assistant prompt
improved_score = qm.evaluate(improved_ctx) # Manually tightened
templated_score = qm.evaluate(templated_ctx) # Template-driven

# Typical results for the same ML risk question:
# Raw (generic assistant):    34/100
# Manually improved:          68/100
# Template-driven (transform): 84/100
```

The heuristic runs in under 1ms — no API call, no token cost. Score every prompt before you send it.

From **mycontext-ai 0.11.0**, `QualityMetrics` also rewards prompts whose **`Constraints`** include quality-oriented fields: a non-empty **`self_check`**, an explicit **`verbosity`** level, and/or **`forbidden_phrases`** — each is a signal that the prompt shapes *how* the model answers, not only *what* it must discuss.

---

## Wire a quality gate into your pipeline

```python
QUALITY_THRESHOLD = 60

def gated_execute(ctx, threshold=QUALITY_THRESHOLD):
    score = QualityMetrics(mode="heuristic").evaluate(ctx)
    
    if score.overall < threshold:
        raise ValueError(
            f"Prompt quality {score.overall:.0f}/100 is below threshold {threshold}. "
            f"Fix the prompt before calling the model."
        )
    
    return ctx.execute(provider="openai", user=question)

# Raw prompt → raises ValueError: "34/100 is below threshold 60"
# Templated prompt → passes, executes
```

This pattern is standard in production pipelines. Catch bad prompts in CI, before they reach users.

---

## OutputEvaluator: seven dimensions of answer quality

```python
from mycontext.intelligence import OutputEvaluator

evaluator = OutputEvaluator(mode="heuristic")
output_score = evaluator.evaluate(ctx, llm_response)

# output_score.overall: 78/100
# Dimensions: groundedness, relevance, completeness, 
#             structure, register_fit, specificity, evidence_citation
```

The seven dimensions map to what actually matters in production: is the answer grounded in the prompt's constraints? Does it match the intended register? Is it complete? Use it after execution to flag responses that need human review.

When the **`Context`** defines **`constraints.forbidden_phrases`**, **`OutputEvaluator`** treats appearances of those phrases in the model output as a quality violation (lower score). That pairs naturally with Prompt Architect–suggested phrases or your own anti-boilerplate list.

---

## ContextAmplificationIndex: prove the template is worth it

CAI answers one question: *how much better is a templated prompt vs. a raw one, on the same question?*

```python
from mycontext.intelligence import ContextAmplificationIndex

cai = ContextAmplificationIndex()
result = cai.measure(
    question="What are the key risks of deploying ML in a regulated financial environment?",
    template_name="risk_assessor",
    provider="openai",
)

print(f"CAI: {result.cai_overall:.2f}x")  # e.g., "CAI: 2.1x"
print(result.verdict)
# "Strong amplification — risk_assessor provides 2.1x quality lift over raw prompt"
```

CAI calls the LLM twice — once with the template, once without — and compares the outputs across the same dimensions as `OutputEvaluator`. It's the number you put in a team presentation when someone asks "why should we use these templates?"

---

## The proof (what we measured)

For the question *"What are the key risks of deploying an ML model in a regulated financial environment?"*:

| Metric | Raw prompt | Template-driven |
|--------|-----------|-----------------|
| Prompt quality | 34/100 | 84/100 |
| Output quality (heuristic) | ~52/100 | ~81/100 |
| CAI | — | ~2.0x |

**+50 prompt quality points, +29 output quality points, 2x CAI lift** — all from template selection.

---

## Try it

The companion notebook [`05_quality_gates.ipynb`](05_quality_gates.ipynb) runs all three measurement layers, shows the gated execution pattern, and measures CAI live (API key required for CAI).

```bash
cd mycontext-cukbook
uv sync
cd content-series
uv run --directory .. jupyter notebook 05_quality_gates.ipynb
```

**Next:** [11 — Five Prompts in Five Minutes](05_multi_template_pipeline.md) — scale from single-template quality to a full five-step reasoning pipeline, automatically suggested, quality-gated at every step.
