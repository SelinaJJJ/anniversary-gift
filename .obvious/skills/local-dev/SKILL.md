---
name: local-dev
description: How to run and verify the anniversary-gift static site locally in the repo sandbox
---

# Local dev: anniversary-gift

## Environment

- Zero dependencies, zero build step. The app is one static `index.html` plus image assets.
- Runtime used: Python 3.13 (`python3 -m http.server`). Node 20 is also available.
- No lock files, no env vars, no external services. Google Fonts is the only external
  fetch and the page degrades gracefully to system fonts without it.

## Start

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

- App URL: http://127.0.0.1:8000/
- Confirm the port from startup output: `Serving HTTP on 127.0.0.1 port 8000`.
- Health check: `curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8000/` → `200`
  (also spot-check the 8 image assets — all should be 200).

## Verify (primary user flow)

Playwright 1.49 + Chromium are installed in the snapshot under `/tmp/pw`.
With the dev server running:

```bash
cd /tmp/pw && node verify.js
```

The script asserts: page title, 7 board cards render, wrong-answer feedback works,
all 7 quiz answers reveal their photos, the completion screen appears, and there are
zero console/page errors. 16 checks, exit code 0 = pass. Screenshots land in
`/tmp/pw/shots/`.

Quiz answers (spoilers, from `MEMORIES[]` in `index.html`):
`charleston`, `press`, `tennis`, `carlos alcaraz`, `aryna sabalenka`, `diet coke`, `4 6`.

## Reinstalling the browser tooling (fresh sandbox)

`npx playwright install --with-deps chromium` FAILS on this base image — it tries to
apt-install `ttf-ubuntu-font-family`, which has no installation candidate (exit 100).
Instead, split it:

```bash
cd /tmp/pw && npm i playwright@1.49
npx playwright install chromium            # downloads headless shell (~100 MB)
apt-get install -y libnss3 libnspr4 libdbus-1-3 libatk1.0-0 libatk-bridge2.0-0 \
  libcups2 libdrm2 libxcomposite1 libxdamage1 libxfixes3 libxrandr2 libgbm1 \
  libxkbcommon0 libasound2 libatspi2.0-0 fonts-liberation fonts-noto-color-emoji
```

Launch with `--no-sandbox` (the sandbox user is not root in the browser's view).

## Quirks learned during onboarding

- The question modal stays open after a wrong answer. Close it (the `.btn-ghost`
  "close" button) before clicking another board card — the open overlay intercepts
  pointer events.
- The Escape key only closes the modal while the answer input has focus; after
  clicking "reveal ✦" focus sits on the button, so Escape does nothing.
- No lint/typecheck/test tooling exists in the repo — never fabricate those results;
  browser flow verification + HTTP checks are the evidence bar.
