# Agent guidance

- This repository is a standalone Three.js simulation; application markup, CSS, and JavaScript live in `index.html`.
- Read [DESIGN.md](DESIGN.md) before changing the interface.
- Preview from the repository root: `python3 -m http.server 8000 --bind 127.0.0.1`, then open `http://127.0.0.1:8000/`.
- There is no package manifest, build step, or automated test suite; CDN access is required for Three.js.
- Keep the single HTML entry point and existing script order. Three.js and its example scripts currently use version `0.128.0`.
- Check APIs against that version before using them; keep core and example scripts compatible when changing dependencies.
- Keep changes focused on the requested behavior and preserve existing controls, element IDs, and event handlers.
- For behavior changes, check launch controls, manual mode, HUD visibility, reset, camera controls, and viewport resizing in a browser.
- For visual changes, check panel readability, control access, and overlap at desktop and narrow widths; inspect the console for new errors.
- Report what changed, what was verified, and any checks that could not run. Do not describe a syntax check as a browser test.
- Deployment workflows are under `.github/workflows/`; account for their Pages triggers before pushing changes.
