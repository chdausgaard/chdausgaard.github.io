# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Personal academic website for Christoffer H. Dausgaard (PhD Candidate, Political Science, University of Copenhagen). Built with Hugo and the Hugo Blox (formerly Wowchemy/Academic) theme, deployed via GitHub Pages.

## Build and dev commands

```bash
# Local development server
hugo server -D --disableFastRender

# Production-like local build
hugo --gc --minify -b http://localhost:1313

# Reproduce CI build
hugo --gc --minify -b $URL
```

Hugo Extended is required. The authoritative version is pinned in `.github/workflows/publish.yaml` (currently 0.124.1). Netlify config (`netlify.toml`) pins 0.123.3 — the GitHub Actions version is canonical for deployment.

## Deployment

GitHub Pages via `.github/workflows/publish.yaml` on push to `main`. Netlify config exists as a secondary/legacy deploy target.

## Architecture

### Content structure

Content lives in `content/`. Publications use Hugo **page bundles** under `content/publication/` with three category subfolders:

- `content/publication/journal/` — published journal articles
- `content/publication/working/` — working papers
- `content/publication/chapters/` — book chapters

**Folder structure is load-bearing.** The publication listing templates use `strings.HasPrefix .File.Path "publication/journal/"` (etc.) to filter publications into sections. Moving publications between folders changes how they appear on the site.

Each publication bundle contains:
- `index.md` — front matter: `title`, `authors`, `date`, `publication`, `abstract`, `featured_graph`, `tags`
- Optional image files (referenced via `featured_graph` param)
- Optional PDF files (rendered as "View PDF" link)
- Optional `cite.bib`

### Template overrides (4 files in `layouts/`)

| File | Role |
|------|------|
| `layouts/publication/list.html` | Publications page — filters and renders publications by folder path into three sections |
| `layouts/_default/custom-publications.html` | Duplicate of `list.html` (vestigial — `list.html` is the one Hugo uses) |
| `layouts/partials/publication-card.html` | Renders individual publication cards with title, authors, venue, abstract, images via `Resources.GetMatch`, and PDF link |
| `layouts/blocks/about.html` | Author profile block override — conditionally hides portrait title and social icons |

### Custom styling

`assets/scss/custom.scss` (~234 lines) controls: container width (800px max), navbar alignment, publication image sizing, PDF button styling, square avatar, about widget tweaks.

### Configuration

All Hugo config lives in `config/_default/` as YAML files: `hugo.yaml` (main config), `params.yaml` (theme params), `menus.yaml` (navigation), `languages.yaml` (English only), `module.yaml` (theme imports).

`baseURL` in `hugo.yaml` is a placeholder (`https://example.com/`); the actual URL is set at build time via `-b` flag.

## BibTeX import workflow

`publications.bib` at repo root feeds `.github/workflows/import-publications.yml`. When the bib file changes on `main`, the workflow converts entries to Hugo page bundles and creates a PR automatically.

## Gotchas

- `enableGitInfo: false` locally but `HUGO_ENABLEGITINFO=true` on Netlify. GitHub Actions fetches full history for `.GitInfo`/`.Lastmod` support.
- Images referenced via `Resources.GetMatch` must stay in page bundles, not `static/`.
- Do not edit `public/` or `resources/_gen/` — these are generated outputs.
- Prefer adding/modifying partials in `layouts/partials/` over editing theme files directly.

## Delegation context

- Hugo Blox (Academic) theme — templates extend/override this theme's defaults
- Go modules manage theme dependencies (`go.mod`), no npm/bundler
- `.editorconfig`: UTF-8, 2-space indent, LF line endings
