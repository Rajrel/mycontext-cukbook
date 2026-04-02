# Build the Prompt Yourself. Then Let the Architect Fix It.

**The problem.** You have an existing prompt — or you've written a new one from scratch. You've iterated a few times. It feels okay. But "feels okay" is not a measurement. You don't know which sections are weak, you don't know what to fix first, and rewriting the whole thing from scratch would throw away the parts that are already good.

**The pitch.** `parse()` maps your prompt to the 9-section architecture and scores each section. `improve()` rewrites only the sections that are below threshold — it doesn't touch the rest. `diff_report()` shows you the exact changes with reasoning. `GuidanceOptimizer` upgrades every vague "try to be accurate" into a binding, testable constraint. You go from v0 to production-grade in one iteration.

---

## The research behind surgical improvement

Two findings shaped how `improve()` works:

**Targeted edits outperform full rewrites.** *Pryzant et al. 2023 — "Automatic Prompt Optimization with 'Gradient Descent'"* showed that iterative, section-specific edits consistently outperform wholesale rewrites. `improve()` implements this: it parses, identifies weak sections, rewrites those only, and leaves strong sections untouched.

**Binding modals produce measurably better constraint following.** *Mizrahi et al. 2023 — "State of What Art?"* showed that "must" and "never" produce significantly more consistent behavior than "should" and "try to". `GuidanceOptimizer` applies this directly — it rewrites every hedged rule into a binding, testable constraint.

---

## What `parse()` exposes

Most prompts have 2–3 sections out of 9. `parse()` makes the gaps visible:

```python
from mycontext.intelligence import PromptArchitect

architect = PromptArchitect(provider="openai")
parsed = architect.parse("You are a helpful assistant. Analyze customer support tickets. Be accurate.")

# Shows:
# role:            ✓ (weak — "helpful assistant" has no expertise scope)
# goal:            ✗ MISSING
# rules:           ~ (vague — "be accurate" uses no binding modal)
# reasoning:       ✗ MISSING — no thinking strategy injected
# examples:        ✗ MISSING
# output_contract: ✗ MISSING — no format specified
# guard_rails:     ✗ MISSING — no fallback for uncertainty
# task:            ~ (merged with role — should be last, in recency zone)
```

---

## The manual upgrade path

Once you know what's missing, you build it properly:

```python
from mycontext import Context, Guidance, Directive, Constraints

ctx = Context(
    guidance=Guidance(
        role="Senior customer intelligence analyst specialising in support ticket analysis",
        goal="Surface actionable patterns that product and ops teams can act on",
        rules=[
            # Binding modals replacing 'try to be accurate'
            "Must classify each ticket by issue type: billing, technical, UX, policy, other",
            "Must assign sentiment (positive/neutral/negative) with a one-sentence evidence quote",
            # Positive redirect replacing 'avoid making assumptions'
            "If insufficient data to classify, state: 'INSUFFICIENT DATA — [missing field]'",
        ],
        style="Precise, structured, written for a product manager audience",
    ),
    directive=Directive(content="Analyze the support tickets below"),
    constraints=Constraints(
        format_rules=["Return a JSON array — one object per ticket"],
    ),
    thinking_strategy="verify",   # Self-reflection: review before returning
    research_flow=True,
)
```

Score it immediately — before sending to any model:

```python
from mycontext.intelligence import QualityMetrics

qm = QualityMetrics(mode="heuristic")
print(qm.evaluate(ctx).overall)  # ~78/100 vs ~32/100 for the original
```

---

## `improve()`: the architect takes over what's still weak

```python
improved = architect.improve("You are a helpful assistant. Analyze tickets. Be accurate.")

print(improved.diff_report())
# === Section: rules ===
# BEFORE: "Be accurate and helpful"
# AFTER:  "Must cite the specific ticket field supporting each classification.
#          Must not infer issue type from sentiment alone."
# Change: Binding modal (be→must), specificity added (cite field), compound split
```

`improve()` is not a rewrite — it's a targeted repair. Strong sections survive.

### Quality controls on the improved context (0.11.0+)

After `improve()`, check `improved.improved_context.constraints` for the same optional quality fields as in `build()`: **`verbosity`**, **`communication_posture`**, **`answer_first`**, **`forbidden_phrases`**, and **`self_check`**. They are suggested by the architect when the model returns them, rendered via **`Constraints.render()`**, and you can overwrite any of them before execution.

---

## GuidanceOptimizer: the rules specialist

```python
from mycontext.intelligence import GuidanceOptimizer
from mycontext import Guidance

guidance = Guidance(role="Analyst", rules=[
    "Try to be helpful",
    "Avoid making things up",
    "Use good judgment when information is missing",
])

optimizer = GuidanceOptimizer()
audit = optimizer.audit(guidance)     # Heuristic, instant, no API key

# Flags: 3/3 rules use hedged language, 0/3 are testable, 0/3 have fallback phrases

optimized = optimizer.optimize(guidance, provider="openai")
for rule in optimized.guidance.rules:
    print(f"  ✓ {rule}")
# ✓ Must address all stated questions before returning.
# ✓ Every claim must be grounded in the provided material. If absent, state: 'NOT IN SOURCE'.
# ✓ If any required field is missing, state which field is missing and what assumption would be needed.
```

---

## The before/after proof

Three versions of the same prompt — the original, manually upgraded, and architect-improved:

| Version | Quality Score |
|---------|--------------|
| v0 — original ("helpful assistant, be accurate") | ~32/100 |
| v1 — manual upgrade (binding rules, strategy, output contract) | ~76/100 |
| v2 — `improve()` applied to v0 | ~81/100 |

**+49 points from diagnosis and targeted repair.** The entire process — parse, improve, score — takes under 30 seconds.

---

## Try it

The companion notebook [`02_prompt_architect_improve.ipynb`](02_prompt_architect_improve.ipynb) walks through the full v0→v1→v2 journey with a realistic support-ticket analysis prompt, shows every diff, and prints the final comparison table.

```bash
cd mycontext-cukbook
uv sync
cd content-series
uv run --directory .. jupyter notebook 02_prompt_architect_improve.ipynb
```

**Next:** [03 — Smart Execute: One Function Call from Question to Answer](03_smart_execute.md) — when you don't want to build or improve manually and just want the best answer in one call.
