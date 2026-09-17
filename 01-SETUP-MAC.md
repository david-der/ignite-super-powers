# Set up your Mac

Goal: a terminal that can run Claude Code and the five tools the house way depends on. Assume
nothing is installed. Everything here is copy and paste. When a step says *check*, paste the check
line and compare with what you see. If it prints a version number, move on.

## 0. Open Terminal

Press `Cmd + Space`, type `Terminal`, press Enter. The window that opens is where you paste each
command. Press Enter after pasting. A command is finished when the prompt (your name and a `%`)
comes back.

Two things to know:

- Typing a password in Terminal shows nothing on screen. That is normal. Type it and press Enter.
- If something asks `Do you want to continue? [y/N]`, type `y` and press Enter.

## 1. Apple's developer tools (git and compilers)

```bash
xcode-select --install
```

A window pops up. Click **Install** and wait; it can take ten minutes. Check:

```bash
git --version
```

## 2. Homebrew (the Mac's app installer for the terminal)

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

It asks for your Mac password once. At the end it prints two or three lines under **Next steps**
that start with `echo` and `eval`. Paste those exact lines too; they teach Terminal where Homebrew
lives. Then close Terminal and open it again. Check:

```bash
brew --version
```

## 3. The five tools, in one line

```bash
brew install just uv node pnpm gh
```

What each one is:

- **just** runs the short command menu in every project (the `justfile`).
- **uv** installs and runs Python and Python projects. You never install Python by hand; uv does it.
- **node** and **pnpm** run JavaScript projects (websites built with Vite).
- **gh** talks to GitHub from the terminal, for saving your code online.

Check all five:

```bash
just --version && uv --version && node --version && pnpm --version && gh --version
```

## 4. Tell git who you are

Use your real name and the email you use for GitHub.

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
```

## 5. GitHub (optional today, needed the first time you want your code backed up online)

Make a free account at github.com if you do not have one, then:

```bash
gh auth login
```

Pick **GitHub.com**, **HTTPS**, **Login with a web browser**, and follow the code it shows you.

## 6. Claude Code

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Close Terminal, open it again, then:

```bash
claude
```

The first run asks you to sign in with your Claude account in the browser. Claude Code needs a
paid Claude plan (Pro or higher); the sign-in page says so if yours is not. When you see the
Claude prompt, type `/exit` to leave. Check:

```bash
claude --version
```

## 7. A workspace folder, with this guide inside it

All projects live in one place so you and Claude always know where to look. The second line
downloads this guide into it.

```bash
mkdir -p ~/workspace && cd ~/workspace
git clone https://github.com/david-der/ignite-super-powers.git
```

Check:

```bash
ls ~/workspace/ignite-super-powers
```

## 8. Playwright (the browser that takes screenshots)

Playwright is installed per project by `just setup`, so there is nothing to do now. The first
`just setup` in a project downloads a copy of Chromium, about 150 MB. That is expected.

## Done

You have: `git`, `brew`, `just`, `uv`, `node`, `pnpm`, `gh`, `claude`, and this guide at
`~/workspace/ignite-super-powers`. Go to `README.md` step 2 to install the superpowers, then read
`02-THE-LOOP.md`.

## If something goes wrong

Paste the error into Claude Code and ask. Open Terminal, type `claude`, and say *"I ran this and
got this error"* with the text. Fixing setup problems is something it is good at.
