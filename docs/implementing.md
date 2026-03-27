# Implementation Plan

## Phase 1 — Build-time

1. `logger.py` — logging adapter
2. `assets.py` — CSS/JS injection helpers
3. `plugin.py` — `on_config`, `on_post_build`, `on_page_context` hooks

## Phase 2 — Runtime

4. `server.py` — Claude session + streaming
5. `chat.js` / `chat.css` — widget UI
