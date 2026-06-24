# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A Hugo static site for **etuannv.com** — Tuan Nguyen's freelance developer portfolio and blog (Python, web scraping, automation, Django). Deployed to GitHub Pages at `etuannv.com` via the `etuannv/etuannv.github.io` repo, built and deployed automatically by `.github/workflows/deploy.yml` on every push to `main`.

## About Tuan Nguyen
An AI Automation, n8n & AI Agents | RAG Chatbots | Python Data Expert
I build AI automation that actually ships and keeps running — n8n & Make workflows, AI agents, and RAG chatbots — on top of 10+ years of Python automation and data engineering. Top Rated Plus · 100% Job Success · 67 jobs · 4,000+ hours.

Most freelancers can drag-and-drop a no-code flow. Few can handle the messy data underneath — pulling it, cleaning it, and feeding it reliably into your systems. That's what I've done at scale for a decade, and it's why my automations don't break in week two.

What I can build for you:
▸ AI automation & agents — n8n / Make workflows with AI steps that classify, extract, summarize, and decide; multi-step AI agents that use tools and take action (Claude, GPT, Gemini).
▸ RAG chatbots — assistants that answer from your own documents and data, with every answer grounded in a cited source (PostgreSQL/pgvector, embeddings, hybrid search).
▸ Data pipelines & web scraping — large-scale extraction, ETL, enrichment, and monitoring with Python, Scrapy, Selenium, and Playwright, including proxies and anti-bot handling.
▸ Integrations — HubSpot, GoHighLevel, Airtable, Google Workspace, Slack, Telegram/WhatsApp, REST APIs, and webhooks.

How I work:
▸ Consult first, then automate — we fix the real bottleneck, not just the obvious one.
▸ Every build ships with source code, documentation, and error handling, so it survives the handoff.
▸ Clear, proactive communication on US-friendly hours; honest about scope and cost up front.

10+ years in software, a Master's in Cyber Security, and a track record clients describe as creative, thorough, and reliable.

If a workflow is eating your team's hours — or you have an "AI setup" that sounds impressive but doesn't solve anything — message me with what's slowing you down, and I'll tell you exactly how I'd automate it.
- Upwork profile: https://www.upwork.com/freelancers/etuannv
- Freelancer profile: https://www.freelancer.com/u/etuannv

**Use the above skills description to write article about etuannv**


## Commands

```bash
hugo server -D       # local dev server with live reload + drafts, http://localhost:1313/
hugo --minify         # production build to ./public (mirrors CI)
hugo new posts/my-new-post.md       # scaffold a new blog post
hugo new projects/my-new-project.md # scaffold a new project page
```

There is no test suite or linter — verify changes by running `hugo server -D` and checking the page in a browser, or `hugo --minify` to confirm a clean production build.

Requires Hugo **extended** edition (theme uses SCSS/asset pipeline features). Theme is a git submodule (`themes/PaperMod`) — clone with `--recurse-submodules` or run `git submodule update --init --recursive` if `themes/PaperMod` is empty.

## Architecture

- **Single config file**: `hugo.yaml` — site params, menu, taxonomies (`categories`, `tags`), sitemap settings. No `config/` directory split.
- **Theme is PaperMod but fully overridden**: `themes/PaperMod` is installed as a submodule, but every template that matters is replaced by custom templates in `layouts/`. Don't look to the theme's own templates/partials for current behavior — check `layouts/` first.
- **Custom layouts** (`layouts/`):
  - `_default/baseof.html` — base HTML shell: head/meta/OG/Twitter tags, Google Fonts, JSON-LD `Person` schema (homepage only), wraps `partials/header.html` + `block "main"` + `partials/footer.html`.
  - `index.html` — homepage: hero, recent projects (first 4), latest posts (first 3).
  - `_default/single.html` — two-column post/project page: article + sticky sidebar (TOC + "hire me" widget).
  - `_default/list.html` — section list page (used by `/posts/`, `/projects/`) as a card grid.
  - `partials/header.html` / `partials/footer.html` — nav and footer, shared across all pages.
- **Content sections** (`content/`): `posts/` (blog), `projects/` (portfolio case studies, has `_index.md`), plus standalone pages `contact.md`, `services.md`. Front matter conventions (title/date/tags/categories/description) are documented in README.md — follow the existing pattern in nearby files when adding content.
- **Multilingual (en default + vi)**: configured in `hugo.yaml` via `defaultContentLanguage: en` + `defaultContentLanguageInSubdir: false` and a `languages:` block (per-language `params`/`menu`). English content lives at the root (e.g. `content/posts/foo.md` → `/posts/foo/`); the Vietnamese translation of the *same* file is `content/posts/foo.vi.md` → `/vi/posts/foo/` — Hugo links them as translations by matching filename. Static pages with explicit `url:` front matter (`contact.md`, `services.md`) need the `.vi.md` counterpart's `url:` set to the `/vi/...` path explicitly, since an explicit `url` bypasses automatic language prefixing. UI strings (buttons, labels, breadcrumbs) are NOT hardcoded in templates — they come from `i18n/en.yaml` / `i18n/vi.yaml` via `{{ i18n "key" }}`; add new keys to both files when adding template text. The language switcher (`partials/header.html`) and `hreflang` tags (`_default/baseof.html`) use `.AllTranslations`, so a page without a `.vi.md`/un-suffixed counterpart simply won't show a switcher link for the missing language.
- **Styling**: single hand-written stylesheet at `static/css/style.css` (no Tailwind/Bootstrap, no CSS framework). Dark theme only, driven by CSS custom properties defined at the top of the file (`--bg`, `--accent`, `--text`, etc.) — change tokens there rather than hardcoding colors in new components.
- **`public/`** is generated build output (committed for GitHub Pages history) — never hand-edit; it's overwritten by `hugo --minify`.
- **`static/`** files are copied as-is into `public/` (e.g. `CNAME`, legacy redirect page `duongsinhviet.html`).

See `README.md` for the full content/front-matter schema, CSS component reference, and SEO/structured-data details.
