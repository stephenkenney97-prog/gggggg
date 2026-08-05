# CLAUDE.md

Guidance for Claude Code (and other AI assistants) working in this repository.

## What this repo is

A single self-contained HTML page — `index.html` — implementing "Iron Beam
Photorealistic Simulation," a browser-based 3D demo built with Three.js
(r128). It renders a terrain scene with a turret-style defense system that
engages incoming projectiles (rockets, drones, mortars), complete with
HUD overlays, particle/explosion effects, post-processing bloom, and
procedural sound via the Web Audio API. It is a creative-coding / visual
simulation toy, not a real control system — there is no backend, no
network calls beyond the CDN script tags, and no persistence.

There is no application code outside of `index.html`. Do not assume a
build system, package manager, or test suite exists — none do.

## Repository structure

```
index.html                       # The entire application: HTML + inline <style> + inline <script>
.github/workflows/static.yml     # Deploys the repo root straight to GitHub Pages on push to main
.github/workflows/jekyll-docker.yml  # CI-only Jekyll build check on push/PR to main (does NOT deploy)
README.md                        # Minimal placeholder
```

`static.yml` is the actual deployment path (Pages `actions/deploy-pages`).
`jekyll-docker.yml` just runs `jekyll build` inside a container as a build
check; it has no deploy step. Don't assume the two are duplicating the same
job — if you touch deployment, `static.yml` is the one that matters for
what ends up live.

## Working inside `index.html`

The file is organized top-to-bottom as:

1. `<head>` — CDN `<script>` tags for `three.js` (r128) and the r0.128
   postprocessing/controls examples (`EffectComposer`, `RenderPass`,
   `UnrealBloomPass`, `ShaderPass`, `OrbitControls`), plus an inline
   `<style>` block for the HUD panels (`#system-panel`, `#physics-panel`,
   `#engagement-panel`, `#controls-panel`).
2. `<body>` — the HUD markup (buttons, stat readouts) followed by a single
   inline `<script>`.
3. The script, roughly in this order:
   - Module-level state: `scene`, `camera`, `renderer`, `clock`,
     `composer`, `threats[]`, `neutralizedCount`, `isManualControl`, etc.
   - `init()` — scene/camera/renderer setup, event listener registration
     (`resize`, `mousemove`, `mousedown`/`mouseup` for manual firing).
   - Scene builders: `createSkybox`, `createAdvancedLighting`,
     `createRealisticTerrain` (procedural hills + random buildings),
     `setupPostProcessing` (bloom via `UnrealBloomPass`).
   - `playSound(type, params)` — synthesizes `laser` / `explosion` SFX
     with `AudioContext` oscillators/filters; no audio files.
   - `class AdvancedIronBeam` — the turret: apertures, `startFiring` /
     `stopFiring`, `fireSingleLaser(aperture, targetPos)`, and `update()`
     which either aims at the mouse raycast (manual mode) or the active
     `threats[]` target (auto mode) and slerps the turret rotation.
   - `class AdvancedThreat` — incoming projectiles (`rocket` / `drone` /
     `mortar`), each with a `mesh`, `velocity`, `health`, particle
     trail (`updateTrail`), and `destroy()` (explosion + camera shake +
     removal from `threats[]`).
   - `createExplosion(position)`, `triggerCameraShake(intensity, duration)`.
   - `animate()` — the main render loop (delta-time updates, threat
     spawning/cleanup, composer render).
   - UI handlers: `updateHUD`, `toggleManualControl`, `toggleHud`,
     `launchAdvancedThreat(type)`, `launchMultiThreat`,
     `resetAdvancedDemo`, `onWindowResize`, `onMouseMove`.

Keep new functionality inside this single-file structure unless you have a
reason to introduce a build step (see below) — the whole point of the repo
is a page that runs unmodified when opened or served statically.

## Development workflow

- **No install, no build.** Open `index.html` directly in a browser, or
  serve the directory with any static file server (e.g.
  `python3 -m http.server`) if you need it under `http://` rather than
  `file://` (some browsers restrict certain APIs under `file://`).
- **No test suite, no linter configured.** Verify changes by loading the
  page and exercising it manually: toggle manual control, fire the
  turret, launch single/multi threats, and check the HUD updates and
  console for errors.
- **Dependencies are CDN-pinned in the `<head>`.** Three.js and its
  examples/postprocessing modules are all pinned to specific versions
  (r128 core, 0.128.0 examples). If you bump one, bump the matching
  examples build too — the postprocessing/controls scripts are
  version-coupled to core `three.min.js` and mismatches break silently or
  throw at runtime.
- **Deployment is automatic**: any push to `main` triggers
  `static.yml`, which publishes the repo root to GitHub Pages. There is
  no staging environment — pushing to `main` is publishing.

## Conventions

- Everything lives in one file; don't split into modules/bundlers without
  discussing it first, since that changes the deploy story (`static.yml`
  currently uploads the raw repo root as-is).
- Scene object construction, per-frame update logic, and UI/HUD glue are
  kept in separate functions/classes as outlined above — follow that
  separation when adding new threat types or weapon behavior rather than
  inlining logic into `animate()`.
- Colors/materials follow a consistent "HUD green/cyan on dark" palette
  (`#00ff88`, `#00ffff`) — match it for any new UI elements.
