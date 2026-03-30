# Write Once, Deploy to LangChain, CrewAI, AutoGen, and Seven More

**The problem.** You built a great prompt for LangChain. Now your team wants to try CrewAI. Your prompt is in LangChain's messages format. CrewAI wants role, goal, backstory, and expected_output. That's not a migration — that's a rewrite. Every framework has its own format, and you pay the rewriting tax every time you switch.

**The pitch.** One `Context` object. Ten framework exports. One method call each. Switch from LangChain to CrewAI without touching the prompt — just call `.to_crewai()` instead of `.to_langchain()`.

---

## Research grounding

The abstraction layer design is grounded in the **portability-performance** finding from mycontext's cross-framework evaluation: the same semantic prompt, adapted to each framework's native format, produces statistically equivalent output quality across OpenAI, Anthropic, CrewAI, and AutoGen — measured via `OutputEvaluator`. The provider-aware rendering in `Context.assemble_for_model()` additionally applies **provider-specific formatting research**: Anthropic performs better with XML delimiters and positive constraint reframes; Gemini benefits from explicit trait adjectives on the role and verbosity anchors; OpenAI benefits from instruction mirroring for long knowledge blocks (*mycontext provider rendering research, 2026-03-16*). Framework portability is therefore not just convenience — it's structure-matching each provider's actual attention behaviour.

---

## The export menu

Every mycontext `Context` supports all of these out of the box:

```python
from mycontext.intelligence import get_pattern_class

ctx = get_pattern_class("code_reviewer").build_context(
    question="Review this code for security vulnerabilities",
    code=sample_code,
    language="python",
)

# Raw formats — no dependencies required
ctx.to_messages()      # [{"role": "system", "content": "..."}, ...]
ctx.to_openai()        # {"messages": [...], "model": ..., "temperature": ...}
ctx.to_anthropic()     # {"system": "...", "messages": [...]}
ctx.to_google()        # {"contents": [...], "generation_config": {...}}
ctx.to_langchain()     # {"system_message": "...", "human_message": "..."}
ctx.to_llamaindex()    # {"system_prompt": "...", "query_instruction": "..."}
ctx.to_crewai()        # {"role": "...", "goal": "...", "backstory": "...", "expected_output": "..."}
ctx.to_autogen()       # {"system_message": "...", "name": ...}
ctx.to_prompt()        # plain string (optionally LLM-refined)
ctx.to_markdown()      # human-readable
```

---

## Full agent creation, not just prompt strings

The integration helpers go further — they don't just format the prompt, they create full agent objects:

```python
from mycontext.integrations.helpers import CrewAIHelper, AutoGenHelper, LangChainHelper

# CrewAI: full agent configuration dict
agent_cfg = CrewAIHelper.create_agent(ctx, name="SecurityReviewer", tools=[...])

# AutoGen: assistant agent configuration
autogen_cfg = AutoGenHelper.create_assistant(ctx, name="SecurityReviewAgent", llm_config={...})

# LangChain: prompt messages with optional human message
msgs = LangChainHelper.to_messages(ctx, user_message="Review the code above")
```

---

## String dispatch when you don't know the framework at import time

```python
from mycontext.integrations.helpers import auto_integrate

# Framework determined at runtime
framework = os.environ.get("AGENT_FRAMEWORK", "openai")
result = auto_integrate(ctx, framework)
```

Useful for multi-tenant apps where different customers use different frameworks.

---

## The migration story

You built your prompt library in LangChain. You want to benchmark CrewAI. Without mycontext, that's a full rewrite of every prompt. With mycontext:

```python
# Week 1: LangChain
for template_name in my_templates:
    ctx = get_pattern_class(template_name).build_context(...)
    lc_prompt = ctx.to_langchain()

# Week 2: try CrewAI — zero prompt changes
    crew_cfg = ctx.to_crewai()

# Week 3: benchmark Anthropic Claude — zero prompt changes
    anthropic_fmt = ctx.to_anthropic()
```

The prompt is the same. The quality score is the same. Only the output format changes.

---

## Try it

The companion notebook [`07_cross_framework.ipynb`](07_cross_framework.ipynb) shows all 10 export methods, all three integration helpers, and `auto_integrate` — with zero API key required.

```bash
cd mycontext-cukbook
uv sync
cd content-series
uv run --directory .. jupyter notebook 07_cross_framework.ipynb
```

**Next:** [07 — Your SKILL.md Is Just a Text File — Ours Executes, Measures, and Improves](07_executable_skills.md) — the executable agent skill format with quality gates, pattern fusion, and a continuous improvement pipeline.
