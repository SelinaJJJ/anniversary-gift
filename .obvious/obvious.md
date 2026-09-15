# anniversary-gift

A single-page "Our Memory Board" anniversary quiz. The recipient answers 7 questions
about the couple; each correct answer reveals a pinned photo on a corkboard, ending
in a confetti "Happy 1st Anniversary" completion screen.

> Refreshed 2026-09-15 by a re-onboarding run: local dev was re-verified end-to-end
> on a fresh sandbox and a new snapshot captured. The previous snapshot
> `3t8lpne0um0umr553heu:default` (2026-08-21) no longer exists.

## Stack

| Layer | Technology |
|---|---|
| App | Static single-page app — one `index.html` with inline CSS + vanilla JavaScript |
| Build | None — no bundler, no transpiler, no package manager, no dependencies |
| Serving | Any static file server; onboarding used Python 3.13 `http.server` |
| External assets | Google Fonts (Sacramento, Special Elite, Playfair Display) via CDN; degrades gracefully to system fonts offline |
| Services / env | None — no database, no backend, no required env vars |

## Commands

```bash
# Serve locally (verified 2026-09-15) → http://127.0.0.1:8000
python3 -m http.server 8000 --bind 127.0.0.1

# Quick health check
curl -s -o /dev/null -w "%{http_code}\n" http://127.0.0.1:8000/
```

There is no install, build, lint, typecheck, or test step — the repo has no package
manifest or tooling. Do not invent one; verification is browser-based (see below).

## Codebase map

Tiny flat repo (no app directories) — map inlined instead of a separate
`codebase-map.md`:

| Path | Purpose |
|---|---|
| `index.html` | Entire app: styles, board layout, quiz logic (`MEMORIES[]` holds answers), confetti canvas, completion screen |
| `Our First Kiss.jpg` | Photo asset — quiz reveal (card 1) |
| `our fav brunch place.png` | Photo asset — quiz reveal (card 2, `contain` fit) |
| `Our Sport.png` | Photo asset — quiz reveal (card 3) |
| `Your fav tennis player.png` | Photo asset — quiz reveal (card 4) |
| `My fav tennis player.png` | Photo asset — quiz reveal (card 5) |
| `our fav soft drink.png` | Photo asset — quiz reveal (card 6) |
| `our anniversary date.png` | Photo asset — quiz reveal (card 7) |
| `kiss.png` | Completion-screen image clipped into the lace heart shape |
| `.DS_Store` | macOS artifact (committed; harmless) |

## Local Verification Summary

Validated 2026-09-15 in sandbox `cmp_xTziKJVW` (fresh checkout of `main` @ 9e1b39d):

- `python3 -m http.server 8000 --bind 127.0.0.1` (Python 3.13.14) → startup output
  `Serving HTTP on 127.0.0.1 port 8000`; `GET /` returns HTTP 200 (33,344 bytes);
  all 8 image assets return HTTP 200.
- Headless Chromium 131.0.6778.33 (Playwright 1.49.1) exercised the primary user
  flow end-to-end — 12/12 assertions passed, script exit 0:
  - Page loads with title "Our Memory Board 💌"; board renders all 7 quiz cards.
  - Wrong answer → "not quite... try again ♡" feedback; attempt pip marked used.
  - All 7 correct answers → every card flips to its photo (`.done`, image `.show`,
    `naturalWidth > 0` on all 7).
  - Completion screen appears: "Happy 1st Anniversary" with lace-heart photo.
  - Zero console errors, zero page errors.
- 4 screenshots captured (1280×900 PNG) in `/tmp/pw/shots/`: initial board,
  question modal, completion screen, fully revealed board.
- Lint / typecheck / unit tests: not applicable — no such tooling exists in this
  repo; those results are never fabricated.

## Sandbox snapshot

- Snapshot ID: `n2jbyo38c0byb0nj54vm:default`
- Captured: 2026-09-15T17:34:09Z (sandbox `cmp_xTziKJVW` / `i6pem2dzklvj2cagpu4ge`)
- Contains: repo checkout on `main`, dev server running on 127.0.0.1:8000,
  Playwright 1.49.1 + Chromium 131 (browser binaries in `~/.cache/ms-playwright`),
  verification script `/tmp/pw/verify.js`, screenshots in `/tmp/pw/shots/`.

## Notes for agents

- Quiz answers are stored in plain text in `MEMORIES[]` inside `index.html` — treat
  as spoilers when quoting file contents.
- Do not rename image files: `index.html` references them by exact filename (with
  URL-encoded spaces) and has no fallback if one 404s.
- PR #4 (`polish/ui-refinements`) is open against `main` with typography/spacing/
  focus-state changes to `index.html` — check it before editing styles to avoid
  conflicts.
- Default branch is `main`; repo is public. See `.obvious/config.yml` for merge policy.
