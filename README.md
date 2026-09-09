# Geospatial Workflows

A Quarto-based site for sharing reproducible geospatial / remote sensing
workflows. Posts are Jupyter-flavored `.qmd` files: write narrative in
Markdown, execute Python code inline, and Quarto renders the whole thing
(text + code + static figures + interactive HTML widgets) into a static
website.

## Local setup

```bash
# 1. Install Quarto (one-time): https://quarto.org/docs/get-started/
# 2. Create a Python environment and install dependencies
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

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

This repo is set up to auto-publish to **GitHub Pages** via GitHub Actions
(`.github/workflows/publish.yml`) every time you push to `main`:

1. Push this repo to GitHub.
2. In the repo settings, go to **Pages** and set the source to the `docs/`
   folder... or, since the Action already pushes rendered HTML to the
   `gh-pages` branch, set the Pages source to that branch instead (simpler —
   recommended). Either approach works; pick one and stay consistent.
3. Push a commit — the Action will render the site and publish it.
   Your site will be live at `https://your-github-username.github.io/geospatial-workflows/`.

## Structure

```
geospatial-workflows/
├── _quarto.yml              # site config (nav, theme, output dir)
├── index.qmd                # homepage — auto-lists posts/
├── about.qmd                # bio / links page
├── styles.css               # custom CSS overrides
├── requirements.txt         # Python deps for rendering
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
