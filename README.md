# avallecam.github.io

Personal website of Andree Valle-Campos, built with [Quarto](https://quarto.org) and published at <https://avallecam.github.io>.

## How it works (short version)

1. Edit `.qmd` files locally and preview them with `quarto preview`.
2. Commit and push to `master`.
3. A GitHub Action (`.github/workflows/publish.yml`) renders the site and pushes the HTML to the `gh-pages` branch.
4. GitHub Pages serves the `gh-pages` branch. The live site updates about 1–2 minutes later.

You never commit the rendered HTML yourself. `_site/` is gitignored.

## Repository structure

| Path | What it is |
|---|---|
| `_quarto.yml` | Site config: navbar, footer, theme, freeze settings |
| `index.qmd` | Home page |
| `bio.qmd`, `publications.qmd`, `resources.qmd`, `about.qmd` | Top-level pages (Vitae menu + About) |
| `blog/index.qmd` | Blog listing page |
| `blog/posts/<year>-<type>-<slug>/index.qmd` | One folder per post (workshops, lectures, talks) |
| `figure/` | Images used across the site |
| `styles.css` | Custom CSS |
| `_extensions/` | Quarto extensions (e.g. academicons for the ORCID icon) |
| `_freeze/` | Cached results of R code chunks. **Commit this.** |
| `renv.lock`, `renv/`, `.Rprofile` | R package versions, managed with `renv` |
| `_extras/` | Backup or draft material that isn't rendered |

## Local setup (first time on a machine)

Requirements: R, [Quarto CLI](https://quarto.org/docs/get-started/), and RStudio (optional).

```r
# Open avallecam.github.io.Rproj. renv activates automatically via .Rprofile.
renv::restore()   # installs the package versions listed in renv.lock
```

## Everyday editing

```bash
quarto preview    # live-reloading local preview in the browser
quarto render     # full build into _site/ to check that everything renders
```

### Adding a blog post (workshop, lecture or talk)

Create `blog/posts/<year>-<type>-<short-slug>/index.qmd`:

```yaml
---
title: "Title of the post"
date: "2024-05-01"
categories: [Workshop]    # Workshop | Lecture | Talk
image: ../../../figure/<image-file>.png
---
```

The categories drive the Vitae → Workshops, Lectures and Talks menu links.

### Pages that run R code (`publications.qmd`)

The site uses `execute: freeze: auto`. R code is re-run only when its source file changes, and the results are stored in `_freeze/`. CI uses the frozen results, so:

> After editing a page with R code, run `quarto render` locally and **commit the updated `_freeze/` folder** along with the `.qmd`.

### Adding or updating R packages

```r
install.packages("pkg")
renv::snapshot()          # then commit renv.lock
```

CI installs packages from `renv.lock`.

## Deploying

Push to `master`. The **Quarto Publish** workflow does the rest. You can also trigger it manually: GitHub → **Actions** → *Quarto Publish* → **Run workflow**.

### One-time setup (already done, kept here for reference)

1. Run `quarto publish gh-pages` once locally. This creates the `gh-pages` branch and `_publish.yml`.
2. GitHub → **Settings → Pages** → Source: *Deploy from a branch*, Branch: `gh-pages`, folder `/ (root)`.
3. GitHub → **Settings → Actions → General** → Workflow permissions: *Read and write*.

## Troubleshooting

- **Site didn't update:** check the **Actions** tab for a failed run and open its log.
- **CI fails on an R package:** the package is probably missing from `renv.lock`. Run `renv::snapshot()` locally and push.
- **CI fails executing R code:** `_freeze/` probably wasn't committed. Render locally and commit it.
- **Old site still showing:** confirm that Pages points at `gh-pages`, then hard-refresh the browser.
