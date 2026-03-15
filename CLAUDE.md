# CLAUDE.md

## README synchronization

This project has two README files: `README.md` (French) and `README-english.md` (English). When either file is updated, the other must be updated with the corresponding translation to keep both in sync.

## Project structure

- The **active deployment** uses root-level `worker.js` and `wrangler.toml`. The `naolib-worker/` directory is unused scaffolding — do not use it.
- `settings.yml` is documentation only — it does NOT sync to the TRMNL dashboard.

## Liquid templates

All 4 templates in `views/` share the same logic and only differ in CSS sizing classes. When updating one template, apply the same change to all four:
- `views/full.liquid`
- `views/half-horizontal.liquid`
- `views/half-vertical.liquid`
- `views/quadrant.liquid`

## Worker constraints (TRMNL integration)

- The worker response **must** be wrapped in `{ "merge_variables": { ... } }` for TRMNL to pick it up.
- Liquid templates access data via `{{ merge_variables.* }}`, never `{{ data.* }}`.
- Always return HTTP 200 even on errors (non-200 causes TRMNL to discard data). Include error info in the response body.
- All filtering and limiting of departures must be done **server-side** in `worker.js`. Liquid integer counters and comparisons are unreliable in TRMNL's Liquid engine.

## TRMNL API docs

TRMNL documentation is available at: https://docs.trmnl.com/go/llms.txt

## TAN API

- No API key required.
- Coordinates must use **commas** as decimal separators (French format): `47,21661` not `47.21661`.
