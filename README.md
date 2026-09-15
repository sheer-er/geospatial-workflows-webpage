# Geospatial Workflows

A Quarto-based site for sharing reproducible geospatial / remote sensing
workflows. Posts are Jupyter-flavored `.qmd` files: write narrative in
Markdown, execute Python code inline, and Quarto renders the whole thing
(text + code + static figures + interactive HTML widgets) into a static
website.

## Local setup

**Pick ONE of the two options below — don't activate both at once.** They're
alternative ways of installing the same kind of thing (Python packages), and
stacking them just causes confusing "module not found" errors.

```bash
# 1. Install Quarto (one-time): https://quarto.org/docs/get-started/

# 2a. Option A: pip + venv (requirements.txt now includes everything,
#     including the GFS/NHC post's deps)
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
# to leave this environment later: deactivate

# 2b. Option B: conda/micromamba (recommended if cfgrib/eccodes fails to
#     build under pip — conda-forge handles that dependency more reliably)
# If you already ran Option A: run `deactivate` first to exit .venv
micromamba create -f environment.yml
micromamba activate geospatial-workflows
# to leave this environment later: micromamba deactivate

# 3. Preview the site locally (live-reloads on save)
quarto preview
```

## Adding a new post

1. Create a new folder under `posts/`, named `YYYY-MM-DD-slug/`.
2. Add an `index.qmd` inside it. Copy the structure from
   `posts/2026-09-08-example-workflow/index.qmd` as a starting template.
3. Set the YAML front matter (`title`, `date`, `categories`, `jupyter: python3`).
4. Run `quarto preview` to check it renders, then commit and push.

## Publishing

This repo auto-publishes to **GitHub Pages** via GitHub Actions
(`.github/workflows/publish.yml`). Three triggers, so expensive posts don't
re-render more often than they need to:

- **Push to `main`**: renders the *entire* site (every post, including the
  live-data ones) and commits the rendered `docs/` output back to `main`.
- **Daily cron** (`0 12 * * *`, i.e. 12:00 UTC): renders *only* the NHC/GFS
  tropical cyclone page, which tracks active storms day to day.
- **Monthly cron** (`0 13 15 * *`, the 15th of each month): renders *only*
  the city-temperature-records page, which only needs to refresh once a
  completed calendar month is available — this one is the slow render
  (~15-20 min, ~250 station fetches), so it's kept off the daily/push path.

`quarto render` writes to `docs/` (see `_quarto.yml`'s `output-dir: docs`),
and the workflow commits that folder straight to `main` — **not** a
separate `gh-pages` branch. So in the repo's **Settings → Pages**, set the
source to the `docs/` folder on the `main` branch. Push a commit (or
trigger the workflow manually from the **Actions** tab via
`workflow_dispatch`) and the site goes live at
`https://your-github-username.github.io/geospatial-workflows/`.

## Structure

```
geospatial-workflows/
├── _quarto.yml              # site config (nav, theme, output dir)
├── index.qmd                # homepage — auto-lists posts/
├── about.qmd                # bio / links page
├── styles.css               # custom CSS overrides
├── requirements.txt         # Python deps for simple, pip-only posts
├── environment.yml          # conda env for posts needing cfgrib/eccodes/cartopy
├── posts/
│   └── YYYY-MM-DD-slug/
│       └── index.qmd        # one folder per post
└── .github/workflows/
    └── publish.yml          # auto-render + deploy on push
```

## Notes

- Keep heavy data files (large rasters, big NetCDFs) out of the repo —
  download them at render time in the post's code, or link out to where
  they live (e.g., a cloud bucket, Zenodo, GitHub Releases).
- If a workflow needs a real backend (an API, a database, live compute)
  rather than a pre-rendered notebook, that's a signal to build it as a
  separate small app (e.g., Flask/FastAPI + Leaflet) and link to it from a
  post here, rather than trying to force it into a static site.
