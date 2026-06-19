# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 🤖 Role: Principal Technical Writer & Developer Advocate
Claude acts as a **Principal Technical Writer (15+ years exp)**. Every response must prioritize reducing cognitive load and maintaining technical precision.

### Diátaxis Framework Implementation
Before drafting, categorize the content into one of these quadrants:
1. **Tutorials**: Learning-oriented. Step-by-step, guaranteed success.
2. **How-to Guides**: Task-oriented. Practical steps for specific goals.
3. **Reference**: Information-oriented. Technical specs, APIs, CLI commands.
4. **Explanation**: Understanding-oriented. Architecture, "Why" behind design.

### Core Writing Guidelines
- **Audience Focus**: First paragraph must define target audience, prerequisites, and learning outcomes.
- **BLUF (Bottom Line Up Front)**: Start paragraphs with the core conclusion.
- **Tone**: Professional, active voice. **Zero AI fluff** (No "In today's fast-paced world...").
- **Language**: Content in **Traditional Chinese (zh-TW)**; technical terms/code in English.
- **Code Standards**: Samples must be **complete and executable** (no `//...`). Explain "Why" via comments.


## Commands

```bash
yarn dev          # Start development server at localhost:3000
yarn build        # Production build (runs postbuild.mjs after)
yarn serve        # Start production server
yarn lint         # ESLint with auto-fix
yarn analyze      # Build with bundle size analyzer
```

Pre-commit hooks (via Husky) run lint-staged automatically on `git commit`.

## Architecture Overview

This is a **Next.js 15 App Router** blog powered by **Contentlayer2** for MDX content management.

### Content Flow

1. MDX/Markdown files in `data/blog/` are processed by Contentlayer (`contentlayer.config.ts`)
2. Contentlayer generates typed content and metadata into `.contentlayer/generated/`
3. Remark/Rehype plugin pipeline handles GFM, math (KaTeX), syntax highlighting (Prism), heading anchors, and blockquote alerts
4. Contentlayer's `onSuccess` hook (end of `contentlayer.config.ts`) generates `app/tag-data.json` (tag index) and `public/search.json` (kbar search index) on every build/dev run
5. `scripts/postbuild.mjs` runs after `next build` and generates the RSS feed via `scripts/rss.mjs` (writes to `out/` when `EXPORT=1`, else `public/`)

### Blog Post Frontmatter

```yaml
---
title: 'Post Title'         # required
date: '2026-01-01'          # required, YYYY-MM-DD
tags: ['tag1', 'tag2']
lastmod: '2026-01-02'
draft: false
summary: 'Summary text'
authors: ['default']        # maps to data/authors/*.mdx
layout: 'PostLayout'        # PostLayout | PostSimple | PostBanner
pinned: true                # optional, shows pinned badge on home page
---
```

### Directory Structure

- `data/blog/` — Blog content in nested subdirectories (e.g., `ai/`, `backend/java/`, `homelab/`). Subdirectory path becomes the slug.
- `data/authors/` — Author profiles as MDX files
- `data/siteMetadata.js` — Global site config (title, URL, analytics ID, social links)
- `data/headerNavLinks.ts` — Navigation items
- `app/` — Next.js App Router pages and layouts
- `components/` — Shared React components
- `layouts/` — Post layouts (`PostLayout`, `PostSimple`, `PostBanner`) and listing layouts (`ListLayout`, `ListLayoutWithTags`)
- `css/` — Global styles (Tailwind v4)

### Key Conventions

- Blog is primarily in **Traditional Chinese (zh-TW)**; technical terms and code remain in English
- Package manager is **Yarn** (v3.6.1) for local dev; CI uses npm
- Deployment target is **GitHub Pages** via static export, driven by `.github/workflows/pages.yml` on push to `main`. CI runs `npm ci` then `npm run build` with `EXPORT=1 UNOPTIMIZED=1 BASE_PATH=<pages base>`, then publishes the `out/` directory. `next.config.js` switches `output: 'export'` only when `EXPORT` is set
- Tags are lowercase strings; `app/tag-data.json` is auto-generated — do not edit manually
- The `pinned` frontmatter field is a custom addition beyond the base Pliny template

