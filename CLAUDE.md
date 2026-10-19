# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Run

Sin build, lint ni tests, sin package.json. Abrir `index.html` o servir estático (`python -m http.server 8000`).

## Architecture

Tres archivos: `index.html` (canvas `#board` 300x600, `#next-canvas` 120x120, HUD, `#overlay`), `style.css`, `game.js` (toda la lógica, script global, `'use strict'`, sin módulos).

- Estado: variables `let` globales (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, `animId`...). `init()` las resetea; también reinicia.
- Tablero: matriz `ROWS x COLS`, 0 = vacío, 1-8 = índice en `COLORS`/`PIECES` (el mismo índice es el valor de las celdas de la forma).
- Flujo: `loop` (rAF) baja pieza por `dropInterval` -> `lockPiece` = `merge` + `clearLines` + `spawn`. `spawn` llama `endGame` si la pieza nueva colisiona.
- `clearLines` también recalcula nivel (cada 10 líneas) y `dropInterval = max(100, 1000-(level-1)*90)`.
- Input: un único listener `keydown` (por `e.code`) al final; `togglePause` cancela/reanuda rAF.
- Puntuación: `LINE_SCORES * level`; hard drop +2/celda, soft drop +1/fila.

- Tema: variables CSS en `:root` (oscuro) y `[data-theme="light"]` en `style.css`; `applyTheme` (game.js) fija `data-theme`, lee `--grid`/`--ghost-alpha` para el canvas y redibuja. Se persiste en `localStorage` (`tetris-theme`).

- Records: `records` global `{top,bestCombo,maxLines}` en `localStorage` (`tetris-records`); `combo` se actualiza en `lockPiece` vía `trackCombo`; `endGame` -> `showGameOverRecords` (input de nombre en `#overlay`). El `keydown` global sale si `e.target` es INPUT.

## Gotchas

- Cambiar `COLS`/`ROWS`/`BLOCK` exige ajustar `width`/`height` del canvas en `index.html` a mano.
- `drawNext` usa tamaño de bloque fijo 30 y caja 4x4.
- README.md (español) documenta lo mismo en detalle; mantener sincronizado si cambian mecánicas.
