# AGENTS.md

## Project overview

AI-3D Design Master (AI-3D设计大师) — a static single-page web application for 3D STL/STEP file preview and AI-assisted mechanical design. Deployed on GitHub Pages.

## Entry points

| File | Role |
|------|------|
| `index.html` | Project list page (landing), personal center, all modals |
| `workspace.html` | 3D workbench: viewer, chat, version history, export |
| `api/` | Vercel serverless functions (upload, modify, history) |
| `stl_tools.py` | Python STL processing utilities |

## Dev commands

```bash
# Local dev server (port 8080)
python3 -m http.server 8080 --directory /Users/lisa/.openclaw/workspace-technology/stl-preview

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

## Key conventions

- **All JS is inline** inside `<script>` tags in each HTML file — no external `.js` files
- **Color scheme** (workspace): `#0A0E1B` bg, `#12182B` card, `#2563EB` highlight, `#E5E7EB` text, `#9CA3AF` secondary
- **Color scheme** (index): `#0A0E1B` bg, `#12182B` card, `#1A213A` info bar, `#00D4FF` highlight, `#FFFFFF` text, `#8892B0` secondary
- **Font**: Inter from Google Fonts CDN
- **localStorage keys**: `stl_projects`, `stl_ai_models`, `stl_members`, `stl_upload_history`, `stl_versions_*`
- **gitignored**: `node_modules/`, `报价文档.md`

## Common pitfalls

1. **CDN caching**: GitHub Pages caches 10 min. Users say "no change" → suggest `?v=N` or incognito
2. **Port 3000 occupied**: A Next.js dev server often occupies 3000. Use port 8080 instead
3. **index.html auto-redirect**: When no projects exist, the page auto-creates "默认项目" and jumps to workspace. To test the index page, localStorage must have at least one project
4. **Personal center dropdown** closes on outside click; uses `position: fixed` to avoid parent overflow clipping
5. **Version history menu** also uses `position: fixed` with JS-calculated coordinates to avoid panel clipping
