# anniversary-gift

A single-page anniversary gift web app: **"Our Memory Board"** — a quiz-style memory board.
7 cards, each hiding a photo behind a question about the couple. A correct answer (or 3
wrong tries) reveals the photo; revealing all 7 shows a confetti completion screen.

## Stack

| Layer | Technology |
|---|---|
| App | Static HTML page — one file, no framework |
| Styling | Inline CSS in `index.html` (no preprocessor) |
| Logic | Inline vanilla JavaScript in `index.html` |
| Assets | 8 local images (PNG/JPG) in repo root |
| External | Google Fonts CDN only (page degrades gracefully offline) |
| Build | **None** — no bundler, no package manager, no dependencies |
| Services | **None** — no database, no backend, no env vars |

## Commands

```bash
# Serve locally (any static file server works; Python 3 is preinstalled in the sandbox)
python3 -m http.server 8000 --bind 127.0.0.1

# Open the app
# http://127.0.0.1:8000/

# Stop the server
# kill the python3 process (e.g. pkill -f "http.server 8000")
```

There is no install step, no lint, no typecheck, and no test suite — the repo has no
tooling manifests (`package.json`, CI configs, Makefile all absent).

## Codebase map

See `codebase-map.md` for the full table. Summary: everything lives in `index.html`
(markup + CSS + JS + quiz data); images sit in the repo root.

## Local verification

### Validation Summary

| Check | Result | Evidence |
|---|---|---|
| Static server serves page + all assets | PASS | `GET /` → 200; all 8 images → 200 |
| Board renders 7 memory cards | PASS | Playwright: `.item` count == 7 |
| Question modal opens on card click | PASS | Modal title "Our First Kiss" shown |
| Wrong answer gives feedback + attempt pips | PASS | "2 tries remaining" message |
| Correct answer reveals photo | PASS | 7/7 cards reach `.done` + `img.show` |
| All 7 revealed → completion overlay | PASS | "Happy 1st Anniversary · 04 · 06" |
| Completion overlay closes back to board | PASS | `#doneOv` removes `.open` |
| All images load (naturalWidth > 0) | PASS | 0 broken images |
| Console / page errors | PASS | none during full run |
| Lint / typecheck / unit tests | N/A | no tooling exists in repo |

Screenshots: `/home/user/onboarding-evidence/` on the sandbox
(`01-initial-board.png`, `02-question-modal.png`, `03-first-card-revealed.png`,
`04-completion-screen.png`).

### How to re-verify

1. `python3 -m http.server 8000 --bind 127.0.0.1` from repo root
2. `curl -s -o /dev/null -w "%{http_code}" http://127.0.0.1:8000/` → expect `200`
3. Full browser flow: see `.obvious/skills/local-dev/SKILL.md`

## Sandbox snapshot

- **Snapshot ID:** `uq891i3vtoppbcepkbe1:default`
- **Captured:** 2026-08-21T17:24:14Z
- **State:** repo at `main`, Python 3.13 + Node 20 + Playwright/Chromium installed,
  static server recipe verified (server process does not persist across snapshot restore)

## Notes for agents

- Quiz answers are embedded in `index.html` (`MEMORIES` array) — useful for testing.
- Image filenames contain spaces; URL-encode when fetching (`Our%20First%20Kiss.jpg`).
- Do not rename images without updating the `MEMORIES` array and `kiss.png` reference
  in `buildHeart()`.
