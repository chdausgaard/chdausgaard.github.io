# Copilot instructions for chdausgaard.github.io

This repository is a Hugo static site (Academic/Wowchemy-like theme). The guidance below highlights the project's structure, developer workflows, and concrete examples so an AI coding agent can be productive immediately.

1. Big picture
   - This is a Hugo-generated personal website. Source content lives in `content/`; generated output is `public/` (do not edit `public/`).
   - Layout overrides and templates live in `layouts/` (see `layouts/_default/custom-publications.html` and `layouts/partials/publication-card.html` for publication presentation patterns).
   - Theme metadata in `theme.toml` indicates an Academic-style theme. `config/_default/hugo.yaml` contains site-wide configuration (permalinks, taxonomies, imaging, etc.).

2. Build / dev / deploy commands (concrete)
   - Netlify build (used in CI): `hugo --gc --minify -b $URL` (see `netlify.toml`, HUGO_VERSION=0.123.3). Use the same flags for parity when building locally.
   - Local dev server: run Hugo's server to preview changes: `hugo server -D --disableFastRender` or `hugo server --gc --minify -b http://localhost:1313` when checking production-like output.
   - If editing SCSS or using advanced asset pipelines, use the Hugo Extended build (netlify uses a pinned HUGO_VERSION; match that locally when necessary).

3. Content & template conventions to follow
   - Publications are stored under `content/publication/` with subfolders such as `journal/`, `working/`, and `chapters/`.
   - Each publication is a page bundle (folder containing `index.md` plus resources like `cite.bib`, `graph1.png`). Templates expect page bundle resources; e.g. `layouts/partials/publication-card.html` uses `{{ .Resources.GetMatch .Params.featured_graph }}` to render a graph image.
   - Front matter commonly used in publication pages: `title`, `authors` (array), `abstract`, `publication` (venue string), `featured_graph` (filename of bundled image). When adding content, follow existing examples under `content/publication/*/*/index.md`.
   - Author pages are under `content/authors/` and may include avatar files next to `_index.md` (see `content/authors/admin/avatar.jpg`).
   - Do not move images referenced via `Resources.GetMatch` into `static/` unless you intentionally want a global static asset; prefer page bundles so Hugo image processing works.

4. Files & patterns to reference when making changes
   - Presentation of publications: `layouts/_default/custom-publications.html` and `layouts/partials/publication-card.html` — mimic their structure when adding new lists or cards.
   - Site config: `config/_default/hugo.yaml` — check `permalinks`, `taxonomies`, and `imaging` settings before changing URLs or image processing behavior.
   - CI/hosting: `netlify.toml` — Netlify sets `HUGO_ENABLEGITINFO` and uses `--minify`; follow these flags locally to reproduce production output.

5. Integration points & external dependencies
   - Netlify (builds from repo); uses Hugo binary pinned via `HUGO_VERSION` in `netlify.toml`.
   - Bibliography files are colocated in publication bundles as `cite.bib` (see multiple examples in `content/publication/**/cite.bib`). If you change citation rendering, update templates that read these files.

6. Typical change examples (follow these concrete steps)
   - Add a new working paper:
     - Create `content/publication/working/<slug>/`.
     - Add `index.md` with front matter: `title`, `authors: [..]`, `abstract`, `publication`, `featured_graph: "graph1.png"`.
     - Add `graph1.png` and `cite.bib` to the same folder (page bundle). Hugo templates will pick these up.
   - Change the publications listing layout: edit `layouts/_default/custom-publications.html` and reuse `partial "publication-card.html"` for consistency.

7. Project-specific gotchas
   - Many templates rely on file path checks, e.g. `strings.HasPrefix .File.Path "publication/journal/"` — keep publication files in the expected folder structure so lists and filters continue to work.
   - `config/_default/hugo.yaml` sets `enableGitInfo: false` locally, but Netlify sets `HUGO_ENABLEGITINFO=true` during builds. Be mindful of any template logic that uses Git info (e.g., lastmod) — test on both local and CI builds if relevant.
   - Avoid editing generated files in `public/` and `resources/_gen/` — they are output from Hugo.

8. If you need to change behavior not covered by templates
   - Prefer adding or modifying partials in `layouts/partials/` rather than editing theme files directly. This repo already overrides templates in `layouts/`.
   - When adding image processing or new shortcodes, update `config/_default/hugo.yaml` imaging settings if you need different defaults.

9. Where to run tests / verification
   - Quick verification: run `hugo server -D` and visit `http://localhost:1313` to visually inspect pages changed.
   - Reproduce production build: `hugo --gc --minify -b http://example.com` (or mimic Netlify env vars) and inspect the `public/` output.

Please review these instructions and tell me if you'd like more examples (for authors, slides, or publication bib handling) or if you want me to merge additional details from other docs.
