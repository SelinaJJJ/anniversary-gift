# anniversary-gift

A single-page "Our Memory Board" anniversary quiz. The recipient answers 7 questions
about the couple; each correct answer reveals a pinned photo on a corkboard, ending
in a confetti "Happy 1st Anniversary" completion screen.

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
# Serve locally (verified during onboarding) → http://localhost:8000
python3 -m http.server 8000

# Quick health check
curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8000/
```

There is no install, build, lint, typecheck, or test step — the repo has no package
manifest or tooling. Do not invent one; verification is browser-based (see below).

## Codebase map

Tiny flat repo (no directories) — map inlined instead of a separate `codebase-map.md`:

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

Validated 2026-08-21 in sandbox `cmp_04zwgzx1`:

- `python3 -m http.server 8000` → `GET /` returns HTTP 200 (33,344 bytes); every image asset returns HTTP 200.
- Headless Chromium (Playwright) exercised the primary user flow end-to-end:
  - Page loads with title "Our Memory Board 💌"; board renders all 7 quiz cards.
  - Wrong answer → "not quite... try again ♡" feedback with attempt pips.
  - All 7 correct answers → each card flips to its photo (`.done` class, image visible).
  - Completion screen appears: "Happy 1st Anniversary" with confetti.
- Zero console errors, zero page errors. 4 screenshots captured: initial board,
  question modal, post-reveal board, completion screen.
- Lint / typecheck / unit tests: not applicable — no such tooling exists in this repo.

## Sandbox snapshot

- Snapshot ID: `3t8lpne0um0umr553heu:default`
- Captured: 2026-08-21T18:00:52Z
- Contains: repo checkout on `main`, verified dev-server setup, Playwright + Chromium
  installed under `/tmp/pw` (verification script `/tmp/pw/verify.js`, screenshots in
  `/tmp/pw/shots/`).

## Notes for agents

- Quiz answers are stored in plain text in `MEMORIES[]` inside `index.html` — treat as
  spoilers when quoting file contents.
- Do not rename image files: `index.html` references them by exact filename (with
  URL-encoded spaces) and has no fallback if one 404s.
- Default branch is `main`; repo is public. See `.obvious/config.yml` for merge policy.
