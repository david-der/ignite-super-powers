---
name: turn-critique
description: Verify any UI-touching change with real turns — use the app through its real interface, screenshot into ./turn_results/<YYYYMMDD-HHMMSS-what>/, then READ every screenshot and critique it as a user before calling anything done. Use when asked to "do a turn", "turn and critique", "show me", "verify", or "memorialise a run".
---

# Turn and critique

A **turn** is one real use of the app the way a user would do it, through the real interface
(the browser, the real backend, real data, real model if there is one), ending in a screenshot of
what the user sees. A **critique** is reading that screenshot as the user, not the author, and
saying what is wrong. Turn → screenshot → read → critique → fix → repeat, in the same run folder,
until the pixels are right. Never claim a UI change works without turn evidence you have looked at.

## Where turns live

- `./turn_results/<YYYYMMDD-HHMMSS>[-suffix]/` at the project root, gitignored. One flat folder
  per run, images only, no subfolders, so a person opens one folder and scrolls.
- Files are numbered in the order taken: `01-login-empty.png`, `02-login-error.png`,
  `03-home-first-visit.png`. A folder reads top to bottom like a session.
- Group a session's turns with `export TURN_RUN_DIR="$(date +%Y%m%d-%H%M%S)-what-im-testing"`
  before the first turn. Every `just turn` in that shell lands in the same folder.
- Never write screenshots to `/tmp` or into the source tree. Past runs stay on disk; check
  earlier folders for "before" evidence before re-diagnosing.
- The mechanics of taking the screenshot are in the `screenshots` skill.

## Running a turn

```bash
just dev                                                  # the app, in the background or another tab
export TURN_RUN_DIR="$(date +%Y%m%d-%H%M%S)-add-game"
just turn 01-shelf-empty /                                # open a page, screenshot
just turn 02-add-form / "click:text=Add a game"           # do a step first
just turn 03-added / "fill:#title=Catan" "click:text=Save"
```

A scripted multi-step run (a Playwright spec) is also a turn; it must save its screenshots into
the same kind of folder with the same numbering.

## Critiquing

1. Read **every** PNG in the run folder with the Read tool. Do not sample. Look as the user.
2. Judge the experience, not the assertion: did it finish, is the state what a user expects, is
   anything blank, faded, clipped, duplicated, unstyled, overlapping, or off the edge at this
   viewport? Is the empty state understandable? Is an error visible when it should be?
3. Cross-check against ground truth where it exists: the server log, the database row, the API
   response. A pretty screenshot of wrong data is still wrong.
4. Write findings as one line each, concrete: "Save button clipped at 1280px", "two toasts after
   one click", "month header missing on first visit".
5. Each finding is fixed now, or explicitly accepted under **Known gaps** in `docs/PROGRESS.md`.
6. Fix, run the next turn in the SAME run folder, confirm in pixels.

## Memorialising

When a feature is verified, add to `docs/PROGRESS.md` next to the feature: the run folder name,
which screenshots to look at, and the critique findings (fixed or accepted). That pointer is how
the person who was not watching finds the evidence.

## Coverage trap

Scripts submit with Enter and never touch the mouse. Once per critique run, drive at least one
turn through the path a human uses: click the actual button, open the actual menu, use the phone
viewport if the product is for phones (`APP_VIEWPORT=390x844`).

## Report

End with: the run folder, the list of findings and what happened to each, and one sentence on
whether the feature is done. If it is not done, say so.
