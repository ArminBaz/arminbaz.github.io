# arminbazza.github.io

Personal academic site, built with [al-folio](https://github.com/alshedivat/al-folio) v1 and served from GitHub Pages.

## Why this repo is so small

al-folio v1 ships its layouts, includes, styles and JavaScript as Ruby gems (`al_folio_core` and friends), so none of that lives here. What's in this repo is only your content plus a little configuration. If you want to see the theme's internals:

```bash
bundle show al_folio_core
```

## Where things live

| What you want to change | File |
| --- | --- |
| Homepage bio, subtitle, cover photo | `_pages/about.md` |
| Publications | `_bibliography/papers.bib` |
| CV page | `_data/cv.yml` (and `assets/pdf/ArminBazarjani_CV.pdf` for the download) |
| Social icons | `_data/socials.yml` |
| Venue badge colors on the publications page | `_data/venues.yml` |
| Site title, name, feature toggles | `_config.yml` |
| Images | `assets/img/` |

## Deployment

GitHub Pages can't build this site itself — al-folio uses plugins (jekyll-scholar in particular) that aren't on the Pages allowlist, and Pages runs Jekyll in safe mode where those can't load. Instead, `.github/workflows/deploy.yml` runs a full `bundle exec jekyll build` on every push and uploads the result to Pages directly.

**One-time setup:** Settings → Pages → Source: **GitHub Actions**.

Everything stays on one branch. This repo holds source only; the built HTML is never committed anywhere. After the initial setup, pushing to `master` is all you ever need to do. The build takes a few minutes; watch it under the Actions tab.

If a run fails, the built site is untouched — the deploy job only runs after a clean build, so a broken push leaves the live site as it was.

## Running it locally

Docker is the least painful route:

```bash
docker run --rm -p 8080:8080 -v "$PWD:/srv/jekyll" amirpourmand/al-folio
```

Then open http://localhost:8080.

Without Docker:

```bash
bundle install
bundle exec jekyll serve --livereload
```

You'll need Ruby 3.x and ImageMagick (`brew install imagemagick`) for the responsive image generation.

## The blog

There's a working blog at `/blog/`, but nothing links to it: `_pages/blog.md` has `nav: false`, so it stays out of the navbar, and the homepage doesn't list recent posts.

To publish, drop a file in `_posts/` named `YYYY-MM-DD-slug.md`. See `_posts/2026-08-18-hello.md` for the front matter, then delete it.

To make the blog public:

1. Set `nav: true` in `_pages/blog.md`.
2. Optionally uncomment the `latest_posts` block in `_pages/about.md` to show recent posts on the homepage.
3. Optionally uncomment `rss_icon: true` in `_data/socials.yml`.

## The cover photo

`_layouts/about.liquid` is a local copy of al-folio's about layout with a full-width banner added at the top. It's driven by the `cover:` block in `_pages/about.md`:

```yaml
cover:
  image: cover.jpg # file in assets/img/
  height: 220px
  position: center 55% # which slice of the photo stays visible
  alt: Yosemite Valley
```

Delete the `cover:` block to remove the banner. Delete `_layouts/about.liquid` to go back to stock al-folio.

Because this file shadows a gem-owned layout, al-folio can tell you when the upstream version changes:

```bash
bundle exec al-folio upgrade overrides audit
bundle exec al-folio upgrade overrides diff _layouts/about.liquid
```

## Adding a publication

Append a BibTeX entry to `_bibliography/papers.bib`. Useful non-standard fields al-folio understands:

| Field | Effect |
| --- | --- |
| `abbr` | Badge in the left margin; color it in `_data/venues.yml` |
| `selected={true}` | Also shows on the homepage under "selected publications" |
| `bibtex_show={true}` | Adds a "BibTeX" button |
| `abstract` | Adds an expandable "Abs" button |
| `pdf`, `html`, `code`, `slides`, `poster`, `video`, `website` | Link buttons (a bare filename resolves inside `assets/`) |
| `arxiv`, `doi` | Link buttons built from the identifier alone |
| `preview` | Thumbnail from `assets/img/publication_preview/` — also set `enable_publication_thumbnails: true` in `_config.yml` |
| `additional_info` | Free text appended after the venue, e.g. *Under review at NeurIPS 2026* |
