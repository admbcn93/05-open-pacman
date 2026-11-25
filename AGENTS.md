# AGENTS.md

## Project

Vanilla JS Pac-Man clone (HTML/CSS/JS). No framework, no bundler, no dependencies.

## Commands

- No tooling configured: no `package.json`, no test/lint/typecheck/build scripts.
- Run by opening `src/index.html` in a browser, or serving `src/` statically.
  Scripts are classic scripts (not ES modules), so `file://` works without a server.

## Architecture

- No modules / ES imports. Cross-file wiring is via globals on `window`.
- Load order in `src/index.html` matters and is fixed:
  `js/maze.js` -> `js/game.js` -> `js/render.js` -> `js/main.js`
- Responsibilities:
  - `js/maze.js` — level data (`MAZE_STR` -> numeric `MAZE`), tunnel row, start/ghost positions. Exports via `window.*`.
  - `js/game.js` — state & rules. `createGame()` deep-copies `MAZE` per run; dots are eaten by mutating `game.grid`. Exports `createGame`/`update` via `window.*`.
  - `js/render.js` — canvas drawing; reads `game.grid` (not the pristine `MAZE`) so eaten dots disappear.
  - `js/main.js` — rAF loop, keyboard input, overlays/screens. Calls global `createGame`/`update`/`draw`.

## Quirks

- Grid encoding: `#` = wall, `.` = dot, ` ` = empty walkable, `-` = pen door. Cell coords: x right, y down.
- Tunnel: `TUNNEL_ROW = 14`; actors wrap horizontally at that row.
- Pacman movement is sub-cell precision with per-frame alignment snap; speeds tuned via fractions of a cell/frame.
- Ghost AI: `hunter` ghosts chase via best Manhattan distance; `random` ghosts pick randomly among legal moves; dead-end forces a 180° turn.

## Conventions

- Code comments and player-facing UI strings are in Spanish. Keep new comments/UI consistent.
- Game states: `start`, `playing`, `won`, `lost`; overlay text is Spanish ("GANASTE", "PERDISTE").

## Verification

- No automated tests. Verify manually in the browser: start game, move, eat dots, win/lose flow.
