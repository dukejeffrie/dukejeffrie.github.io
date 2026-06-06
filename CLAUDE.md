# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Hugo-based blog/personal website for TMS Tech (tmsgreen.tech), a technology consultancy focused on software development and leadership content.

This is a standard Hugo static site generator setup with minimal customization.
 
- Uses the Ananke theme without extensive modifications
- Content is organized by topic (dev vs leadership)
- No complex build pipeline - just Hugo's standard workflow
- Published on GitHub Pages (via git push).

**Primary Language:** Brazilian Portuguese (unless otherwise specified)
**Target Audience:** Senior developers, product people, and owners of small tech companies
**Writing Style:** Sober, concise, technical content that keeps the author's personality while maintaining clarity

## Repository Structure

```
/docs/                    # Hugo site root (all Hugo work happens here)
  ├── content/           # Markdown content files
  │   ├── post/         # Blog posts
  │   │   ├── dev/      # Development/technical posts
  │   │   └── leadership/ # Leadership posts
  │   ├── hire/         # Hiring/services pages
  │   └── page/         # Static pages
  ├── themes/           # Hugo themes (ananke is active theme)
  ├── layouts/          # Custom layout overrides
  │   └── shortcodes/   # Custom shortcodes (e.g., rawhtml.html)
  ├── static/           # Static assets
  ├── public/           # Generated site (git ignored, deployed)
  └── hugo.toml         # Hugo configuration

/local/                  # Planning documents and CV (not deployed)
```

## Common Commands

**Working directory for all Hugo commands:** Always run Hugo commands from the `/docs` directory.

### Development
```bash
cd docs
hugo server -D              # Start dev server with drafts
hugo server                 # Start dev server (published content only)
```

### Building
```bash
cd docs
hugo                        # Build site to public/
hugo -D                     # Build including drafts
```

### Content Creation
```bash
cd docs
hugo new content/post/dev/my-post.md        # New dev post
hugo new content/post/leadership/my-post.md # New leadership post
```

## Content Guidelines

### Front Matter Format
Blog posts use TOML front matter (between `+++` markers):

```toml
+++
title = "Post Title"
date = 2025-06-18T06:35:00-03:00
draft = false
type = "post"
summary = "Brief summary"
tags = ["desenvolvimento", "ciência de dados"]
+++
```

**Important:** Preserve front matter exactly when editing posts. The markers (`+++` or `---`) and configuration must remain intact.


## Site Configuration

- **Theme:** Ananke (located in `docs/themes/ananke/`)
- **Domain:** tmsgreen.tech (via `docs/CNAME`)
- **Default Language:** Portuguese (`pt`)
- **Main Section:** `post`
- **Recent Posts:** Shows 10 most recent

## Custom Components

### Shortcodes
- `rawhtml` - Allows raw HTML in markdown content (in `docs/layouts/shortcodes/rawhtml.html`)

## Deployment

The site is deployed to GitHub Pages. The `docs/public/` directory contains the generated static site.

**Main branch:** `master` (use this for pull requests)
**Current branch:** `main` (check branch before creating PRs)

### Content Sections
- `content/post/dev/` - Technical and development articles
- `content/post/leadership/` - Leadership and management content
- `content/hire/` - Services and hiring information
- `content/page/` - General static pages

### Writer skill

Always load the writer skill when editing or refining blog posts.
## Git Workflow

Untracked files in the repository include work-in-progress posts and local planning documents. When committing:
- Only commit finalized blog posts in `docs/content/post/`
- Avoid committing files from `local/` directory unless specifically needed
- Check that images referenced in posts are committed alongside the post
- never push to remote.

