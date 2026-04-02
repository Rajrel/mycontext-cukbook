# mycontext Capability Series

Eight articles with companion notebooks — one per capability, from general to advanced. Each article follows the same formula: **the pain** users feel today, **the pitch** in one sentence, **the code** that proves it, and **the proof** in hard numbers.

The `ui-guides/` folder covers the same ideas for no-code users of the web app.

**Canonical copy:** this folder is the home for the series inside the **[mycontext-cukbook](../README.md)** repo. Use this tree for GitHub, videos, and `uv sync` — not a duplicate under another project.

---

## Quick start

1. **Python** — 3.12+.

2. **Install dependencies** — From the **repository root** `mycontext-cukbook` (parent of this folder):

   ```bash
   cd mycontext-cukbook
   uv sync
   ```

   That installs **`mycontext-ai`** from PyPI (see root `README.md` for an optional editable sibling checkout), Jupyter, `python-dotenv`, LiteLLM, and agent-related packages used in the cross-framework notebook. The repo pins **`mycontext-ai>=0.11.0`**.

3. **API keys** — In **`content-series/`**, copy the environment template and edit:

   ```bash
   cd content-series
   cp .env.example .env
   # Windows PowerShell: Copy-Item .env.example .env
   ```

   Open `.env` and set **`OPENAI_API_KEY`**. The setup cell calls **`load_dotenv()`**; keep `.env` next to the notebooks and run Jupyter with **`content-series`** as the working directory so the key is found.

4. **Start Jupyter** — **Use `content-series` as the working directory** so `.env` resolves next to the notebooks:

   ```bash
   cd mycontext-cukbook/content-series
   uv run --directory .. jupyter notebook
   ```

   Or Lab:

   ```bash
   uv run --directory .. jupyter lab
   ```

5. **Open a notebook** — Start with `01_prompt_architect_auto.ipynb` if you are new to the series.

---

## Notebook output (Markdown for recordings)

The setup cell defines **`md()`** (`display_markdown(..., raw=True)`). Use it for narrative output instead of `print` where you want readable markdown. If your editor shows “No renderer for text/markdown”, try **Notebook: Trusted** mode or update the Jupyter extension.

Model calls use **`provider="openai"`** and expect **`OPENAI_API_KEY`** in `.env`. Cells that only use heuristics, catalog browsing, or structural exports can run without a key.

---

## Code Series (SDK users)

Each article has a **Research grounding** section. Each notebook has a matching research cell where applicable.

| # | Article | Notebook | Capability | Audience |
|---|---------|----------|------------|----------|
| 01 | [Prompt Architect: Auto](01_prompt_architect_auto.md) | [01_prompt_architect_auto.ipynb](01_prompt_architect_auto.ipynb) | One sentence → 9-section production prompt; strategies and archetypes | General |
| 02 | [Prompt Architect: Improve](02_prompt_architect_improve.md) | [02_prompt_architect_improve.ipynb](02_prompt_architect_improve.ipynb) | Build manually, parse, improve, diff — v0 → v2 | General |
| 03 | [Smart Execute](03_smart_execute.md) | [03_smart_execute.ipynb](03_smart_execute.ipynb) | One function call from question to answer | General |
| 04 | [Template Selection](04_template_selection.md) | [04_template_selection.ipynb](04_template_selection.ipynb) | Let mycontext pick the right template | Intermediate |
| 05 | [Quality Gates](05_quality_gates.md) | [05_quality_gates.ipynb](05_quality_gates.ipynb) | Score prompts before execution, outputs after | Intermediate |
| 06 | [Multi-Template Pipeline](06_multi_template_pipeline.md) | [06_multi_template_pipeline.ipynb](06_multi_template_pipeline.ipynb) | Chaining templates and agents | Intermediate–Advanced |
| 07 | [Cross-Framework](07_cross_framework.md) | [07_cross_framework.ipynb](07_cross_framework.ipynb) | Export to LangChain, CrewAI, AutoGen, and more | Intermediate |
| 08 | [Executable Skills](08_executable_skills.md) | [08_executable_skills.ipynb](08_executable_skills.ipynb) | Executable `SKILL.md`, scoring, improvement loop | Advanced |

---

## UI Guides (web app — no code)

| # | Guide | Covers |
|---|-------|--------|
| 01 | [Pit stop / dashboard](ui-guides/01_pit_stop.md) | Getting oriented; Smart Execute style flows |
| 02 | [Context Studio: wizard & copilot](ui-guides/02_context_studio_wizard_and_copilot.md) | Guided 9-section builder |
| 03 | [Cognitive Studio & Data Analyzer](ui-guides/03_cognitive_studio_data_analyzer.md) | Templates, analyzer |
| 04 | [Chain Composer](ui-guides/04_chain_composer.md) | Visual multi-template pipelines |

---

## Repository layout

| Path | Role |
|------|------|
| `mycontext-cukbook/pyproject.toml` | Dependencies — run `uv sync` from repo root |
| `content-series/*.ipynb` | Tutorial notebooks |
| `content-series/*.md` | Long-form articles |
| `content-series/ui-guides/*.md` | Web-app guides |
| (inline in each `.ipynb`) | Setup cell: `load_dotenv`, `show` / `show_json`, `LLM_ENABLED` |
| `content-series/.env` | Your secrets (from `.env.example`; do not commit) |
| `content-series/_demo_skills/` | Sample `SKILL.md` files for notebook 08 |

---

## Troubleshooting

| Issue | What to do |
|-------|------------|
| Markdown outputs look like raw text | Trust the notebook or use a recent Jupyter UI; helpers use `display_markdown(..., raw=True)`. |
| `No module named 'mycontext'` | Run `uv sync` from `mycontext-cukbook` and use **`uv run --directory .. jupyter ...`** from `content-series`. |
| Editable `mycontext` fails | Ensure `../mycontext` exists and you added `[tool.uv.sources]` as in the root `README.md`, or remove that block to use PyPI only. |
| Model errors / empty responses | Confirm `OPENAI_API_KEY` in `.env`, working directory is `content-series`, restart the kernel. |

---

## Relation to other material

These notebooks are **story-driven tutorials**. For exhaustive API coverage of the SDK, use the main **mycontext** docs and any separate cookbook/reference notebooks you maintain elsewhere.
