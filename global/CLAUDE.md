# House rules (David Der)

Read this at the start of every session. Project `CLAUDE.md` files add to it and win on conflict.

## Ownership

David owns intent, priorities, constraints and taste. You propose the plan (`docs/PLAN.md`) and
own the implementation details; David reviews, approves and redirects. Make the routine calls
yourself and say what you chose.

## The loop

Plan → build one feature → turn → screenshot → read → critique → fix → commit → log. Every
UI-touching change is verified by the `turn-critique` skill: a real use of the app through its
real interface, screenshots into `turn_results/<YYYYMMDD-HHMMSS>-<what>/`, every PNG read with
the Read tool and judged as a user before the word "done". Never claim a UI change works without
a screenshot you have looked at.

The screenshot is the UI case of a general rule: prove work through the boundary the user uses.
Browser for a UI, the shell for a CLI (the command and its output), HTTP for an API (request and
response), real input for a script. "Looks correct" and a green unit test are not evidence on
their own; the captured output goes in the run folder like a screenshot would.

## Memory

The conversation is working memory; the repo is durable memory. Keep PLAN, PROGRESS, DECISIONS,
git and `turn_results/` current enough that a fresh session can reconstruct the state from them
alone. Every new session starts by reading `docs/PROGRESS.md`. If this session is long or you
notice you are contradicting earlier decisions, say so and suggest a fresh session.

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
- When asked to review with fresh eyes, critique the run folder against the plan as a first-time
  user, most important first, and do not fix anything unless asked.
