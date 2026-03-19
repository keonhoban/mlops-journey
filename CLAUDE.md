# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

A Hugo-based static blog documenting an MLOps/ML Platform engineering journey. Content is written in Korean and English. The live site is at https://keonhoban.github.io/mlops-journey/.

## Hugo Commands

```bash
# Serve locally with live reload
hugo server -D

# Build the site (output to /public)
hugo --minify

# Create a new post
hugo new posts/<category>/<post-name>/index.md
```

Hugo version in use: **v0.145.0** (pinned in CI).

## Deployment

Pushing to `main` triggers `.github/workflows/deploy.yml`, which builds with `hugo --minify` and deploys to GitHub Pages via `peaceiris/actions-gh-pages@v3`. No manual deployment steps needed.

The PaperMod theme is a git submodule (`themes/PaperMod`). When cloning, use `git clone --recurse-submodules` or run `git submodule update --init` afterward.

## Content Architecture

```
content/
├── posts/          # Blog posts, organized by category subdirectory
│   ├── airflow/
│   ├── aws/
│   ├── kubernetes/
│   ├── mlflow/
│   ├── mlops-platform-e2e/
│   ├── mlops-platform-feature-store/
│   ├── mlops-platform-gitops/
│   ├── mlops-platform-observability/
│   ├── triton/
│   └── ...         # linux, network, python, online-judge, TroubleShoot, etc.
└── projects/       # Project-level documentation (11 projects)
```

Each post is a directory containing `index.md` (and optionally images). Front matter uses Hugo standard fields plus `categories`, `tags`, `weight`.

## Key Configuration

- **`config.toml`** — Hugo config: base URL, theme (`PaperMod`), menu, custom CSS path
- **`static/css/custom.css`** — Table styling (borders, alternating rows)
- **`layouts/partials/footer.html`** — Custom footer: scroll-to-top, code copy buttons, theme toggle

## Blog Series Integrity

Several blog posts form multi-part series (especially the `mlops-platform-*` categories). When editing content:
- Keep terminology and component names consistent across posts in the same series
- Code snippets, architecture diagrams, and configuration examples in posts should reflect the actual state of any referenced repositories/code
- The `projects/` pages summarize each series — keep them aligned with their corresponding `posts/` entries
