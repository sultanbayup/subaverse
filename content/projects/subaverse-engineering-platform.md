---
title: 'Subaverse: Personal Engineering Platform'
date: '2026-05-16'
draft: false
tags: ['hugo', 'github-actions', 'github-pages', 'hextra', 'cloudflare']
repo: "https://github.com/sultanbayup/subaverse"
summary: "Personal engineering platform built with Hugo and Hextra. Serves as a portfolio, blog, and documentation hub. Deployed to GitHub Pages via GitHub Actions, served at sultanbp.com through Cloudflare."
related_docs: []
---

## Problem

Engineering notes were scattered across Notion, blog posts lived on a legacy platform, and project documentation was nowhere in particular. I wanted a single version-controlled home for all of it — something I could write on consistently without the tooling getting in the way.

The requirements:

- Fast builds, simple deployment
- Markdown-first — no CMS
- Zero-maintenance hosting
- No frontend framework to maintain long-term
- Structured enough to grow into a real knowledge base

## Solution

A static site built with [Hugo](https://gohugo.io/) and the [Hextra](https://imfing.github.io/hextra/) theme. Deployed automatically to GitHub Pages via GitHub Actions on every push to `main`, served at `sultanbp.com` through Cloudflare DNS.

Hugo generates the site with no runtime dependencies — single binary, sub-second builds. Hextra provides a docs-and-blog layout with search, dark mode, syntax highlighting, and Mermaid support out of the box.

Content is split into four sections:

| Section | Purpose |
|---|---|
| **Docs** | Technical reference — cloud, kubernetes, devops, linux |
| **Blog** | Engineering posts, troubleshooting writeups, personal reflections |
| **Projects** | Portfolio entries in problem/solution/outcome format |
| **About** | Background and platform purpose |

## Stack

| Layer | Technology | Reason |
|---|---|---|
| Static Site Generator | Hugo | Fast builds, single binary, no runtime |
| Theme | Hextra | Docs + blog layout, dark mode, search |
| Hosting | GitHub Pages | Free, zero-maintenance, automatic HTTPS |
| CI/CD | GitHub Actions | Native to the repo, no external service |
| DNS / CDN | Cloudflare | CDN, DDoS protection, DNS management |
| Content | Markdown | Version-controlled, portable |

## Architecture

A push to `main` triggers the full build and deploy sequence automatically.

```mermaid
flowchart LR
    A([Developer]) -->|git push to main| B[GitHub Repository]
    B --> C[GitHub Actions]
    C --> D[Install Go + Hugo Extended]
    D --> E[hugo mod tidy]
    E --> F[hugo --minify]
    F --> G[Upload Pages Artifact]
    G --> H[Deploy to GitHub Pages]
    H --> I[sultanbp.com via Cloudflare]
```

The site is fully static — no database, no server-side rendering, no runtime process. GitHub Pages serves pre-built HTML from the Actions artifact. Cloudflare handles DNS and CDN caching in front of it.

### Design decisions

**Hugo over Next.js / Gatsby** — A JS-based SSG adds build complexity and dependency churn. Hugo is a single binary with no node_modules. Builds finish in under a second.

**GitHub Pages over a self-hosted VPS** — Running a VPS for a static site adds operational overhead with no real benefit. GitHub Pages handles hosting, HTTPS, and CDN for free.

**GitHub Actions for CI/CD** — Native to the repo, no external CI to manage. The pipeline is a single YAML file. Hugo version is pinned to avoid breakage from upstream releases.

**Cloudflare for DNS** — Adds CDN and DDoS protection on top of GitHub Pages. Keeps the proxy layer independent from GitHub, which makes future hosting changes easier.

**`HUGO_BASEURL` as a repository variable** — The base URL is stored as a GitHub Actions variable rather than hardcoded in the workflow. Changing the domain only requires updating the variable.

## Content Strategy

Each section has a clear scope:

- **Docs** — "how does this work?" — objective, implementation-focused
- **Blog** — "what did I learn?" — personal experience, engineering opinions
- **Projects** — "what was built?" — architecture decisions, outcomes

Keeping these separate prevents the site from becoming a mix of tutorials, opinions, and portfolio pieces that don't belong together.

## Outcome

- All engineering content in one version-controlled place
- Sub-second page loads — static HTML, no JS framework
- Deploys complete in under 40 seconds on every push to `main`
- Zero hosting cost, zero server maintenance
- Custom domain at `sultanbp.com` with HTTPS via Cloudflare
