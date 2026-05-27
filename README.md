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

## Updating the dashboard (bumping the submodule)

**Note to self — the mental model:** this repo does *not* automatically track
the garden repo. The `garden/` submodule is a **pinned pointer to one specific
commit** of the garden repo — frozen in time. When the build runs, it checks
out exactly that commit and renders from its `garden.sqlite`. So logging a
watering or pushing new engine code in the garden repo changes **nothing** here
until I move the pointer forward. "Bumping the submodule" = moving that pointer
to the latest garden commit.

It's a **two-repo, two-push** dance:

```bash
# 1. In the GARDEN repo: log/change + push, so there's a new commit to point at.
cd ~/dev/garden
uv run garden log watered patio-bed --amount-l 5
git commit -am "watered" && git push

# 2. In THIS repo: move the pointer to garden's latest, then push.
cd ~/dev/etrieschman.github.io
git submodule update --remote garden    # fast-forwards the pointer to garden's origin/main
git add garden                          # stages the new pointer (shows as "garden <hash>")
git commit -m "bump garden submodule"
git push                                # GH Actions rebuilds + redeploys (~1-2 min)
```

The second push is what makes the dashboard update. Forget it, and the site
keeps showing whatever garden looked like at the old pinned commit.

**Useful checks:**

```bash
# What commit is the submodule currently pinned to?
git -C garden rev-parse --short HEAD

# Is the pointer behind garden's latest? (shows "garden (new commits)" if so)
git submodule update --remote garden && git status
```

**Gotchas:**
- After a fresh `git clone`, use `--recurse-submodules` (see Local development) — otherwise `garden/` is an empty directory and the build fails.
- The bump shows up in `git status` as a one-line change to `garden` (a commit hash, not file contents). That's normal — you're committing "which commit to point at," not the garden files themselves.
- The garden repo must be **pushed** first (step 1). `--remote` fast-forwards to garden's `origin/main`, so an unpushed local garden commit won't be picked up.

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
