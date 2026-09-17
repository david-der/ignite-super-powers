# The loop

This is the method. Everything else in this folder exists to serve it.

You own the intent: what this is for, what matters first, what it must never do, and whether it
looks and feels right. Claude Code proposes the plan, owns most of the implementation details,
proves its work from the outside, judges it honestly, fixes what it finds, and writes down what
happened. You review, approve, and redirect. Neither of you gets to say "done" without evidence.

## One cycle

```
plan  →  build one feature  →  turn  →  screenshot  →  read  →  critique  →  fix  →  commit  →  log
                                  ↑                                            │
                                  └────────────── same run folder ─────────────┘
```

1. **Plan.** Before any code, a short plan in `docs/PLAN.md`: what the product is, who it is for,
   the features in priority order, what "done" looks like for each. You say what you want in a
   few sentences; Claude drafts the plan and you edit it until it says what you mean. The plan is
   the spec; when Claude deviates, it says so.
2. **Build one feature.** Not three. One feature with a name, from the plan.
3. **Turn.** Claude uses the app the way a real user would, through the real interface. It starts
   the app, clicks the button, types the message, uploads the file. The browser is the beginner
   case of a general rule: Claude proves work through the same boundary the user will use. A
   command-line tool is run from the shell, an API is called over the network, a script is run on
   real input. A test that only checks a function is not a turn.
4. **Screenshot.** Every turn ends in a screenshot of what the user sees (for a command-line tool
   or an API, the command and its captured output as a text file), saved into
   `turn_results/<YYYYMMDD-HHMMSS>-<what>/` at the project root. One folder per run, files in a
   flat list, numbered in the order they were taken. No subfolders, ever, so you open one folder
   and scroll.
5. **Read.** Claude opens every screenshot and looks at it. So do you. This is the step people
   skip and it is the whole point: a passing test says the code ran, a picture says it worked.
6. **Critique.** As the user, not the author. Did it finish? Is anything blank, clipped, faded,
   duplicated, unstyled, or misaligned? Is the state what a user would expect? Each finding is
   written down in one line.
7. **Fix.** Every finding is either fixed now or explicitly accepted and noted. Then another turn,
   in the same run folder, to confirm in pixels.
8. **Commit.** One git commit per feature, named for the feature. No squashing days of work into
   one commit; the history is the story of the build.
9. **Log.** `docs/PROGRESS.md` gets the feature's status and the name of the run folder that
   proved it. `docs/DECISIONS.md` gets any deviation from the plan and why.

Then the next feature.

## Rules that keep the loop honest

- **No claim without evidence from the outside.** "It works" means "here is what a user would
  see, and here is what I saw in it". For a UI that is a screenshot; for a command-line tool, the
  command and its output; for an API, the request and the response. Claude saying the code looks
  correct is not evidence, and a passing unit test on its own is not either.
- **Blockers do not stop the build.** If Claude cannot do something (a password, a login, a
  permission), it writes it under **Blocked** in `docs/PROGRESS.md` and moves to the next thing.
  You clear the blocked list when you come back.
- **Every deviation is written down.** The plan says X, the library only allows Y: Claude does Y
  and adds one line to `docs/DECISIONS.md` saying why. You read that file to learn what changed.
- **Verify the library before using it.** Packages change monthly. Claude reads the installed
  source, not its memory, before calling an API.
- **Logic goes in scripts, not prose.** Anything that computes a number or transforms a file is a
  script that can be run again and tested, never a paragraph of instructions.
- **Autonomy by default.** Claude makes the implementation decisions and does not ask permission
  for reversible things that follow from the plan. It asks only for destructive actions (deleting
  data, force-pushing, spending money) or genuine changes of scope. You can redirect at any time.
- **Prioritise the demo.** When two features are equally next, build the one you could show a
  friend. Rich, visible outcomes first: real data, a page you can open, a file you can download.

## What you do when you come back

You will step away for hours or days. When you return:

1. Open `docs/PROGRESS.md`. Newest status is at the top. Look at **Blocked** first.
2. Open the newest folder in `turn_results/`. Scroll the pictures top to bottom like a session.
3. Say what you think. "The table is too wide", "I don't understand the empty state", "ship it".

That is the whole review. You never have to read code to know where the project is.

## Two kinds of memory

The conversation with Claude is working memory. It is fast, and it fills up. When it gets long,
Claude starts to lose track of earlier decisions, repeats itself, or contradicts something it did
an hour ago. The project folder is the memory that lasts: `docs/PLAN.md` says what you are
building, `docs/PROGRESS.md` says where you are, `docs/DECISIONS.md` says what changed and why,
git says what happened, and `turn_results/` shows it. Together they let a brand-new session
rebuild the whole picture in a minute.

So do not keep one conversation alive forever. Start a fresh session at a natural boundary: after a
feature ships, when Claude seems confused, or when the answers get worse. Type `/exit`, then
`claude` again, and say *"Read docs/PROGRESS.md and continue."* If the loop has been kept honest,
nothing is lost. This is also why the log step is not optional: it is what makes the next session
possible.

## What to say to Claude

These phrases map directly to the skills and the loop:

| You say | What happens |
|---|---|
| *"Read docs/PROGRESS.md and continue"* | Claude picks up the next feature in the plan |
| *"Do a turn"* or *"turn and critique"* | Claude uses the app, screenshots it, reads the pictures, reports findings |
| *"Show me"* | Claude points you to the run folder and says what to look at |
| *"Memorialise this run"* | Claude names the run folder in PROGRESS.md next to the feature it proved |
| *"That's not done, look at the screenshot"* | Claude re-reads it as a user and lists what is wrong |
| *"Start a new project called X"* | Claude runs the new-project skill: folder, justfile, CLAUDE.md, docs, git |
| *"Ship it"* | Claude commits with the feature name and updates the docs |
| *"Review this with fresh eyes"* (in a new session) | Claude critiques the run folder against the plan with no memory of building it |

## Fresh eyes, when it matters

The Claude that built a feature has the whole build in its head, and that is exactly what makes it
a forgiving reviewer. For work that matters or is a matter of taste, especially UI, ask a second
session to look. Open a new terminal tab, run `claude` in the project, and say:

> Review the newest folder in turn_results/ against docs/PLAN.md as a first-time user. List what is
> wrong, most important first. Do not fix anything.

It has no memory of the shortcuts taken, so it sees what the builder stopped seeing. Take the list
back to the building session, or to a fresh one, to fix. That is the whole technique: two sessions
and one folder of pictures. No extra tools.

## Why it works

A model that builds and then grades its own work with the same eyes will pass itself. Forcing the
work through the boundary the user uses, and into a picture or a captured output, puts a second,
dumber, more honest check in the loop. A fresh session adds a third. And putting every run in a
dated folder means the evidence is always one click away for the person who was not watching.
