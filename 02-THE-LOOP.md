# The loop

This is the method. Everything else in this folder exists to serve it.

You are the product owner. Claude Code is the builder. Your job is to say what you want, to have
taste, and to look at the pictures. Claude's job is to build it, prove it with screenshots, judge
its own work honestly, fix what it finds, and write down what happened. Neither of you gets to
say "done" without evidence.

## One cycle

```
plan  →  build one feature  →  turn  →  screenshot  →  read  →  critique  →  fix  →  commit  →  log
                                  ↑                                            │
                                  └────────────── same run folder ─────────────┘
```

1. **Plan.** Before any code, a short plan in `docs/PLAN.md`: what the product is, who it is for,
   the features in priority order, what "done" looks like for each. You write this, or you talk
   and Claude writes it and you edit it. The plan is the spec; when Claude deviates, it says so.
2. **Build one feature.** Not three. One feature with a name, from the plan.
3. **Turn.** Claude uses the app the way a real user would, through the real interface. It starts
   the app, clicks the button, types the message, uploads the file. A test that only checks a
   function is not a turn.
4. **Screenshot.** Every turn ends in a screenshot of what the user sees, saved into
   `turn_results/<YYYYMMDD-HHMMSS>-<what>/` at the project root. One folder per run, images in a
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

- **No claim without a picture.** "It works" means "here is the screenshot and here is what I saw
  in it". A UI change verified only by tests is not verified.
- **Blockers do not stop the build.** If Claude cannot do something (a password, a login, a
  permission), it writes it under **Blocked** in `docs/PROGRESS.md` and moves to the next thing.
  You clear the blocked list when you come back.
- **Every deviation is written down.** The plan says X, the library only allows Y: Claude does Y
  and adds one line to `docs/DECISIONS.md` saying why. You read that file to learn what changed.
- **Verify the library before using it.** Packages change monthly. Claude reads the installed
  source, not its memory, before calling an API.
- **Logic goes in scripts, not prose.** Anything that computes a number or transforms a file is a
  script that can be run again and tested, never a paragraph of instructions.
- **Autonomy by default.** Claude does not ask permission for reversible things that follow from
  the plan. It asks only for destructive actions (deleting data, force-pushing, spending money) or
  genuine changes of scope.
- **Prioritise the demo.** When two features are equally next, build the one you could show a
  friend. Rich, visible outcomes first: real data, a page you can open, a file you can download.

## What you do when you come back

You will step away for hours or days. When you return:

1. Open `docs/PROGRESS.md`. Newest status is at the top. Look at **Blocked** first.
2. Open the newest folder in `turn_results/`. Scroll the pictures top to bottom like a session.
3. Say what you think. "The table is too wide", "I don't understand the empty state", "ship it".

That is the whole review. You never have to read code to know where the project is.

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

## Why it works

A model that builds and then grades its own work with the same eyes will pass itself. Forcing the
work through the real interface and into a picture puts a second, dumber, more honest check in the
loop: pixels. And putting every run in a dated folder means the evidence is always one click away
for the person who was not watching.
