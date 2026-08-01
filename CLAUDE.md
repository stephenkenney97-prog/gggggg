# CLAUDE.md

Guidance for Claude Code (and other AI assistants) working in this repository.

## What this is

A single self-contained HTML file (`index.html`) implementing an interactive
3D browser demo/game built with Three.js: a laser point-defense system
("Iron Beam") that auto-tracks and neutralizes incoming projectile types
(rocket, drone, mortar) over a procedurally generated terrain, with a HUD
showing system/atmospheric/engagement stats and manual aim-and-fire controls.
It's a physics-flavored visual toy, not a real simulation of any actual
weapon system — targeting, damage, and atmospheric numbers are cosmetic/
fictional and exist to make the HUD feel alive.

There is no build system, no package manager, no source directory structure,
and no tests. Everything — scene setup, entity classes, UI, and event
handling — lives inline in `<script>` tags inside `index.html`.

## Repository layout

```
index.html   Entire application: HTML shell, CSS (inline <style>), and JS (inline <script>)
README.md    One-line placeholder ("gggggg / newnew") — not informative
```

## Running it

No server or build step is required.
```bash
python3 -m http.server 8000   # from the repo root, then open http://localhost:8000/
```
Opening `index.html` directly via `file://` also works in most browsers,
though some browsers restrict certain APIs (audio autoplay, etc.) under
`file://` — prefer serving it over HTTP if something behaves oddly.

All dependencies (Three.js r128 + `EffectComposer`, `RenderPass`,
`UnrealBloomPass`, `OrbitControls`, and related shader passes) are pulled from
public CDNs (`cdnjs.cloudflare.com`, `cdn.jsdelivr.net`) via `<script>` tags in
`<head>`. There's nothing to `npm install`; an internet connection is only
needed to fetch those CDN scripts and the skybox cube-texture images on load.

## Code structure inside `index.html`

- **Scene setup** (`init`, `createSkybox`, `createAdvancedLighting`,
  `createRealisticTerrain`, `setupPostProcessing`): one-time Three.js scene,
  camera, renderer, lighting, procedurally-displaced terrain with random
  buildings, and bloom post-processing.
- **`AdvancedIronBeam`** class: the turret — aiming, firing lasers from four
  apertures, smooth rotation via quaternion slerp, manual vs. auto targeting.
- **`AdvancedThreat`** class: spawned projectiles (`rocket` / `drone` /
  `mortar`), each with its own mesh, velocity, health, and (for rockets) a
  particle trail.
- **Explosions/sound**: `createExplosion` (particle burst + shockwave ring +
  point light flash) and `playSound` (WebAudio oscillator-based laser/explosion
  SFX — no audio files).
- **Main loop**: `animate()` drives per-frame updates (threats, turret,
  camera shake, composer render) and `updateHUD()` syncs DOM text content.
- **UI wiring**: HUD buttons call global functions directly via inline
  `onclick="..."` handlers (`launchAdvancedThreat`, `launchMultiThreat`,
  `toggleManualControl`, `toggleHud`, `resetAdvancedDemo`) — there's no
  framework or event-delegation layer.

## Conventions

- Everything is vanilla JS (ES6 classes, arrow functions, template literals)
  with no bundler/transpiler — code must run as-is in a modern browser.
  Don't introduce `import`/`export`, JSX, or anything requiring a build step
  without also adding the tooling to support it.
- 4-space indentation, semicolons, single quotes for JS strings, double quotes
  in HTML attributes.
- Global mutable state (`scene`, `camera`, `threats`, `activeTarget`, etc.) is
  declared at the top of the main `<script>` block and shared across
  functions/classes — this is a single-scene demo, not a reusable module, so
  keep new state additions consistent with that pattern rather than trying to
  encapsulate everything.
- Comments are sparse and used mainly as section dividers (`// --- X ---`);
  follow that style rather than adding verbose docblocks.

## Making changes

- Since it's one file, prefer targeted edits over restructuring into multiple
  files unless explicitly asked — splitting it up would break the "open and
  run" simplicity that's clearly intentional here.
- If you add new CDN dependencies, add the `<script>` tag alongside the
  existing ones in `<head>` and pin a version the same way the existing tags
  do (e.g. `three@0.128.0`).
- There's no test suite; verify changes by serving the file and exercising it
  in a browser (spawn threats, toggle manual control, resize the window).

## Working across the two repos in scope
This session may also have access to
`stephenkenney97-prog/super-duper-telegram`, a completely separate, unrelated
project (a grab-bag of Python scripts and a plant-care React frontend). It has
its own `CLAUDE.md`. Do not mix concerns between the two repos.
