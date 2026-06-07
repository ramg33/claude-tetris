# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the game

No build step. Open directly or serve statically:

```bash
open index.html                  # macOS — simplest option
python3 -m http.server 8000      # then visit http://localhost:8000
npx serve .
```

There are no tests, no linter, and no `package.json`.

## Architecture

Three files, no dependencies:

- **`index.html`** — two `<canvas>` elements (`#board` 300×600 px, `#next-canvas` 120×120 px), a sidebar panel (#score, #lines, #level), and a shared overlay div for PAUSE / GAME OVER states.
- **`style.css`** — dark/retro theme; overlay uses `backdrop-filter`.
- **`game.js`** — all game logic (~305 lines, `'use strict'`, no modules).

### Key data structures in `game.js`

- `board` — `ROWS × COLS` (20×10) 2-D array; cells hold `0` (empty) or a color index 1–7.
- `current` / `next` — piece objects `{ type, shape, x, y }` where `shape` is a 2-D matrix of color indices.
- `PIECES[1..7]` / `COLORS[1..7]` — parallel arrays; index 0 is always `null`.

### Game loop

`init()` → `spawn()` → `requestAnimationFrame(loop)`. `loop(ts)` accumulates delta time against `dropInterval`, calls `lockPiece()` when a piece lands, then always calls `draw()`. All input is handled via a single `keydown` listener.

### Tunable constants (top of `game.js`)

`COLS`, `ROWS`, `BLOCK` (px per cell), `COLORS`, `LINE_SCORES`, initial `dropInterval`. If `COLS`, `ROWS`, or `BLOCK` change, update the `<canvas>` `width`/`height` attributes in `index.html` to match (`COLS×BLOCK` and `ROWS×BLOCK`).

### Rotation

`rotateCW(shape)` — transpose + reverse rows. `tryRotate()` applies wall kicks by testing offsets `[0, -1, 1, -2, 2]` in order.

### Speed formula

`dropInterval = Math.max(100, 1000 - (level - 1) * 90)` ms. Level increases every 10 lines.
