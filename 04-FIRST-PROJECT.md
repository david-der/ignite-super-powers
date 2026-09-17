# Your first project, in 30 minutes

You have done `01-SETUP-MAC.md` and installed the superpowers from the README. This walks through
one real project so the loop stops being a diagram.

## 1. Decide what it is, in three sentences

Write them down before opening the terminal. For example:

> A page that lists my board games and lets me mark which ones we played this month. For me and my
> family on our phones. Done when I can add a game, mark it played, and see the month's list.

That is the intent. Claude will propose a plan from it in `docs/PLAN.md`: your sentences at the
top, then the features in an order, with what "done" means for each one. Read it and change what
is wrong before the build gets far.

## 2. Start Claude Code in your workspace

```bash
cd ~/workspace
claude
```

## 3. Say this

> Start a new project called game-shelf. It is: [paste your three sentences]. Draft the plan,
> then build the first feature from it, do a turn, and show me.

Then wait. Claude will:

- make `~/workspace/game-shelf` with the justfile, `CLAUDE.md`, the three docs, and git;
- draft `docs/PLAN.md` from your sentences, in the order it thinks is right (you can change it);
- run `just setup`, which downloads the screenshot browser (this is the slow part);
- build the first feature;
- run `just turn`, which starts the app, uses it, and saves screenshots to a dated folder in
  `turn_results/`;
- read the screenshots and tell you what it saw and what it fixed;
- commit.

## 4. Look at the pictures

In Finder, open `~/workspace/game-shelf/turn_results/`. Open the newest folder. Press space on the
first image and use the arrow keys to walk through them in order.

Say what you think, in plain words. "The add button is tiny on my phone." "I want the month at
the top." "Good, next feature." Every sentence you say is either a critique (goes back into the
loop) or a plan change (Claude updates `docs/PLAN.md` and continues).

## 5. Leave and come back

Close the laptop. Tomorrow:

```bash
cd ~/workspace/game-shelf
claude
```

> Read docs/PROGRESS.md and continue.

Claude reads where it left off, including anything under **Blocked**, and picks up the next
feature. You read `docs/PROGRESS.md` and the newest `turn_results/` folder. That is the whole
rhythm.

The new session starts with an empty memory, and that is fine: the docs and the pictures are the
memory. Start a fresh session whenever the old one gets long or confused, not only tomorrow.

## 6. When you want it online

Not yet. Use it locally until the screenshots look like something you would show a friend. Then
say *"I want this online"* and Claude will propose the smallest way to do that for this project,
and write the choice in `docs/DECISIONS.md`.

## Things that are normal

- The first `just setup` takes a few minutes and prints a lot. It is downloading a browser.
- Claude asks for permission the first time it runs some commands. Say yes to things that read or
  build; ask it to explain anything that deletes.
- Turns and screenshots cost nothing extra; Claude's work is covered by your Claude plan. Only an
  app that itself uses AI (a chatbot, say) would add a few cents per turn.
- Claude will sometimes say "this is not done, the screenshot shows X". That is the loop working.
- Claude proposes things you did not ask for: a feature order, a library, a layout. That is its
  job. Say yes, or say why not, and it adjusts.
