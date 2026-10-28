# Cursor Build Plan: AZ-104 + Copilot Studio Tutorial Site

> Drop this file in the root of an empty repo, open it in Cursor, and tell the agent:
> **"Read CURSOR_PLAN.md and execute it one phase at a time. Stop after each phase and show me what changed."**

---

## 0. What we're copying (research findings)

The reference site (https://microsoft.github.io/mcscatblog/) is:

- **Jekyll 4.x** using the **Chirpy theme (v7.x)**: https://github.com/cotes2020/jekyll-theme-chirpy
- Hosted on **GitHub Pages** (project site, so it uses a `baseurl`)
- Features to replicate:
  - Light / Dark / System theme toggle
  - Sidebar tabs: **Home, Categories, Tags, Archives, Resources, About, Authors**
  - **Custom home page**: hero ("Field notes for..."), stats line, **Featured** posts, **Browse by topic** cards with post counts, **Latest** posts grid with thumbnails, pagination
  - Post layout: title, subtitle tag, description, posted/updated dates, header image, author line, **read time**, **table of contents**, anchored headings, callouts (`> quote` with `{: .prompt-tip }`), image captions, category + tags footer, CC BY 4.0 license line, share buttons, **Further Reading** cards, "Recently Updated" and "Trending Tags" in the right panel
  - Post URL pattern: `/posts/<slug>/`, assets in `/assets/posts/<slug>/header.png`
  - RSS feed, Open Graph / Twitter card metadata, site search

We will use the Chirpy **starter template** (not a fork) so upgrades are painless, then add a custom home page and a few includes for tutorial-specific needs.

---

## 1. Decisions to make before starting (fill in)

| Item | Value |
|---|---|
| GitHub username | `<USERNAME>` |
| Repo name | `<REPO>` (use `<USERNAME>.github.io` for a root URL, otherwise a project repo like `learn-azure` which needs a `baseurl`) |
| Site title | `<e.g. "The Cloud Lab">` |
| Tagline | `<e.g. "AZ-104 study guides, hands-on workshops, and Copilot Studio field notes">` |
| Author name / links | `<NAME>`, GitHub, LinkedIn |
| Language / timezone | `en`, `America/Chicago` |

**Cursor: if any value above is still a placeholder, ask me before proceeding. Do not invent them.**

---

## 2. Information architecture

### Categories (top-level topics, shown as cards on the home page)

| Category slug | Display name | What goes in it |
|---|---|---|
| `az-104` | AZ-104 Azure Administrator | Exam-aligned study guides and labs |
| `workshops` | Workshops | Multi-part, hands-on, follow-along workshops |
| `copilot-studio` | Copilot Studio | Tutorials, patterns, and field notes |

Optional sub-categories for AZ-104 (Chirpy supports two levels): one per exam domain, such as identity and governance, storage, compute, networking, and monitoring/backup.
**Cursor: verify the current domain names and weights against the official Microsoft Learn AZ-104 study guide before creating them. Don't rely on memory, the exam outline gets revised.**

### Tags
Lowercase, hyphenated: `entra-id`, `rbac`, `vnet`, `storage`, `bicep`, `powershell`, `azure-cli`, `mcp`, `skills`, `orchestration`, `beginner`, `lab`, etc.

### Content types (use a `kind` front matter field)
`tutorial`, `lab`, `workshop`, `cheatsheet`, `field-notes`

### Series support (for workshops and multi-part tutorials)
Front matter: `series: "az-104-networking"` and `series_order: 2`. Rendered by a custom include (Phase 5).

---

## 3. Target repo structure

```
.
├── .github/workflows/pages-deploy.yml    # from Chirpy starter
├── .devcontainer/                        # from Chirpy starter (Ruby env, no local install needed)
├── .cursor/rules/site.mdc                # Cursor rules (Phase 7)
├── _config.yml
├── _data/
│   ├── authors.yml
│   ├── contact.yml
│   ├── share.yml
│   └── resources.yml                     # drives the Resources page
├── _includes/
│   ├── series-nav.html                   # prev/next within a series
│   └── home-hero.html
├── _layouts/
│   └── home.html                         # override of Chirpy's home
├── _tabs/
│   ├── categories.md
│   ├── tags.md
│   ├── archives.md
│   ├── resources.md                      # custom tab
│   ├── authors.md                        # custom tab
│   └── about.md
├── _posts/
│   └── 2026-10-28-welcome.md
├── assets/
│   ├── img/                              # avatar, favicon, site OG image
│   └── posts/<slug>/header.png           # per-post assets, same convention as reference
├── scripts/
│   └── new-post.sh                       # scaffolds a post (Phase 6)
├── index.html                            # custom home page content
└── Gemfile
```

---

## 4. Phases

### Phase 1: Scaffold from the Chirpy starter
**Tasks**
1. Initialize the repo from https://github.com/cotes2020/chirpy-starter (copy the files in; don't keep its git history).
2. Keep `.devcontainer/` so I can run the site in a container instead of installing Ruby locally.
3. Run `bundle install` and `bundle exec jekyll s`, confirm it serves locally.
4. Fill in `_config.yml`: `title`, `tagline`, `description`, `url`, `baseurl`, `lang`, `timezone`, `github.username`, `social.name`, `social.links`, `theme_mode` (leave empty so Light/Dark/System toggle works), `avatar`, `toc: true`, `comments` off, `pageviews` off.
5. Set `paginate: 12`, since the reference shows 12 posts per page.
6. Add a `_posts/` welcome post so the build isn't empty.

**Acceptance**: `jekyll s` renders at `http://localhost:4000<baseurl>/`, theme toggle works, no build warnings.

---

### Phase 2: Deploy to GitHub Pages
**Tasks**
1. Use the workflow in `.github/workflows/pages-deploy.yml` from the starter (Ruby 3.x, `jekyll build`, `htmlproofer` step).
2. In repo **Settings → Pages**, set **Source = GitHub Actions**. (Cursor can't do this part, so give me exact click-by-click instructions.)
3. Confirm `url` and `baseurl` in `_config.yml` match the real Pages URL.
4. Push to `main`; verify the Action goes green and the site loads.

**Acceptance**: live URL loads with correct CSS/images (a wrong `baseurl` is the #1 cause of a broken, unstyled site).

---

### Phase 3: Navigation, tabs, and taxonomy
**Tasks**
1. Sidebar order: Home, Categories, Tags, Archives, **Resources**, About, **Authors**. Use `order:` in each `_tabs/*.md` front matter.
2. Create `_tabs/resources.md`, rendered from `_data/resources.yml` (title, URL, description, type). Seed with Microsoft Learn AZ-104 learning path, the AZ-104 exam page, Azure free account, and Copilot Studio docs. **Use real links only. Fetch and verify each URL before adding it.**
3. Create `_tabs/authors.md` rendered from `_data/authors.yml`.
4. Add `_data/authors.yml` with me as the first author (name, GitHub URL, optional avatar).
5. Add category descriptions (shown on the home page cards) via a `_data/topics.yml`: slug, title, description, and icon.

**Acceptance**: all 7 tabs resolve with no 404s, and the sidebar looks like the reference.

---

### Phase 4: Custom home page (the "wow" part)
Override Chirpy's home layout with an `index.html` plus `_layouts/home.html` that renders, in order:

1. **Hero**: small eyebrow text (`// <Site name>`), big headline, one-line subhead, two buttons ("Browse by topic" scrolls to `#collections`, "Latest posts" scrolls to `#latest`), and a stats line (`{{ site.posts | size }} posts · {{ topics | size }} topics · Updated <cadence>`, computed in Liquid, not hard-coded).
2. **Featured**: posts with `featured: true` in front matter, max 3, card layout with image, category·tag line, title, description, and date.
3. **Browse by topic**: grid of cards from `_data/topics.yml`, each showing title, description, and live post count, linking to the category page.
4. **Latest**: paginated grid of recent posts (image, categories, title, description, date), with a numbered pager.

**Constraints**
- Pure Liquid + CSS using Chirpy's CSS variables so dark mode works automatically. No JS frameworks.
- Put custom styles in `assets/css/jekyll-theme-chirpy.scss` (Chirpy's documented override point), not by editing theme gems.
- Must be responsive down to 375px.

**Acceptance**: with 4+ dummy posts, home shows Featured, Topics with counts, and Latest with working pagination, in both light and dark mode.

---

### Phase 5: Post experience
**Tasks**
1. Confirm the Chirpy post layout provides: header image, read time, TOC, anchored headings, share buttons, license footer, and Recently Updated / Trending Tags in the right panel.
2. Set license to **CC BY 4.0** in `_config.yml` (or my choice, so ask).
3. Add `_includes/series-nav.html`: if a post has `series`, show "Part N of M" plus Previous/Next links, sorted by `series_order`. Include it at the top and bottom of the post.
4. Add a **"Further reading"** block: render `related:` front matter (list of post slugs) as cards.
5. Add a **"Prerequisites"** and **"Estimated time"** callout pattern for labs, with documented markdown and CSS using Chirpy's prompt classes (`{: .prompt-info }`, `{: .prompt-warning }`, `{: .prompt-tip }`, `{: .prompt-danger }`).
6. Make sure code blocks have a language label and copy button (Chirpy default), and test with `powershell`, `bash`, `json`, `yaml`, and `bicep` (fallback to `text` if Rouge doesn't highlight Bicep).

**Acceptance**: a sample 3-part workshop shows series nav correctly, and a sample lab shows prerequisites and callouts.

---

### Phase 6: Authoring workflow
**Tasks**
1. `scripts/new-post.sh <slug> "<Title>" <category>` creates `_posts/YYYY-MM-DD-<slug>.md` with the front matter template below plus `assets/posts/<slug>/`.
2. Add a `README.md` section, "How to write a post," covering the front matter, image naming (`header.png` + inline images), callouts, and how to preview.
3. Add **three real sample posts**, one per category, with placeholder content clearly marked `TODO`, so the layout can be judged.

**Post front matter template**
```yaml
---
title: "Post title"
description: "One or two sentence summary used on cards and in social previews."
date: 2026-10-28 09:00:00 -0500
last_modified_at: 2026-10-28 09:00:00 -0500
categories: [az-104]          # one primary category (optionally a sub-category)
tags: [entra-id, rbac, lab]
kind: lab                     # tutorial | lab | workshop | cheatsheet | field-notes
series: ""                    # optional series slug
series_order: 0               # optional
featured: false
authors: [<author-key>]
image:
  path: /assets/posts/<slug>/header.png
  alt: "Descriptive alt text"
related: []                   # optional list of post slugs
---
```

---

### Phase 7: Quality, SEO, and guardrails
**Tasks**
1. Add `.cursor/rules/site.mdc` with: never edit files under the theme gem; custom styles go in `assets/css/`; every post needs `description`, `image.alt`, and a category from `_data/topics.yml`; use lowercase-hyphen tags; verify links before committing.
2. Add `htmlproofer` to CI to catch broken internal links.
3. SEO: confirm `jekyll-seo-tag`, `jekyll-sitemap`, and `jekyll-feed` (RSS) are active, and add a default site OG image at `assets/img/og-default.png`.
4. Accessibility pass: alt text on all images, color contrast OK in both themes, and keyboard-focus visible on cards.
5. Add a custom `404.html` that links back to Home and the Categories tab.

**Acceptance**: CI is green, Lighthouse (Performance/Accessibility/SEO) is ≥90 on home and a post page.

---

### Phase 8 (optional): Nice-to-haves
- Site search (Chirpy's built-in search works out of the box. Check it before adding Algolia).
- "Updated weekly" badge based on the newest post date.
- Downloadable lab files: `assets/downloads/<slug>/` with a consistent "Download resources" callout.
- Giscus comments (GitHub Discussions based), with `comments.provider: giscus`.
- Custom domain (add `CNAME`, configure DNS, and enforce HTTPS).

---

## 5. Rules for Cursor while executing

1. **One phase at a time.** Stop and summarize after each phase (files changed, how to verify).
2. **Don't invent facts.** All Azure/AZ-104/Copilot Studio statements, exam domains, and URLs must come from official Microsoft Learn or docs pages you actually fetched. If you can't verify, mark `TODO: verify`.
3. **Don't modify theme internals.** Override via `_layouts/`, `_includes/`, `_sass`/`assets/css`, and `_config.yml` only.
4. **Keep it buildable.** Run `bundle exec jekyll build` after every phase and fix warnings.
5. **Ask before** adding any new gem, JS dependency, or external service.
6. Use relative, `baseurl`-aware links in Liquid (`{{ '/path' | relative_url }}`).

---

## 6. Final deliverable checklist

- [ ] Site live on GitHub Pages with correct styling
- [ ] Light/Dark/System toggle works
- [ ] Custom home: Hero, Featured, Topics with counts, Latest with pagination
- [ ] Tabs: Home, Categories, Tags, Archives, Resources, About, Authors
- [ ] Post layout: TOC, read time, series nav, callouts, CC license, further reading
- [ ] 3 sample posts (AZ-104, Workshop, Copilot Studio) + a welcome post
- [ ] `scripts/new-post.sh` and a README authoring guide
- [ ] CI green (build + htmlproofer)
- [ ] `.cursor/rules/site.mdc` in place
