# etuannv.com — Personal Freelancer Portfolio Site

A fully custom **Hugo** static site for **Tuan Nguyen** (`etuannv`), a freelance developer with 15+ years of experience in Python, web scraping, web automation, and Django. The site is deployed to GitHub Pages at **[https://etuannv.com](https://etuannv.com)** via the `etuannv/etuannv.github.io` repository on the `main` branch.

---

## Table of Contents

- [Project Purpose](#project-purpose)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Hugo Configuration](#hugo-configuration)
- [Content Architecture](#content-architecture)
- [Layouts & Templates](#layouts--templates)
- [Styling (CSS)](#styling-css)
- [Navigation & Menus](#navigation--menus)
- [SEO & Structured Data](#seo--structured-data)
- [Deployment](#deployment)
- [Local Development](#local-development)
- [Adding Content](#adding-content)

---

## Project Purpose

This is a **freelancer portfolio and blog** site. Its goals are:

1. **Showcase services** — Web scraping, web automation, Python scripting, API integrations, Django applications.
2. **Display portfolio projects** — Detailed case studies of real, completed client work.
3. **Publish a technical blog** — Tutorials and tips related to Python, Selenium, Playwright, proxies, macOS/Git workflows, and more.
4. **Convert visitors into clients** — Every page has clear CTAs pointing to the `/contact/` page and external freelancing profiles (Freelancer.com, Fiverr).

---

## Tech Stack

| Layer | Technology |
|---|---|
| Static Site Generator | [Hugo](https://gohugo.io/) |
| Theme Base | PaperMod (installed at `themes/PaperMod/`) — **but overridden entirely by custom layouts** |
| Custom Layouts | Go HTML templates in `layouts/` |
| Styling | Fully custom CSS in `static/css/style.css` (no Tailwind, no Bootstrap) |
| Fonts | IBM Plex Sans (body) + JetBrains Mono (brand/code) via Google Fonts |
| Deployment | GitHub Pages via `etuannv/etuannv.github.io` |
| Domain | `etuannv.com` — configured via `CNAME` file |

---

## Repository Structure

```
etuannv.com/
│
├── hugo.yaml                  # Hugo site configuration (single config file)
├── CNAME                      # Custom domain for GitHub Pages (etuannv.com)
├── etuannv.com.code-workspace # VS Code workspace file
│
├── archetypes/
│   └── default.md             # Default front matter template for new content
│
├── assets/                    # Hugo asset pipeline (currently unused)
│
├── content/                   # All Markdown content
│   ├── contact.md             # Contact page (/contact/)
│   ├── services.md            # Services page (/services/)
│   ├── posts/                 # Blog posts section (/posts/)
│   │   └── *.md
│   └── projects/              # Portfolio projects section (/projects/)
│       ├── _index.md          # Projects list page title/description
│       └── *.md
│
├── data/                      # Hugo data files (currently unused)
├── i18n/                      # Internationalization strings (currently unused)
│
├── layouts/                   # Custom Hugo templates (override PaperMod)
│   ├── 404.html               # 404 error page
│   ├── index.html             # Homepage template
│   ├── _default/
│   │   ├── baseof.html        # Base HTML shell (head, header, footer)
│   │   ├── list.html          # Section list page (projects, posts, tags)
│   │   └── single.html        # Individual post/project page (with sidebar)
│   └── partials/
│       ├── header.html        # Site-wide header with responsive nav
│       └── footer.html        # Site-wide footer with copyright
│
├── static/                    # Static files copied as-is to /public
│   ├── CNAME
│   ├── duongsinhviet.html     # Redirector/alias page
│   └── css/
│       └── style.css          # All custom site styles (~576 lines)
│
├── themes/
│   └── PaperMod/              # PaperMod theme (largely overridden by custom layouts)
│
└── public/                    # Hugo build output (deployed to GitHub Pages)
```

---

## Hugo Configuration

**File:** `hugo.yaml`

| Key | Value | Notes |
|---|---|---|
| `baseURL` | `https://etuannv.com/` | Production URL |
| `title` | `etuannv.com` | Site title (shown in `<title>` and brand logo) |
| `languageCode` | `en` | English |
| `paginate` | `10` | Posts per list page |
| `enableRobotsTXT` | `true` | Generates `robots.txt` automatically |
| `buildDrafts` | `false` | Drafts are excluded from builds |
| `params.author` | `Tuan Nguyen` | Used in `<meta name="author">` |
| `params.description` | `Freelance Developer | Python | Web Scraping | Automation | 15+ years experience` | Default meta description |
| `params.keywords` | `["web scraping", "python", "automation", "freelancer", "django"]` | Default meta keywords |

### Homepage Hero Params

```yaml
params:
  homeInfoParams:
    Title: "Hi, I'm Tuan Nguyen 👋"
    Content: >
      I'm a freelance developer with **15+ years** of experience...
```

### Social Icons

| Icon | URL |
|---|---|
| github | https://github.com/etuannv |
| twitter | https://x.com/etuannv |
| email | mailto:etuannv@gmail.com |

### PaperMod Feature Flags

| Flag | Value |
|---|---|
| `ShowReadingTime` | `true` |
| `ShowShareButtons` | `false` |
| `ShowPostNavLinks` | `true` |
| `ShowBreadCrumbs` | `true` |
| `ShowCodeCopyButtons` | `true` |
| `ShowToc` | `true` |
| `TocOpen` | `false` |
| `profileMode.enabled` | `false` |

### Taxonomies

```yaml
taxonomies:
  category: categories
  tag: tags
```

---

## Content Architecture

### Sections

| Section | URL | File Location |
|---|---|---|
| Homepage | `/` | `layouts/index.html` |
| Services | `/services/` | `content/services.md` |
| Projects | `/projects/` | `content/projects/*.md` |
| Blog | `/posts/` | `content/posts/*.md` |
| Contact | `/contact/` | `content/contact.md` |

### Front Matter Schema

**Blog Post (`content/posts/*.md`)**
```yaml
---
title: "Post Title"
date: 2026-01-01
tags: ["python", "web-scraping"]
categories: ["posts"]
description: "Short description for meta and card previews."
---
```

**Project (`content/projects/*.md`)**
```yaml
---
title: "Project Name"
date: 2025-01-01
tags: ["python", "django", "automation"]
categories: ["projects"]
description: "One-line project description used in cards and meta."
---
```

**Static Page (`content/contact.md`, `content/services.md`)**
```yaml
---
title: "Page Title"
date: 2026-06-02
description: "Meta description."
layout: "single"
url: "/contact/"
---
```

### Current Portfolio Projects

| Project | File | Tags |
|---|---|---|
| Price Tracking System | `projects/price-tracking-system.md` | python, web-scraping, django, automation |
| Etsy Ads Reporting Tool | `projects/etsy-ads-reporting-tool.md` | python, selenium, etsy, dashboard |
| ShipStation Labor Tracking | `projects/labor-tracking-system.md` | python, django, shipstation, automation, dashboard |
| Database of Licenses | `projects/database-of-licenses.md` | — |
| Power Price Tracking | `projects/power-price-tracking.md` | — |

### Current Blog Posts

| Post | File |
|---|---|
| Best Proxies for Web Scraping | `posts/best-proxies-for-web-scraping.md` |
| Etsy Ads Reporting Tool Download Guide | `posts/etsy-ads-reporting-tool-download-daily-weekly-reports-with-ease.md` |
| GitHub Token Authentication (macOS) | `posts/github-token-authentication-macos.md` |
| Python Get Current Time by Timezone | `posts/python-get-current-time-by-timezone.md` |
| Python Using Playwright with Proxy | `posts/python-using-playwright-with-proxy.md` |
| Revolutionize Workforce Productivity (ShipStation) | `posts/revolutionize-workforce-productivity-with-our-shipstation-integrated-labor-tracking-system.md` |
| Updating GitHub Auth with Token on macOS | `posts/updating-github-authentication-using-token-on-macos-replacing-password.md` |

---

## Layouts & Templates

All templates are in `layouts/` and **override** the PaperMod theme defaults.

### `layouts/_default/baseof.html` — Base Shell

The root HTML template for every page. Provides:
- `<head>` with charset, viewport, dynamic `<title>`, meta description, meta keywords, Open Graph tags, Twitter Card tags.
- Inline SVG favicon (💻 emoji).
- Google Fonts: **IBM Plex Sans** (400, 500, 600, 700) + **JetBrains Mono** (400, 500).
- Schema.org `Person` structured data JSON-LD injected on homepage only.
- Link to `/css/style.css`.
- `{{ partial "header.html" . }}` → `{{ block "main" . }}` → `{{ partial "footer.html" . }}`.

### `layouts/index.html` — Homepage

Extends `baseof.html`. Renders:
1. **Hero section** — availability badge, gradient headline, description, 3 CTA buttons (`/services/`, `/projects/`, `/contact/`), stats row (15+ years, 190+ projects, Top Rated, Worldwide), skill pills.
2. **Recent Projects** — first 4 pages from `projects` section as cards.
3. **Latest Posts** — first 3 posts from `posts` section (date + title list).

### `layouts/_default/single.html` — Single Page

Two-column layout (`post-layout`):
- **Left (article):** breadcrumb, `<h1>`, date, reading time, categories, tags, full Markdown content.
- **Right (sidebar):** auto-generated Table of Contents, "Need something similar built?" hire widget with buttons to `/contact/`, Freelancer.com, and Fiverr.

Previous/Next navigation at article bottom.

### `layouts/_default/list.html` — Section List

Renders all pages in a section as a responsive card grid. Cards show: date, title (linked), description, tags, "View →" link.

### `layouts/partials/header.html`

Responsive navbar: brand logo (links `/`), hamburger toggle (mobile), nav links from `hugo.yaml` menu with active state detection.

### `layouts/partials/footer.html`

Single-line footer: copyright year (dynamic `{{ now.Year }}`), links to Freelancer.com and Fiverr.

---

## Styling (CSS)

**File:** `static/css/style.css` (~576 lines)

### CSS Custom Properties (Design Tokens)

```css
:root {
  --bg: #0f1117;          /* Page background — near-black dark */
  --bg2: #1a1d27;         /* Secondary background */
  --surface: #1e2130;     /* Card/surface background */
  --border: #2a2d3e;      /* Border color */
  --accent: #4f8ef7;      /* Primary accent — blue */
  --accent2: #7c6af5;     /* Secondary accent — purple */
  --text: #e2e8f0;        /* Primary text — off-white */
  --text-muted: #8892a4;  /* Secondary/muted text */
  --green: #34d399;       /* Availability dot / success */
  --radius: 8px;          /* Default border radius */
  --font-mono: 'JetBrains Mono', 'Fira Code', monospace;
}
```

### Key Components

| Component | Class(es) | Description |
|---|---|---|
| Navbar | `.navbar`, `.brand`, `.nav-links` | Flex navbar, 60px tall, sticky-ish |
| Hero | `.hero`, `.hero-desc`, `.hero-stats`, `.hero-skills` | Full landing hero with stats + skill pills |
| CTA Buttons | `.btn`, `.btn-primary`, `.btn-secondary`, `.btn-outline` | Blue/ghost/outline button variants |
| Cards | `.card`, `.card-grid`, `.card-footer`, `.card-link` | Responsive 3-column grid of project/post cards |
| Post Layout | `.post-layout`, `.post`, `.post-header`, `.post-content` | Two-column article + sidebar |
| Sidebar | `.post-sidebar`, `.sidebar-widget`, `.hire-widget` | Sticky sidebar with TOC + hire CTA |
| Tags | `.tag`, `.tags` | Pill-style tag badges |
| Availability | `.hero-availability`, `.availability-dot` | Green pulsing "available" badge |
| Mobile Nav | `.nav-toggle`, `.nav-links.open` | Hamburger toggle for responsive nav |

### Design Language

- **Dark theme only** — background `#0f1117`, no light mode toggle.
- **Blue accent** (`#4f8ef7`) for primary actions and links.
- **Monospace font** used for the brand/logo to reflect developer identity.
- **Gradient headline** on hero using `linear-gradient(135deg, --text, --accent)` with `-webkit-background-clip: text`.

---

## Navigation & Menus

Defined in `hugo.yaml` under `menu.main`:

| Item | URL | Weight |
|---|---|---|
| Services | `/services/` | 10 |
| Projects | `/projects/` | 20 |
| Blog | `/posts/` | 30 |
| Contact | `/contact/` | 40 |

---

## SEO & Structured Data

- **`robots.txt`** — auto-generated by Hugo (`enableRobotsTXT: true`).
- **`sitemap.xml`** — auto-generated; `changefreq: weekly`, `priority: 0.5`.
- **Open Graph** — `og:type`, `og:url`, `og:title`, `og:description` on every page.
- **Twitter Card** — `summary` card type with site `@etuannv`.
- **Canonical URL** — `<link rel="canonical">` on every page.
- **JSON-LD Schema.org** — `Person` entity on homepage with `name`, `url`, `email`, `sameAs` (GitHub, Freelancer.com, Fiverr), `jobTitle`, `description`.

---

## Deployment

The site deploys to **GitHub Pages** from the `etuannv/etuannv.github.io` repository on the `main` branch.

- The `public/` directory is the Hugo build output committed to the GitHub Pages repo.
- The custom domain `etuannv.com` is set via the `CNAME` file in both `static/` and `public/`.
- `static/duongsinhviet.html` is a legacy alias/redirect page served as-is.

---

## Local Development

**Prerequisites:** [Hugo extended](https://gohugo.io/installation/) installed.

```bash
# Start local dev server with live reload
hugo server -D

# Build production output to /public
hugo --minify
```

The dev server runs at `http://localhost:1313/` by default.

---

## Adding Content

### New Blog Post

```bash
hugo new posts/my-new-post.md
```

Edit the generated file in `content/posts/my-new-post.md`. Change `draft: true` to `draft: false` when ready to publish.

### New Project

```bash
hugo new projects/my-new-project.md
```

Edit in `content/projects/my-new-project.md`. Set `categories: ["projects"]` and add relevant tags.

### Recommended Tags

`python` · `web-scraping` · `selenium` · `playwright` · `django` · `automation` · `proxies` · `etsy` · `shipstation` · `dashboard` · `database` · `github` · `macos` · `timezone` · `reporting`

---

## Owner / Contact

| | |
|---|---|
| **Name** | Tuan Nguyen |
| **Handle** | `etuannv` |
| **Email** | etuannv@gmail.com |
| **GitHub** | https://github.com/etuannv |
| **Twitter/X** | https://x.com/etuannv |
| **Freelancer.com** | https://www.freelancer.com/u/etuannv |
| **Fiverr** | https://www.fiverr.com/etuannv |
| **WhatsApp** | +84 962 018 279 |
| **Telegram** | @etuannv |
| **Timezone** | UTC+7 (Vietnam) — available Mon–Sat, 8am–10pm |
