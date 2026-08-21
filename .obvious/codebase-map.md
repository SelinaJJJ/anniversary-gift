# Codebase Map — anniversary-gift

Depth-capped at 2. The repo has no subdirectories; all files live at the root.

| Path | Type | Purpose |
|---|---|---|
| `index.html` | App | Entire app: markup, CSS, JS, quiz data (`MEMORIES`), layout (`LAYOUT`), confetti, completion screen |
| `Our First Kiss.jpg` | Asset | Memory card 1 photo |
| `our fav brunch place.png` | Asset | Memory card 2 photo |
| `Our Sport.png` | Asset | Memory card 3 photo |
| `Your fav tennis player.png` | Asset | Memory card 4 photo |
| `My fav tennis player.png` | Asset | Memory card 5 photo |
| `our fav soft drink.png` | Asset | Memory card 6 photo |
| `our anniversary date.png` | Asset | Memory card 7 photo |
| `kiss.png` | Asset | Photo clipped into the lace heart on the completion screen |
| `.DS_Store` | Noise | macOS metadata; safe to ignore |

Key structures inside `index.html`:

- `MEMORIES` — 7 entries: `label`, `question`, `hint`, `icon`, `img`, `answer`, `variants`
- `LAYOUT` — absolute positions/rotations of the 7 cards on the 560×760 board
- `openModal` / `submit` / `revealSlot` — quiz interaction loop (3 attempts max per card)
- `buildHeart` — parametric SVG lace heart for the completion overlay
- confetti engine on `<canvas id="c">`
