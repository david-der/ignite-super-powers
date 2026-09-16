# Preferences

The house stack. Claude picks these by default so that every project looks and works the same way
and you only have to learn one set of habits. Each choice has a reason; if a project needs
something else, that is a decision and goes in `docs/DECISIONS.md`.

## Tools

| Choice | Used for | Why |
|---|---|---|
| **just** | The command menu in every project (`justfile`) | `just dev`, `just test`, `just turn` are the same in every project. You never memorise long commands, and Claude never guesses them. |
| **uv** | All Python: installing it, dependencies, running scripts | One tool replaces Python installers, virtualenvs and pip. `uv run` always uses the right Python. `pyproject.toml` is the single list of dependencies. |
| **Vite + TypeScript** | Websites and browser apps | Fast dev server, one build command, types catch mistakes before a screenshot does. React when the UI has real state; plain TypeScript when it does not. |
| **Tailwind CSS v4, standalone CLI** | Styling | No CSS files to invent names for. The standalone binary lives at `tools/tailwindcss` (gitignored) so Python-only projects get Tailwind without installing node. |
| **FastAPI** | Python backends | Small, typed, and the docs page at `/docs` doubles as a smoke test. |
| **SQLite** | Data, until it graduates | One file, zero setup, backed up by copying. All SQL lives in one repository module so moving to Postgres later is one file's work. |
| **Playwright** | Turns and screenshots | Drives a real browser. Python flavour in Python projects, TypeScript flavour in Vite projects. Chromium only. |
| **git + GitHub, private** | History and backup | Commit per feature. Public only when you decide it is portfolio-worthy. |

## Project shape

Every project, whatever the stack, has this at the root:

```
CLAUDE.md          what it is, the commands, the conventions (Claude reads it first)
justfile           setup, dev, test, turn, and whatever else becomes real
docs/PLAN.md       the spec: what, for whom, features in priority order
docs/PROGRESS.md   status, newest first, with a Blocked section at the top
docs/DECISIONS.md  every deviation from the plan, one line each, dated
turn_results/      screenshots, one dated folder per run, gitignored
.gitignore         .venv, node_modules, tools/tailwindcss, turn_results/, .env, *.db
```

## Conventions

- **Deterministic logic goes in scripts.** If it computes, transforms, or renders, it is a script
  with a test, not instructions in prose. This applies to app code and to skills.
- **One data store per app.** Never a table shared across projects.
- **No SQL outside the repository module.** Keeps the exit to Postgres open.
- **Secrets live in `.env`**, which is gitignored. Never in code, never in a commit, never in a
  screenshot.
- **Commit per feature, named for the feature.** `E2.4 bridge: …` or `login: access-code gate`.
  No time estimates in commits or docs.
- **Ports are fixed per project** and written in `CLAUDE.md`, so two projects can run at once.
- **Costs stay near zero until earned.** Local first. Nothing is deployed until it has been used
  locally and screenshotted.
- **Screenshots go only to `turn_results/`.** Never `/tmp`, never inside the source tree, never
  committed.

## Justfile, minimum recipes

```just
default:
    @just --list

# One-time: dependencies and the screenshot browser
setup:
    uv sync
    uv run playwright install chromium

# Start the app (fixed port, written in CLAUDE.md)
dev:
    uv run uvicorn app.main:app --reload --port 8000

# Unit tests, no network
test:
    uv run pytest -q

# One real turn: open the app, do the steps, screenshot into turn_results/$TURN_RUN_DIR/<label>.png
turn label="turn" path="/":
    uv run python scripts/turn.py {{label}} {{path}}
```

The Vite equivalent swaps `uv run` for `pnpm` and `npx tsx`; the recipe names stay the same. See
`skills/new-project/SKILL.md` for the full templates.

## Tailwind without node

```bash
mkdir -p tools
curl -sL -o tools/tailwindcss https://github.com/tailwindlabs/tailwindcss/releases/latest/download/tailwindcss-macos-arm64
chmod +x tools/tailwindcss
```

Then `just css` runs `tools/tailwindcss -i src/input.css -o static/css/site.css --minify`, and
`src/input.css` starts with `@import "tailwindcss";` plus `@source` lines pointing at the
templates. `tools/tailwindcss` is gitignored; `just setup` downloads it.
