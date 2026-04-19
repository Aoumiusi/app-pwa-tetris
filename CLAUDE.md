# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development

No build system or package manager. Serve `app/` over HTTP (PWA features require a real HTTP server, not `file://`):

```bash
# Python
python -m http.server 8080 --directory app

# Node.js
npx serve app
```

No tests, no linter configured.

## Architecture

The entire game is a single file: `app/index.html`. All HTML, CSS, and JavaScript live there — no modules, no external dependencies.

**Game state** is held in global variables: `board` (18×10 matrix), `player` (current piece position/matrix), `score`, `level`, `lines`, `gameOver`, `paused`.

**Game loop** uses `requestAnimationFrame` with `dropCounter`/`dropInterval` to control piece fall speed. Speed increases with level.

**Rendering** uses two `<canvas>` elements — main board (200×360 logical px) and next-piece preview — scaled to physical pixels via a computed `scale` factor.

**Key functions**:
- `collide(board, player)` — collision detection
- `clearRows()` — line clear with scoring (40/100/300/1200 × level)
- `drawMatrix(matrix, offset)` — renders blocks with color and black border
- `playerReset()` — spawns next piece, triggers game over check
- `merge(board, player)` — locks piece into board

**Input**: keyboard (arrow keys, Space for hard drop, P for pause) and on-screen touch buttons for mobile.

**PWA layer** (`sw.js`): cache-first strategy, caches HTML/manifest/icons on install, cleans old cache versions on activation. `manifest.json` configures standalone display with Japanese app name (テトリスゲーム).
