# Cube Mentor

An interactive 3D Rubik's Cube tutor: scan your cube, see it in 3D, and learn to solve it, from the beginner method to full CFOP.

## Run it

`dist/index.html` is a single self-contained file (Three.js and the solver are bundled in). Open it in Chrome, Edge, Safari or Firefox.

**Live camera:** browsers only allow camera access on `https://` or `localhost`, and some browsers also allow it for files opened directly. If the camera is blocked, serve the folder locally (`npx serve dist`, then open `http://localhost:3000`), or use the built-in photo mode instead.

Rebuild from source: `npm install && node build.mjs`.

## Structure

| Area | File |
|---|---|
| Cube-state model and notation (U D L R F B M E S x y z, wide, prime, double) | `src/core/cube.js` |
| Piece tables and physical validation (counts, centres, pieces, twist, flip, parity) | `src/core/cubies.js` |
| Piece-tracking breadth-first search, orientation helpers | `src/core/search.js` |
| Kociemba two-phase solver (cubejs, MIT) in a Web Worker | `src/solver/kociemba.js`, `src/worker/` |
| Beginner layer-by-layer planner | `src/solver/beginner.js` |
| CFOP planner (optimal cross, F2L table, OLL, PLL, 2-look) | `src/solver/cfop.js` |
| Stage checks | `src/solver/checks.js` |
| Algorithm datasets | `src/data/algs.js` |
| Lesson content | `src/data/lessons.js` |
| Camera and photo sampling | `src/scan/camera.js` |
| Colour calibration (CIELAB, CIEDE2000, balanced k-means) | `src/scan/color.js` |
| 3D renderer (Three.js) | `src/render/cube3d.js` |
| Tutor engine | `src/ui/tutor.js` |
| Views | `src/ui/*View.js` |
| Persistence (localStorage) | `src/ui/store.js` |

## Verification (`tests/`)

- `verify-algs.mjs`: the 57 OLLs and 21 PLLs each preserve the first two layers, are pairwise distinct, and match their group's structure.
- `cross-oll.mjs` / `cross-pll.mjs`: OLL numbers and PLL names cross-checked against jperm.net. Each case is solved by the reference algorithm.
- `f2l-coverage.mjs`: all 41 F2L cases are covered by verified algorithms.
- `test-beginner.mjs`: beginner plans tested on 1,000+ random cubes in random orientations. Every stage check passes and every cube ends solved.
- `test-cfop.mjs`: full and 2-look CFOP plans tested on random cubes, all solved.
- `scan_e2e.py`: synthetic photos with uneven lighting, tint, glare and noise go through the app's photo pipeline. 10 of 10 cubes were rebuilt exactly.
- `tutor_e2e.py`, `timer_e2e.py`, `camera_e2e.py`: browser tests for guided solves, mistake detection and recovery, stage verification, the timer, persistence across reloads, and live camera capture.

## Known limits

- The camera can check only the face it can see, so the app can't watch your hands. Use "Check my real cube with the camera" to compare your cube's front face with the expected state. It identifies single-move slips and being moves ahead or behind.
- The scanner assumes the standard Western colour scheme (white opposite yellow, green opposite blue, red opposite orange).
- Roux and ZZ are taught as explanations and drills (block highlighting, an edge-orientation counting drill). There is no step-by-step Roux or ZZ planner for your own cube.
