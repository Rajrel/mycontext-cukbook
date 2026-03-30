# One Sentence In. Production Prompt Out.

**The problem.** You know you need a good system prompt. You've read the guides — add a role, add constraints, add examples, add a thinking strategy. But assembling nine sections from scratch is slow, inconsistent, and unmeasurable. Most teams ship the third iteration and call it good enough.

**The pitch.** `PromptArchitect.build(task)` takes a task description — as short as five words — and generates a complete, research-structured, linguistically-hardened 9-section prompt. Not a template. Not a fill-in-the-blank. A built artifact, with a score.

---

## The research behind the 9-section architecture

The section ordering is not a style choice — it follows **position-sensitivity findings** from LLM attention research:

| Zone | Sections (assembled prompt, `research_flow=True`, mycontext-ai ≥ 0.10.1) | Research basis |
|------|----------|---------------|
| **Primacy zone** | Role, Goal | Models weight early tokens heavily. The role and mission land first, anchor everything after. *Liu et al. 2023, "Lost in the Middle"* |
| **Instructions** | Rules, Style | Binding behavioral constraints and tone. Must use binding modals (must/never), never vague suggestions. |
| **Middle** | Examples (optional Knowledge) | Few-shot calibration before the output contract. *Li et al. 2025* |
| **Late** | Output Format, Guard Rails | CO-STAR framework: format specification and uncertainty handling before the final ask. *Singapore GovTech* |
| **Recency zone** | Reasoning (thinking strategy), then Task | Reasoning sits **immediately before YOUR TASK** so long prompts do not bury it; task stays last for highest recall. *Li et al. 2023* |

The rewriter JSON still uses the `SECTION_NAMES` field order (`role` … `task`); only the **rendered** section order moved reasoning next to the task in 0.10.1.

**The practical implication:** shuffling sections degrades performance. A prompt with the task in position 1 and the role in position 6 performs measurably worse than one with them in the correct zones — even with identical wording.

---

## The 5 atomic thinking strategies

`build()` selects thinking strategies in JSON; the assembled prompt renders them as **ANALYTICAL AND REPORTING APPROACH** / **REASONING APPROACH** in the **recency zone**, right before **YOUR TASK** (0.10.1+). Each strategy injects text that shapes how the model approaches the task:

| Strategy slug | Label | Research basis |
|--------------|-------|---------------|
| `step_by_step` | Chain of Thought | *Wei et al. 2022 (NeurIPS)* — 8-40% accuracy improvement on reasoning tasks |
| `multiple_angles` | Tree of Thought | *Yao et al. 2023* — explores branches before committing |
| `verify` | Self-Reflection | *Shinn et al. 2023 — Reflexion* — model critiques its own output |
| `explain_simply` | Simplification | Socratic pedagogy; forces concrete language over jargon |
| `creative` | Divergent Thinking | *Girotra et al. 2023* — structured divergence outperforms unguided brainstorming |

---

## The 5 named archetypes: validated strategy combinations

Single strategies are composable. The **archetypes** are pre-validated combinations for common task types — tested so you don't have to guess:

| Archetype | Strategies combined | Use when |
|-----------|-------------------|---------|
| `analytical` | step_by_step + verify | Data analysis, structured problem-solving, reports |
| `deliberative` | multiple_angles + step_by_step + verify | Strategy decisions, trade-offs, policy questions |
| `explanatory` | explain_simply + step_by_step | Documentation, tutorials, customer-facing copy |
| `creative` | creative + multiple_angles | Brainstorming, ideation, scenario planning |
| `high_stakes` | step_by_step + multiple_angles + verify | Medical/legal/financial, incident response, risk assessment |

---

## What it looks like

```python
from mycontext.intelligence import PromptArchitect

architect = PromptArchitect(provider="openai")

# Five words in
result = architect.build("Analyze customer support tickets")

# ~1500 words out
print(result.improved_context.assemble())
print(f"Score lift: {result.score_delta:+.0%} over baseline")
```

```python
# With explicit archetype
result = architect.build(
    "Assess the risk of deploying a new ML model in our fraud detection pipeline",
    reasoning_strategies=["deliberative"],  # expands to: multiple_angles + step_by_step + verify
)
```

---

## The 13 linguistic rules applied to every generated word

`build()` doesn't just fill nine slots — it applies 13 language rules distilled from prompt engineering research to every word it writes:

- **Binding modals** — "must", "never", "always" — never "should", "try", "ideally"
- **Specificity** — every rule must be objectively testable ("≥3 data points" not "be detailed")
- **Positive redirect** — never bare negation ("If absent, state: NOT FOUND" not "don't make things up")
- **Task last** — final instruction always in recency zone
- **Grounding protocol** — claims must be traceable to provided material
- **Anti-boilerplate** — suppresses "delve into", "it's no wonder", generic AI filler

The result is a prompt that reads like it was written by an experienced prompt engineer who also read the research.

---

## Try it

The companion notebook [`01_prompt_architect_auto.ipynb`](01_prompt_architect_auto.ipynb) runs the full demo: five-word input → 1500-character output, all five thinking strategies compared side-by-side, all five archetypes explained, and provider-aware rendering for OpenAI, Anthropic, and Gemini.

```bash
cd mycontext-cukbook
uv sync
cd content-series
uv run --directory .. jupyter notebook 01_prompt_architect_auto.ipynb
```

**Next:** [02 — Build the Prompt Yourself, Then Let the Architect Fix It](02_prompt_architect_improve.md) — manual construction, section-by-section diagnosis, and iterative improvement with before/after quality scores.
