# mycontext-cukbook



**mycontext-cukbook** is the standalone repository for the **mycontext capability series**: narrative notebooks, long-form articles, and web-app UI guides. Everything you need is under **`content-series/`** in **this** repo.

It is separate from the **mycontext** SDK (installed from PyPI as **`mycontext-ai`**). Older or duplicate copies of these notebooks may exist elsewhere; treat **this repository as canonical** when you file issues, record video, or publish to GitHub.

## Layout

| Path | Contents |
|------|----------|
| `content-series/*.ipynb` | Tutorial notebooks |
| `content-series/*.md` | Articles paired with each notebook |
| `content-series/ui-guides/` | No-code / product walkthroughs |
| (in each notebook, first code cell) | `load_dotenv`, `show` / `show_json`, `LLM_ENABLED` |
| `content-series/.env.example` | Template for API keys — copy to `.env` |

Detailed setup and troubleshooting: **[content-series/README.md](content-series/README.md)**.

## Quick start

Clone this repo anywhere you like. From **the repository root**:

```bash
cd mycontext-cukbook
uv sync
cd content-series
cp .env.example .env
# Edit .env: MC_SERIES_PROVIDER + matching API key
uv run --directory .. jupyter lab
```

On Windows PowerShell: `Copy-Item .env.example .env`

## Optional: editable `mycontext` SDK next to this repo

If you have a local checkout of the **mycontext** SDK as a sibling folder (`../mycontext`) and want an editable install instead of PyPI, add this block to the end of `pyproject.toml` and run `uv sync` again:

```toml
[tool.uv.sources]
mycontext-ai = { path = "../mycontext", editable = true }
```

The published default is **`mycontext-ai>=0.11.0`** from PyPI; bump the version in `dependencies` if you need a newer release.

## Repo name

The GitHub repository is **`mycontext-cukbook`**; the Python project name in `pyproject.toml` matches it.
