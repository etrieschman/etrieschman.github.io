# etrieschman.github.io

Personal site built with [Quarto](https://quarto.org). Deployed to GitHub Pages.

## Pages

- `index.qmd` — homepage
- `papers.qmd` — publications
- `garden.qmd` — live garden dashboard (reads the `garden/` submodule)

## Local development

```bash
git clone --recurse-submodules https://github.com/etrieschman/etrieschman.github.io
cd etrieschman.github.io
uv sync                       # install Python deps (jupyter, plotly, pandas, garden)
uv run quarto preview         # live-reload preview at http://localhost:4444
```

## How the garden page works

`garden/` is a git submodule pointing at [github.com/etrieschman/garden](https://github.com/etrieschman/garden). The `garden.qmd` notebook:

1. Imports `garden.app.GardenApp` from the submodule
2. Opens the SQLite at `garden/garden-data/garden.sqlite`
3. Queries beds / plants / events / recommendations / weather
4. Renders Plotly figures and pandas tables inline

To pull the latest garden data + code into this site:

```bash
git submodule update --remote garden
git add garden && git commit -m "bump garden submodule"
git push                      # GH Actions rebuilds + redeploys
```

## Deployment

GitHub Actions renders the Quarto site and deploys via `actions/deploy-pages`
(modern artifact-based deployment, no `gh-pages` branch needed). See
`.github/workflows/publish.yml`.

**One-time setup:** in repo settings → Pages → "Build and deployment", set
**Source** to **GitHub Actions**. After that, every push to `main` rebuilds
and redeploys.

## Adding a new page

1. Create `<name>.qmd`
2. Add it to `_quarto.yml` under `project.render` and `website.navbar.left`
3. `quarto preview` to check
4. Commit + push

Markdown works for static pages; add a `jupyter: python3` block in the
frontmatter for executable Python (with Plotly, pandas, etc.).
