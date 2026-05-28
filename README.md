# etrieschman.github.io

Personal site built with [Quarto](https://quarto.org). Deployed to GitHub Pages.

## Pages

- `index.qmd` — homepage
- `papers.qmd` — publications
- `projects.qmd` — grid of project tiles linking to external Pages sites

The site is pure markdown — no Python execution at build time. Each project
listed on `projects.qmd` is its own repo with its own Pages deploy; this site
just links out.

## Local development

```bash
git clone https://github.com/etrieschman/etrieschman.github.io
cd etrieschman.github.io
quarto preview         # live-reload preview
```

No Python or `uv` needed.

## Deployment

GitHub Actions renders the Quarto site and deploys via `actions/deploy-pages`
(modern artifact-based deployment, no `gh-pages` branch needed). See
`.github/workflows/publish.yml`. Every push to `main` rebuilds and redeploys.

**One-time setup:** in repo settings → Pages → "Build and deployment", set
**Source** to **GitHub Actions**.

## Adding a new page

1. Create `<name>.qmd`
2. Add it to `_quarto.yml` under `project.render` and (if it should be in the nav) `website.navbar.left`
3. `quarto preview` to check
4. Commit + push

## Adding a project tile

Add an entry under `listing.contents` in `projects.qmd`:

```yaml
- title: "Project name"
  description: "One-sentence description."
  path: "https://etrieschman.github.io/<repo>/"
```

Tiles open in a new tab (small script at the bottom of `projects.qmd`).
