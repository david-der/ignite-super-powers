---
name: new-project
description: Scaffold a new project the house way — folder under ~/workspace, justfile (setup/dev/test/turn), CLAUDE.md, docs/PLAN.md + PROGRESS.md + DECISIONS.md, .gitignore, turn_results/, git init, and a first turn. Use when starting any project or repo from scratch, or when asked to "start a new project called X".
---

# New project

Set it up so the loop works from the first hour: one command to start, one to test, one to
screenshot, and the three docs the owner reads when they come back. Do not add anything the plan
does not call for yet: no deploy, no auth, no CI on day one.

## 1. Ask or infer three things

- **Name**, kebab-case: `game-shelf`.
- **What it is, in three sentences**: what, for whom, what "done" means for the first version.
  If the owner gave them, use their words verbatim in `docs/PLAN.md`.
- **Shape**: a Python app (FastAPI + Jinja templates + Tailwind, SQLite), a Vite web app
  (TypeScript, React only if the UI has real state), or a script/CLI (uv, no UI). Default to the
  Python app when there is a UI and no strong reason otherwise.

## 2. Create the folder

```bash
mkdir -p ~/workspace/<name>/{docs,scripts} && cd ~/workspace/<name>
git init
```

Ports: pick 8000 for Python or 5173 for Vite unless another project in `~/workspace` already
uses it; write the choice in `CLAUDE.md`.

## 3. Files, in this order

### `.gitignore`

```
.venv/
__pycache__/
*.pyc
.pytest_cache/
node_modules/
dist/
tools/tailwindcss
*.db
*.db-wal
*.db-shm
.env
.DS_Store
*.log
# screenshots: one dated folder per run (turn-critique convention)
turn_results/
```

### `pyproject.toml` (Python shape)

```bash
uv init --name <name> --python 3.12
uv add fastapi "uvicorn[standard]" jinja2 python-multipart
uv add --dev pytest playwright
```

Vite shape instead: `pnpm create vite@latest . --template vanilla-ts` (or `react-ts`), then
`pnpm add -D @playwright/test tsx`.

### `justfile`

Python shape:

```just
# <name> — `just` lists recipes.
set shell := ["bash", "-euo", "pipefail", "-c"]

default:
    @just --list

# One-time: dependencies, the screenshot browser, the Tailwind binary
setup:
    uv sync
    uv run playwright install chromium
    mkdir -p tools
    test -x tools/tailwindcss || (curl -sL -o tools/tailwindcss https://github.com/tailwindlabs/tailwindcss/releases/latest/download/tailwindcss-macos-arm64 && chmod +x tools/tailwindcss)

# Start the app on :8000
dev: css
    uv run uvicorn app.main:app --reload --port 8000

# Build the stylesheet once / on change
css:
    tools/tailwindcss -i app/static/src/input.css -o app/static/css/site.css --minify
css-watch:
    tools/tailwindcss -i app/static/src/input.css -o app/static/css/site.css --watch

# Unit tests, no network
test:
    uv run pytest -q

# One real turn: open the running app, do the steps, screenshot into turn_results/$TURN_RUN_DIR/<label>.png
turn label="turn" path="/" *steps:
    uv run python scripts/turn.py {{label}} {{path}} {{steps}}
```

Vite shape: `setup` is `pnpm install && npx playwright install chromium`; `dev` is `pnpm dev
--port 5173`; `test` is `pnpm test`; `turn` is `npx tsx e2e/turn.ts {{label}} {{path}} {{steps}}`.

### `scripts/turn.py` (or `e2e/turn.ts`)

Copy it from the `screenshots` skill. Do not rewrite it.

### `app/static/src/input.css` (Python shape)

```css
@import "tailwindcss";
@source "../../templates";
```

### `CLAUDE.md`

```markdown
# <Name>

<The three sentences.> The spec is `docs/PLAN.md`; read it before product decisions.

## Stack

Python 3.12 / FastAPI / Jinja2 / SQLite, managed with `uv`. Tailwind v4 via the standalone CLI at
`tools/tailwindcss` (no node). Screenshots with Playwright (Python) into `turn_results/`.

## Commands (`just --list`)

- `just setup` — deps, Chromium, Tailwind binary
- `just dev` — app on :8000
- `just test` — pytest, no network
- `just turn <label> [path] [steps…]` — one turn → `turn_results/$TURN_RUN_DIR/<label>.png`

## Layout

- `app/main.py` routes · `app/repo.py` ALL SQL · `app/templates/` · `app/static/`
- `docs/` PLAN, PROGRESS (read first when returning), DECISIONS
- `scripts/turn.py` the screenshot driver

## Conventions

Every UI change is verified by turn and critique before "done". One commit per feature, named for
the feature. Deviations from the plan go in `docs/DECISIONS.md`. Blockers go under **Blocked** in
`docs/PROGRESS.md`; keep building.
```

### `docs/PLAN.md`

```markdown
# <Name> — plan

## What it is
<three sentences, the owner's words>

## Who it is for
<one line>

## Features, in priority order
1. <F1> — done when: <observable outcome a screenshot can show>
2. <F2> — done when: …
3. <F3> — done when: …

## Not now
<things deliberately left out of the first version>
```

### `docs/PROGRESS.md`

```markdown
# Progress

Read this first. Newest at the top.

## Blocked

- (nothing)

## Status

| Feature | Status | Verified by |
|---|---|---|
| F1 <name> | not started | — |
```

### `docs/DECISIONS.md`

```markdown
# Decisions

Every deviation from `docs/PLAN.md`, one dated line, with the reason.

- D1 (<date>): <what changed> — <why, usually what the installed library actually allows>.
```

## 4. Prove the scaffold

```bash
just setup
just dev &          # or in another tab
export TURN_RUN_DIR="$(date +%Y%m%d-%H%M%S)-scaffold"
just turn 01-hello
```

Read `turn_results/<run>/01-hello.png` with the Read tool. It should show the app's first page
with the stylesheet applied. If the page is unstyled, fix `css` before anything else.

## 5. First commit

```bash
git add -A && git commit -m "scaffold: <name> — justfile, CLAUDE.md, docs, turn driver"
```

Push only when the owner asks: `gh repo create <owner>/<name> --private --source . --push`.

## 6. Then build F1

Start the loop: build feature 1 from the plan, `turn-critique` it, commit with the feature name,
update `docs/PROGRESS.md` with the run folder. Report the run folder and what you saw.
