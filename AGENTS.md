# AGENTS.md

## Project overview

AI-3D Design Master (AI-Shrek) — a static single-page web application for 3D STL/STEP file preview and AI-assisted mechanical design. Deployed on GitHub Pages.

## Entry points

| File | Role |
|------|------|
| `index.html` | Project list page (landing), personal center, all modals |
| `workspace.html` | 3D workbench: viewer, chat, version history, export |
| `api/` | Vercel serverless functions (upload, modify, history) |
| `stl_tools.py` | Python STL processing utilities (trimesh-based) |

Legacy files (outdated, may conflict with current app):
- `modify.html`, `history.html` — old standalone pages, superseded by workspace.html
- `docs/` — older copy of the same files, ignore

## Dev commands

```bash
# Local dev server (port 8080)
python3 -m http.server 8080

# Push to GitHub (auto-deploys to GitHub Pages)
git push origin master
```

No build step. No framework. All inline vanilla HTML/CSS/JS.

## Deployment

- **Live URL**: `https://lisa8808.github.io/stl-preview/`
- **GitHub Pages** from `master` branch root
- **CDN cache**: 10-minute `max-age`, users see stale content unless cache-busted with query params (`?v=N`)
- Always suggest `Cmd+Shift+R` or `?v=N` for users after pushing changes

## Architecture

- `index.html` → project cards (localStorage `stl_projects`)
- Click a card → `workspace.html?project=<id>`
- workspace loads Three.js via CDN for 3D rendering
- All state in `localStorage` (no backend at runtime): projects, models, members, upload history
- Modals are inline `<div class="modal-overlay">` sections, toggled via `classList.toggle('show')`

### workspace layout (three-column grid: 18% / 58% / 24%)

- **Left panel**: 3-tab design mode bar (通用建模/原生精密/全域设计), each tab has own upload area + per-tab file history
- **Middle panel**: 3D preview canvas only (no chat)
- **Right panel**: Chat panel (full-height) with skill selector + messages + input; version history is a **toggleable overlay** that covers the chat area ("修改历史" button in chat header)

## Key conventions

- **All JS is inline** inside `<script>` tags in each HTML file — no external `.js` files
- **CSS custom properties** (both index.html and workspace.html):
  `--bg-page: #080D1A`, `--bg-panel: rgba(13,18,37,0.86)`, `--bg-card: rgba(18,27,48,0.82)`,
  `--border-soft: rgba(148,163,184,0.12)`, `--border-active: rgba(37,99,235,0.48)`,
  `--text-main: #F8FAFC`, `--text-secondary: #94A3B8`, `--text-muted: #64748B`,
  `--primary: #2563EB`, `--primary-soft: rgba(37,99,235,0.14)`, `--accent: #38BDF8`
- **Font**: Inter from Google Fonts CDN
- **localStorage keys**: `stl_projects`, `stl_ai_models`, `stl_members`, `stl_upload_history_*` (per-tab: `_zhongxing`, `_jingmi`, `_quanyu`), `stl_versions_*`
- **gitignored**: `node_modules/`, `报价文档.md`

## Common pitfalls

1. **CDN caching**: GitHub Pages caches 10 min. Users say "no change" → suggest `?v=N` or incognito
2. **index.html auto-redirect**: When no projects exist, the page auto-creates "默认项目" and jumps to workspace. To test the index page, localStorage must have at least one project
3. **Personal center dropdown** closes on outside click; uses `position: fixed` to avoid parent overflow clipping
4. **Version history menu** also uses `position: fixed` with JS-calculated coordinates to avoid panel clipping
5. **History overlay** (`#historyOverlay`) is `position: absolute` inside `.chat-panel`; toggle via `toggleHistoryOverlay()`. It covers the entire chat area when shown.
6. **Design tabs** use `activeDesignTab` variable and per-tab file history keys; each tab has its own `fileListN` and `uploadAreaN` with indexed IDs.
