# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Asteroids clone in plain HTML5 Canvas + vanilla JS (README is in Spanish). No dependencies, no bundler, no package.json, no tests, no linter, and not a git repo.

## Running

Open `index.html` directly in a browser, or serve the folder: `npx serve .` (then http://localhost:3000). There is no build step; reload the page after editing.

## Architecture

All game logic lives in a single script, `game.js`, loaded by `index.html` onto an 800x600 `<canvas id="canvas">` (the `W`/`H` constants in `game.js` must match the canvas size attributes in `index.html`).

`game.js` is organized in top-to-bottom sections:
- **Input**: `keys` / `justPressed` maps filled by keydown/keyup listeners, read through `pressed(code)`.
- **Utils**: `wrap` (toroidal screen wrapping), `dist`, `rand`, `randInt`.
- **Entity classes** (`Bullet`, `Asteroid`, `Ship`, `Particle`): each has `update(dt)` and `draw()`. Asteroids use size tiers 1–3 via the parallel arrays `RADII`, `SPEEDS`, `POINTS` (indexed by size, index 0 unused); `Asteroid.split()` produces the next smaller tier.
- **Game state**: module-level globals (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`) and a `state` string: `'playing' | 'dead' | 'gameover'`, with `deadTimer` for respawn delay. Helpers: `spawnAsteroids`, `initGame`, `nextLevel`, `explode`, `killShip`.
- **Update/Draw**: `update(dt)` runs all simulation and collisions; `draw()` renders world, HUD (`drawHUD`, `drawLifeIcon`) and overlays (`drawOverlay`).
- **Loop**: `loop(ts)` via `requestAnimationFrame`, computing `dt` from `lastTime`; `initGame()` and the first frame are kicked off at the bottom of the file.

Adding an entity type means: a class with `update`/`draw`, a collection global, and wiring into `initGame`, `update`, and `draw`.

## Note

The README mentions power-ups and special asteroid types (e.g. "estrella fugaz"); verify against `game.js` before assuming they are implemented.
