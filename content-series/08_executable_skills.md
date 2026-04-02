# Your SKILL.md Is Just a Text File. Ours Executes, Measures, and Improves.

**The problem.** Claude Code's AGENTS.md and Cursor's `.cursor/rules` are static text the AI reads. They have no execution model, no parameter schema, no quality gate, no feedback loop. When a skill produces bad output, you find out from the bad output — not from a metric. There's no way to know if the skill is degrading over time.

**The pitch.** mycontext's SKILL.md is an executable artifact. Typed parameters are validated before execution. A cognitive template's reasoning structure is fused in via `pattern:`. `SkillRunner` scores the assembled prompt before calling the model and blocks if quality is too low. Run logs feed a health report that shows you skill quality trends and suggests edits.

---

## Research grounding

The four differentiators address known failure modes in static skill definitions. **Typed parameters:** parameter validation before execution is standard in software engineering; its absence in AI skill files creates the same class of bugs as untyped Python — silent failures with plausible-looking output. **Pattern fusion:** cognitive template application to custom skill bodies is backed by the same research as template selection — structured prompting frameworks improve reasoning quality for their target task type (*Brown et al. 2020 — few-shot prompting; mycontext CAI methodology*). **Quality gating:** pre-execution quality scoring catches structural prompt defects before token cost is incurred — backed by the prompt quality research cited in article 05. **Continuous improvement:** the feedback loop from execution logs follows the *Reflexion* paradigm (*Shinn et al. 2023*) — systematic error analysis from outputs, fed back as targeted edits, producing measurable quality improvement over successive runs.

---

## The four differentiators

**1. Typed parameters — validated, not hoped for.**

Claude/Cursor skills use free text: "Review the code, focus on security." mycontext skills define an `input_schema`:

```yaml
---
name: code_security_check
description: Review code for security vulnerabilities
input_schema:
  language: str
  depth: str
  focus_area: str
pattern: root_cause_analyzer
---

Review the {language} code with {depth} depth. Focus on: {focus_area}.
```

`SkillRunner` validates that `language`, `depth`, and `focus_area` are provided and are strings before executing. Missing param? `ValueError`. Wrong type? `ValueError`. No surprises in production.

---

**2. Pattern fusion — reasoning structure baked in.**

The `pattern: root_cause_analyzer` line fuses the skill body with a cognitive template's reasoning framework. The output isn't just your instructions — it's your instructions wrapped in root cause analysis methodology. No other SKILL.md format supports this.

```python
fused_skill.pattern  # "root_cause_analyzer"
# SkillRunner merges skill body + root_cause_analyzer template
# Result: structured RCA reasoning applied to every code review
```

Fused skills inherit each template’s **default quality scaffolding** where defined — many free patterns include **`self_check`** prompts on `Constraints`, which feeds the same pre-flight scoring story as **Prompt Architect** and **`QualityMetrics`** (0.11.0+).

---

**3. Quality-gated execution — block before you burn tokens.**

```python
from pathlib import Path
from mycontext import SkillRunner

runner = SkillRunner()
# First argument must be a Path to the skill directory (or SKILL.md); runner loads it.
result = runner.run(
    Path("./my_skill"),  # directory containing SKILL.md
    task="Review this code for SQL injection",
    execute=False,          # check quality first
    quality_threshold=0.7,  # only enforced when execute=True
    language="Python",
    depth="thorough",
    focus_area="SQL injection and authentication",
)

print(result.quality_score.overall)  # e.g. 0.82
print(result.gated)                  # False when execute=False
```

A raw SKILL.md with vague instructions might score 0.41 — blocked before a token is spent.

---

**4. Continuous improvement from run logs.**

```python
runner = SkillRunner(log_runs=True, log_path=Path("skill_log.jsonl"))

# After 10 runs:
from mycontext.skills.improvement import skill_health_report, improvement_report

health = skill_health_report(log_path=Path("skill_log.jsonl"))
# Shows: average quality score, trend (improving/degrading), flagged runs

# Per-run feedback:
report = improvement_report(result)
# Shows: issues, strengths, specific suggested edits
```

No other SKILL.md format — Claude's, Cursor's, or anyone else's — has a feedback loop from execution.

---

## The quality comparison

We ran three versions of the same skill — basic (plain text), typed (with schema), fused (with pattern) — through `SkillRunner` with `execute=False` and measured assembled prompt quality:

| Skill version | Quality Score | Gate at 0.65 |
|--------------|--------------|--------------|
| Basic (plain text, no schema) | ~0.42 | BLOCKED |
| Typed (input_schema, no pattern) | ~0.67 | PASS |
| Fused (input_schema + pattern) | ~0.84 | PASS |

**A pattern-fused skill is 2x the quality of a plain-text skill** — before the model is even called.

---

## Try it

The companion notebook [`08_executable_skills.ipynb`](08_executable_skills.ipynb) builds all three skill versions from scratch, runs them through `SkillRunner`, compares quality scores, and demonstrates the health report pipeline.

```bash
cd mycontext-cukbook
uv sync
cd content-series
uv run --directory .. jupyter notebook 08_executable_skills.ipynb
```

---

This is the final article in the series. The full index is in [README.md](README.md).
