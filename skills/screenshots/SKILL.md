---
name: screenshots
description: Take screenshots of a running web app with Playwright and keep them organised in ./turn_results/<YYYYMMDD-HHMMSS-what>/ (flat, numbered, gitignored). Use when a project needs its `just turn` recipe, when adding Playwright to a project, or whenever a screenshot is about to be saved anywhere.
---

# Screenshots

Every screenshot of app work goes to one place, named one way, so nobody navigates to find it.

## The convention

- Folder: `./turn_results/<YYYYMMDD-HHMMSS>[-suffix]/` at the project root. `turn_results/` is in
  `.gitignore`.
- One folder per run. `TURN_RUN_DIR` names it; when unset, each turn makes its own
  `<stamp>-turn` folder.
- Flat: images only, no subfolders. Files are `<label>.png`, and the label starts with a two-digit
  number in the order taken: `01-home-empty`, `02-home-after-add`.
- When the page scrolls, also save `<label>-full.png` (full page). The viewport shot is the one
  to read first.
- Viewport default is 1280×820. `APP_VIEWPORT=390x844` for a phone.
- Never `/tmp`, never the source tree, never committed.

## Setup in a Python project

```bash
uv add --dev playwright
uv run playwright install chromium
```

Add to `justfile`:

```just
# One real turn: open the app, do the steps, screenshot into turn_results/$TURN_RUN_DIR/<label>.png
turn label="turn" path="/" *steps:
    uv run python scripts/turn.py {{label}} {{path}} {{steps}}
```

Write `scripts/turn.py`:

```python
"""One turn: open the running app, do the steps, screenshot into turn_results/.

Usage: uv run python scripts/turn.py <label> [path] [step ...]
Steps: fill:<selector>=<text>   click:<selector>   press:<key>   wait:<ms>
Env:   APP_URL (default http://localhost:8000), TURN_RUN_DIR, APP_VIEWPORT (e.g. 390x844)
Files: turn_results/<run>/<label>.png, plus <label>-full.png when the page scrolls.
"""

from __future__ import annotations

import os
import sys
from datetime import datetime
from pathlib import Path

from playwright.sync_api import sync_playwright

ROOT = Path(__file__).resolve().parent.parent
BASE = os.environ.get("APP_URL", "http://localhost:8000").rstrip("/")


def run_dir() -> Path:
    name = os.environ.get("TURN_RUN_DIR") or f"{datetime.now():%Y%m%d-%H%M%S}-turn"
    out = ROOT / "turn_results" / name
    out.mkdir(parents=True, exist_ok=True)
    return out


def viewport() -> dict[str, int]:
    w, _, h = os.environ.get("APP_VIEWPORT", "1280x820").partition("x")
    return {"width": int(w), "height": int(h)}


def do(page, step: str) -> None:
    kind, _, arg = step.partition(":")
    if kind == "fill":
        selector, _, text = arg.partition("=")
        page.fill(selector, text)
    elif kind == "click":
        page.click(arg)
    elif kind == "press":
        page.keyboard.press(arg)
    elif kind == "wait":
        page.wait_for_timeout(int(arg))
    else:
        raise SystemExit(f"unknown step: {step!r} (use fill:, click:, press:, wait:)")


def main() -> None:
    args = sys.argv[1:]
    label = args[0] if args else "turn"
    rest = args[1:]
    path = rest.pop(0) if rest and rest[0].startswith("/") else "/"
    out = run_dir()
    with sync_playwright() as pw:
        browser = pw.chromium.launch()
        page = browser.new_page(viewport=viewport())
        page.goto(BASE + path, wait_until="networkidle")
        for step in rest:
            do(page, step)
        page.wait_for_timeout(400)
        page.screenshot(path=str(out / f"{label}.png"))
        if page.evaluate("document.documentElement.scrollHeight > window.innerHeight + 1"):
            page.screenshot(path=str(out / f"{label}-full.png"), full_page=True)
        browser.close()
    print(f"turn → {(out / f'{label}.png').relative_to(ROOT)}")


if __name__ == "__main__":
    main()
```

## Setup in a Vite project

```bash
pnpm add -D @playwright/test tsx
npx playwright install chromium
```

`justfile`:

```just
turn label="turn" path="/" *steps:
    npx tsx e2e/turn.ts {{label}} {{path}} {{steps}}
```

`e2e/turn.ts` does the same thing with the same arguments and env vars:

```ts
import fs from 'node:fs'
import path from 'node:path'
import { fileURLToPath } from 'node:url'
import { chromium } from '@playwright/test'

const ROOT = path.resolve(path.dirname(fileURLToPath(import.meta.url)), '..')
const BASE = (process.env.APP_URL ?? 'http://localhost:5173').replace(/\/$/, '')
const [label = 'turn', ...rest] = process.argv.slice(2)
const route = rest[0]?.startsWith('/') ? rest.shift()! : '/'
const stamp = new Date().toISOString().replace(/[-:T]/g, '').slice(0, 15).replace(/(\d{8})(\d{6}).*/, '$1-$2')
const dir = path.join(ROOT, 'turn_results', process.env.TURN_RUN_DIR ?? `${stamp}-turn`)
fs.mkdirSync(dir, { recursive: true })
const [w, h] = (process.env.APP_VIEWPORT ?? '1280x820').split('x').map(Number)

const browser = await chromium.launch()
const page = await browser.newPage({ viewport: { width: w, height: h } })
await page.goto(BASE + route, { waitUntil: 'networkidle' })
for (const step of rest) {
  const [kind, arg] = [step.slice(0, step.indexOf(':')), step.slice(step.indexOf(':') + 1)]
  if (kind === 'fill') { const i = arg.indexOf('='); await page.fill(arg.slice(0, i), arg.slice(i + 1)) }
  else if (kind === 'click') await page.click(arg)
  else if (kind === 'press') await page.keyboard.press(arg)
  else if (kind === 'wait') await page.waitForTimeout(Number(arg))
  else throw new Error(`unknown step: ${step}`)
}
await page.waitForTimeout(400)
const file = path.join(dir, `${label}.png`)
await page.screenshot({ path: file })
if (await page.evaluate(() => document.documentElement.scrollHeight > window.innerHeight + 1))
  await page.screenshot({ path: file.replace(/\.png$/, '-full.png'), fullPage: true })
console.log(`turn → ${path.relative(ROOT, file)}`)
await browser.close()
```

## Running it

The app must be running first (`just dev`, in another Terminal tab or started in the background).
Then:

```bash
export TURN_RUN_DIR="$(date +%Y%m%d-%H%M%S)-shelf"
just turn 01-home
just turn 02-add-open / "click:text=Add"
just turn 03-added / "fill:#title=Catan" "click:text=Save"
```

Steps are quoted, one per argument. Selectors are Playwright selectors: `#id`, `.class`,
`text=Save`, `role=button[name="Save"]`.

## Multi-step scripts

When a run needs more than a few steps (a login, an upload, waiting for a stream to finish),
write a Playwright script under `e2e/` or `scripts/` for that flow. It must still take its
screenshots into the run folder with numbered labels; reuse `run_dir()` from `turn.py` rather
than inventing a second location.

## After the screenshot

Taking the picture is half the job. The `turn-critique` skill covers reading it.
