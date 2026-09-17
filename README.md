# Ignite Super Powers

This is how David Der builds software with Claude Code, written down so a brand-new person on a
brand-new Mac can pick it up in an afternoon. It is markdown only. Nothing here runs by itself;
you read it, Claude Code reads it, and together you follow it.

The idea in one sentence: **you own the intent and the taste, Claude proposes the plan and builds
it, and nothing counts as done until you have both seen it working from the outside, usually in a
screenshot.**

## Three steps

1. **Set up your Mac.** Follow `01-SETUP-MAC.md` top to bottom. About 30 minutes, mostly waiting
   for downloads. You will end up with a terminal that can run `claude`, `just`, `uv` and `node`.
2. **Install the superpowers.** Open Terminal, then paste these two lines:

   ```bash
   mkdir -p ~/.claude/skills && cp -R ~/workspace/ignite-super-powers/skills/* ~/.claude/skills/
   cp ~/workspace/ignite-super-powers/global/CLAUDE.md ~/.claude/CLAUDE.md
   ```

   That gives every Claude Code session your house rules and three skills. If you already have a
   `~/.claude/CLAUDE.md`, open it and paste the contents of `global/CLAUDE.md` at the end instead.
3. **Learn the loop.** Read `02-THE-LOOP.md`. It is the whole method. Then start your first
   project with `04-FIRST-PROJECT.md`.

If you would rather not paste anything: open Terminal, type `cd ~/workspace/ignite-super-powers`,
then `claude`, and say *"Read README.md and install the superpowers."* Claude will do step 2.

## What is in here

| File | What it is for |
|---|---|
| `01-SETUP-MAC.md` | From an empty Mac to a working Claude Code, in plain words |
| `02-THE-LOOP.md` | The agentic loop: plan, build, turn, screenshot, critique, fix, commit. Also memory and fresh eyes |
| `03-PREFERENCES.md` | The house stack (uv, Vite, Tailwind, just, SQLite) and why |
| `04-FIRST-PROJECT.md` | A 30-minute walkthrough of starting a project the house way |
| `global/CLAUDE.md` | The rules Claude reads at the start of every session |
| `skills/turn-critique/` | The most important skill: prove work from the outside with real turns and read the evidence |
| `skills/screenshots/` | How screenshots are taken and where they go (`turn_results/`) |
| `skills/new-project/` | Scaffold a project with justfile, CLAUDE.md, docs and the screenshot loop wired in |

## Words you will see

- **Claude Code** is a program in your terminal. You type what you want; it reads and writes files,
  runs commands, and shows you what it did.
- **A turn** is one real use of your app, the way a user would do it, ending in evidence of what
  the user saw. For an app in a browser, that is a screenshot.
- **A critique** is looking at that evidence as a user and saying what is wrong.
- **Context** is the chat's memory. It fills up and gets muddled. The project folder, with its docs,
  git history and screenshots, is the memory that lasts, so a fresh chat can pick up where the last
  one stopped.
- **A skill** is a folder with a `SKILL.md` that teaches Claude how to do one thing well.
- **A justfile** is a short menu of commands for a project. `just dev` starts it, `just test`
  tests it, `just turn` takes a screenshot. Every project has one, so you never have to remember
  long commands.

## License

MIT. Use it, change it, teach it.
