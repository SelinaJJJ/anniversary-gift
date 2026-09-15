---
name: local-dev
description: How to run and verify the anniversary-gift static site locally in the repo sandbox
---

# Local dev: anniversary-gift

## Environment

- Zero dependencies, zero build step. The app is one static `index.html` plus image assets.
- Runtime used: Python 3.13.14 (`python3 -m http.server`). Node 20.20.2 is also available.
- No lock files, no env vars, no external services. Google Fonts is the only external
  fetch and the page degrades gracefully to system fonts without it.

## Start

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

- App URL: http://127.0.0.1:8000/
- Confirm the port from startup output: `Serving HTTP on 127.0.0.1 port 8000`.
- Health check: `curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8000/` → `200`.
- Start it detached (`setsid nohup … </dev/null >log 2>&1 & disown`) — a plain
  background `&` keeps the calling shell's streams open and hangs long-running calls.

## Verify (primary user flow)

Playwright 1.49.1 + Chromium 131.0.6778.33 are installed in the snapshot under
`/tmp/pw` (browser binaries in `~/.cache/ms-playwright`). With the dev server running:

```bash
cd /tmp/pw && node verify.js
```

The script asserts 12 checks (exit 0 = pass): page title, page header, 7 board cards
render, question modal opens with the card question, wrong-answer feedback + attempt
pip, all 7 quiz answers reveal their photos (`.done` + `naturalWidth > 0`),
completion screen shows "Happy 1st Anniversary", lace-heart element is built, and
zero console/page errors. Screenshots land in `/tmp/pw/shots/`.

Quiz answers (spoilers, from `MEMORIES[]` in `index.html`):
`charleston`, `press`, `tennis`, `carlos alcaraz`, `aryna sabalenka`, `diet coke`, `4 6`.

## Rebuild from scratch (fresh sandbox)

```bash
mkdir -p /tmp/pw && cd /tmp/pw && npm init -y && npm i playwright@1.49.1
npx playwright install chromium          # ~260 MB into ~/.cache/ms-playwright
sudo apt-get update && sudo apt-get install -y \
  libnss3 libnspr4 libdbus-1-3 libatk1.0-0 libatk-bridge2.0-0 libcups2 libdrm2 \
  libxcomposite1 libxdamage1 libxfixes3 libxrandr2 libgbm1 libxkbcommon0 \
  libasound2 libatspi2.0-0
```

Debian 13 base image. The "Host system is missing dependencies" warning that races
the browser download can be ignored once apt finishes — just try launching.

## Quirks learned during onboarding

- The question modal stays open after a wrong answer. Close it (the `.btn-ghost`
  "close" button) before clicking another board card — the open overlay intercepts
  pointer events.
- The Escape key only closes the modal while the answer input has focus; after
  clicking "reveal ✦" focus sits on the button, so Escape does nothing.
- After a correct answer the modal auto-closes after ~680 ms; the completion overlay
  opens ~1 s after the 7th reveal — wait on selectors, do not sleep blindly.
- No lint/typecheck/test tooling exists in the repo — never fabricate those results;
  browser flow verification + HTTP checks are the evidence bar.
