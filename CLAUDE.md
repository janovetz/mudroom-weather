# CLAUDE.md

Mudroom Weather: a single-page weather display for an always-on tablet. All code (HTML, CSS, JS) is in `index.html`. There is no build step, no dependencies to install, and no API keys.

## Documentation

- **`README.md`**: short overview, feature list, location setup, how to run it locally. When a significant feature is added (a new panel, mode, setting or data source), add it to the feature list in the same change. Keep the README brief and point to the docs for detail.
- **`docs/README.md`**: full technical reference. Update it whenever behavior changes: what-to-wear rules, set points (defaults and ranges in `WEAR_DEFAULTS` / `WEAR_SLIDERS`), heads-up items, spoken summary, settings, refresh intervals or data sources. Keep its tables in sync with the code.

## Workflow

- Run locally with `python3 -m http.server 8765` and open http://localhost:8765/.
- There is no test suite. At minimum, extract the `<script>` block and run `node --check` on it.
- Every push to `main` deploys to GitHub Pages (https://janovetz.github.io/mudroom-weather/). Ask before pushing.
- The repo is public: never commit personal coordinates or other location data. Use generic example coordinates in docs.
- Target current browsers only; older devices (e.g. iOS 12) are not supported.

## Code conventions

- Match the existing style: vanilla JS, `const` arrow helpers, compact one-line comments explaining *why*.
- What-to-wear set points are stored in °F / inches / mph and converted for display with `fmtWear()`.
- Bump `cacheKey()`'s version when the requested Open-Meteo fields change.
- Colors are CSS custom properties on `:root`; the theme is mostly pure black for OLED screens, with thin `--edge` outlines for definition.
