# House rules (David Der)

Read this at the start of every session. Project `CLAUDE.md` files add to it and win on conflict.

## The loop

Plan → build one feature → turn → screenshot → read → critique → fix → commit → log. Every
UI-touching change is verified by the `turn-critique` skill: a real use of the app through its
real interface, screenshots into `turn_results/<YYYYMMDD-HHMMSS>-<what>/`, every PNG read with
the Read tool and judged as a user before the word "done". Never claim a UI change works without
a screenshot you have looked at.

## Where evidence lives

- `turn_results/` at the project root, gitignored. One flat folder per run, images only, numbered
  `NN-<feature>-<state>.png` in the order taken. No subfolders. Never `/tmp`, never the source
  tree. Past runs stay on disk as "before" evidence.
- `docs/PROGRESS.md`: status newest first, **Blocked** at the top, and the run folder that proved
  each feature. David reads this first when he returns; keep it true.
- `docs/DECISIONS.md`: every deviation from `docs/PLAN.md`, one dated line, with the reason
  (usually what the installed library actually allows).

## Stack defaults

Python → `uv` + `pyproject.toml`, FastAPI, SQLite with all SQL in one repository module. Web →
Vite + TypeScript (React only when the UI has real state). Styling → Tailwind v4 via the
standalone CLI at `tools/tailwindcss` (gitignored). Browser work → Playwright, Chromium only.
Every project has a `justfile` with at least `setup`, `dev`, `test`, `turn`; prefer `just`
recipes over raw commands. New projects use the `new-project` skill.

## How to work

- Build in the plan's priority order, but prefer the feature you could demo to a friend.
- One commit per feature, named for the feature. No squashing, no time estimates anywhere.
- Blockers go under **Blocked** in PROGRESS.md; then keep building. Do not stall.
- Verify library APIs against the installed source before using them; packages drift monthly.
- Deterministic logic goes in scripts with tests, not in prose. In apps and in skills.
- Secrets live in gitignored `.env`. Never print, commit, or screenshot them.
- Act without asking for reversible work that follows from the plan. Ask only before destructive
  actions, spending money, or a real change of scope.
- When David says "do a turn", "turn and critique", "show me", or "memorialise", run the
  `turn-critique` skill.
