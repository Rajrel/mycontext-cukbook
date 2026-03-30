# Five Prompts in Five Minutes: The Multi-Template Pipeline

**The problem.** A complex question needs multiple reasoning lenses — decomposition, analysis, risk assessment, synthesis, audience adaptation. Without mycontext, that means writing five separate system prompts, deciding which order they run in, and tuning each one to hand off cleanly to the next. That's a week of prompt engineering for one use case.

**The pitch.** `suggest_patterns(question, suggest_chain=True)` reads your question and recommends the full pipeline — which templates, in which order, with reasoning. `TemplateIntegratorAgent` merges all five into one unified prompt. Quality-gated at every step. Cross-framework export included.

---

## Research grounding

Multi-template pipelines are grounded in **cognitive decomposition** research: complex questions answered in a single pass are consistently shallower than those answered through structured sub-question decomposition (*Press et al. 2022 — "Measuring and Narrowing the Compositionality Gap"*; *Khot et al. 2022 — "Decomposed Prompting"*). The five-stage pipeline (decompose → analyze → assess risk → synthesize → adapt) maps each stage to a cognitively distinct operation, each handled by a specialist template optimized for that operation's structure. The `TemplateIntegratorAgent` then merges these into a single coherent prompt — producing a unified reasoning framework rather than five sequential calls — validated through mycontext's sprint research series showing integrated prompts outperform sequential pipelines on structured analytical tasks.

---

## The four things mycontext actually sells in multi-template scenarios

**1. Time to first good prompt.**
Writing one good system prompt = 10 minutes. Writing five for a pipeline, plus the glue = hours. All five, tested, parameterized, with depth controls, are in one `pip install`.

**2. Template selection intelligence — nobody else does this.**
`suggest_patterns(question, suggest_chain=True)` tells you which templates to use and in what order. A developer using plain LangChain has to decide themselves: "Should I decompose first? Do I need risk assessment?" We answer that.

**3. Quality gates before execution.**
`QualityMetrics.evaluate(ctx)` scores each prompt in the pipeline before anything gets sent to the model. You catch weak prompts before they cost tokens.

**4. Cross-framework portability.**
The integrated Context exports to LangChain, CrewAI, AutoGen, OpenAI, Anthropic — via `.to_crewai()`, `.to_langchain()`, etc. Write the pipeline once, deploy anywhere.

---

## The code

```python
from mycontext.intelligence import suggest_patterns, suggest_routes, TemplateIntegratorAgent

question = (
    "How will rising interest rates affect our mortgage portfolio risk "
    "over the next 12 months, and what should we communicate to the board?"
)

# Step 1: What pipeline do I need?
result = suggest_patterns(question, suggest_chain=True, mode="keyword")
print(result.suggested_chain)
# → ['question_analyzer', 'data_analyzer', 'risk_assessor', 'synthesis_builder', 'audience_adapter']

# Step 2: Multiple route options with reasoning
routes = suggest_routes(question, max_routes=3, provider="openai")

# Step 3: Merge all templates into one unified prompt
agent = TemplateIntegratorAgent()
integrated = agent.suggest_and_integrate(question, provider="openai", max_patterns=5)
ctx = integrated.to_context()

print(ctx.assemble())       # One unified 2000-token expert prompt
print(ctx.to_crewai())      # Same prompt, CrewAI agent format
print(ctx.to_langchain())   # Same prompt, LangChain messages
```

---

## Quality-gate every step

Before merging, score each template individually:

```python
from mycontext.intelligence import QualityMetrics, get_pattern_class

qm = QualityMetrics(mode="heuristic")
pipeline = ["question_analyzer", "risk_assessor", "synthesis_builder"]

for name in pipeline:
    ctx = get_pattern_class(name).build_context(question=question)
    score = qm.evaluate(ctx)
    gate = "PASS" if score.overall >= 60 else "BLOCK"
    print(f"{name}: {score.overall:.0f}/100 → {gate}")

# question_analyzer:  81/100 → PASS
# risk_assessor:      88/100 → PASS
# synthesis_builder:  79/100 → PASS
```

---

## The proof

Same complex financial question — raw single prompt vs. integrated five-template pipeline:

| Prompt | Quality Score |
|--------|--------------|
| Raw generic prompt | ~38/100 |
| Single best template | ~82/100 |
| Integrated 5-template pipeline | ~91/100 |

And that's just the prompt score. The output quality difference is where it shows up in practice: structured, multi-dimensional, evidence-based analysis vs. a flat three-paragraph summary.

---

## Try it

The companion notebook [`06_multi_template_pipeline.ipynb`](06_multi_template_pipeline.ipynb) demos all four steps: chain suggestion, route analysis, per-template quality gates, and integrated prompt export.

```bash
cd mycontext-cukbook
uv sync
cd content-series
uv run --directory .. jupyter notebook 06_multi_template_pipeline.ipynb
```

**Next:** [06 — Write Once, Deploy to LangChain, CrewAI, AutoGen, and Seven More](06_cross_framework.md) — take that integrated Context and deploy it to any agent framework without rewriting a line.
