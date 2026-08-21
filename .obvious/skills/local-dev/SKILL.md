---
name: local-dev
description: How to run and verify the anniversary-gift memory board app locally in the sandbox
---

# Local Dev — anniversary-gift

Verified on 2026-08-21 by the onboarding worker. Dev stack: healthy.

## Environment

- No dependencies to install — the app is a single static HTML file.
- Sandbox has Python 3.13 (use `http.server`) and Node 20 available.
- Playwright + Chromium (pip) installed at `/home/user/.cache/ms-playwright` —
  present in snapshot `uq891i3vtoppbcepkbe1:default`.

## Start

```bash
# from repo root
python3 -m http.server 8000 --bind 127.0.0.1   # background it: append & or use nohup
curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8000/   # expect 200
```

Port 8000 was free at onboarding time. No lock files, no env vars, no services.

## Verify (primary user flow)

The app is a quiz: click a card → modal → type answer → photo reveals.
Answers (from the `MEMORIES` array in `index.html`, in board order):

1. Our First Kiss → `charleston`
2. Our Fav Brunch Place → `press`
3. Our Sport → `tennis`
4. Your Fav Tennis Player → `carlos alcaraz`
5. My Fav Tennis Player → `aryna sabalenka`
6. Our Fav Soft Drink → `diet coke`
7. Our Anniversary → `april 6`

Automated check used at onboarding (Playwright, chromium headless):

1. Goto `http://127.0.0.1:8000/`, assert 7 `.item` cards render.
2. Click card 0 → modal opens; submit a wrong answer → expect "tries remaining".
3. Submit the correct answer → card gets `.done`, image gets `.show`.
4. Repeat for all 7 → `#doneOv` gains `.open` ("Happy 1st Anniversary").
5. Click `.done-close` → overlay closes.
6. Assert zero console/page errors and all images `naturalWidth > 0`.

Evidence screenshots: `/home/user/onboarding-evidence/01..04*.png`.

## Gotchas

- Image filenames contain spaces — URL-encode in HTTP fetches.
- Reveal has a 680ms delay; completion overlay appears ~1s after the 7th reveal —
  wait before asserting.
- Google Fonts is a CDN dependency; offline the page still works with fallback fonts.
- No lint/typecheck/tests exist; don't claim them in reports.

## Teardown

```bash
pkill -f "http.server 8000"
```
