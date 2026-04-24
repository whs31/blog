# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
zola serve     # Dev server at http://localhost:1111 with live reload
zola build     # Build to public/
zola check     # Validate site and links
```

## Architecture

**Zola** static site generator (Rust-based). Templates use the Tera engine (Jinja2-like syntax).

- `content/` — Markdown posts and pages; nested dirs become URL paths
- `templates/` — Tera HTML templates; `base.html` is the root layout, `index.html` is the homepage, `macros.html` holds reusable components
- `sass/` — SCSS compiled by Zola; `main.scss` is the entry point; `_*.scss` files are component partials
- `static/` — Copied verbatim to `public/`; includes bundled JS libs (particles.js, sweet-scroll) and fonts
- `zola.toml` — Site config including `[extra]` values exposed to templates as `config.extra.*`

## Templating

Extra config values (username, links, bio, etc.) live in `zola.toml` under `[extra]` and are accessed in templates as `config.extra.field_name`. Content metadata goes in Markdown frontmatter and is accessed as `page.extra.*`.

## Deployment

Deployed on Vercel. `vercel.json` pins Zola to v0.14.0 — keep new features within that version's capabilities.
