# Architecture playbook

For future maintainers (human or AI) working on this repository.

## Stack

- **Hugo (extended)**, module-based, importing `github.com/HugoBlox/hugo-blox-builder/modules/blox-tailwind`
  as the base theme (navbar, footer, head, Tailwind CSS pipeline, search UI chrome).
- Modules are vendored into `_vendor/` (`hugo mod vendor`) for reproducible CI
  builds without a live Go module fetch. Re-run `hugo mod vendor` after
  bumping the theme version in `go.mod`.
- No Node/Pagefind pipeline. Search uses the theme's built-in `flexsearch`
  client-side provider (`params.features.search.provider: flexsearch`),
  which is sufficient at this site's content volume. If the publication list
  grows large, consider adding Pagefind — see the original build spec at
  `https://gking.harvard.edu/mysite/files/ACADEMIC_SITE_PROMPT.md`.
- Deployed via GitHub Actions (`.github/workflows/deploy.yml`) to GitHub
  Pages on every push to `main`.

## Content types

- `content/_index.md` — homepage, `type: landing`, rendered by the fully
  custom `layouts/landing/list.html` (photo + intro hero, research-area
  accordions). Does **not** use the theme's default widget-block landing
  system.
- `content/publication/<slug>/index.md` — one folder per publication.
  Rendered by `layouts/publication/single.html` (custom) and listed by
  `layouts/publication/list.html` (custom "Writings" page: tabs, sidebar
  filters, sort, BibTeX export — all client-side vanilla JS, no build step).
- `content/cv/`, `content/research/`, `content/contact/`, `content/people/`
  — plain pages using the theme's default `single.html` except `people`,
  which has a custom `layouts/people/list.html`.

## Why `/people/`, not `/authors/`

Hugo's built-in `authors` **taxonomy** (`taxonomies.author: authors` in
`hugo.yaml`, used so publication pages can resolve author names to pages)
auto-generates a term-list page at `/authors/`. A content section at the same
path would collide and fail the build. The human-facing "Co-authors" page
lives at `/people/` instead; the `authors` taxonomy is used only internally by
`publication/single.html` to resolve author names.

## Research areas

`data/research_areas.json` is the single source of truth for the homepage
accordions and the Writings page's research-area filter dropdown. Each area
has a list of `tags`; a publication is associated with an area if any of its
front-matter `tags:` match. There's no separate manual linking step — tag
your publications and the homepage/filters update automatically.

## Styling

`assets/css/custom.css` defines the warm earth-tone palette as CSS custom
properties (`--color-bg`, `--color-text`, `--color-accent`, `--color-link`,
etc.) and is loaded after the theme's own Tailwind + `custom` color-theme
CSS (`assets/css/themes/custom.css`, which supplies the theme's
`--hb-primary-*` / `--hb-secondary-*` scales consumed by theme chrome like
buttons and the navbar). Most custom layouts use inline `style="color:
var(--color-text)"` rather than Tailwind color utility classes, so the
palette stays in one place.

## Extensibility hooks, not template overrides, where possible

The theme exposes a hook system (`layouts/_partials/hooks/<hook-name>/*.html`,
auto-discovered via `os.ReadDir` at build time) for `head-start` and
`footer-start`. This site uses it for:
- `layouts/_partials/hooks/head-start/mysite-discovery.html` — the opt-in
  `<meta name="generator">` + homepage schema.org block, gated by
  `params.mysite.discovery`.
- `layouts/_partials/hooks/head-start/favicon-legacy.html` — legacy
  `favicon.ico` link tag.

The footer credit line (`params.mysite.credit`) required a full override of
`layouts/_partials/components/footers/minimal.html` instead, since there's no
`footer-end` hook — only `footer-start`, which would render above the
existing footer content rather than below it.

## Known simplifications (v1)

- **"See Also" auto cross-linking** is not implemented. With a single
  publication there's nothing to cross-link yet; revisit once there are
  several publications/talks/software entries (see the original build spec's
  "Automatic 'See Also' Cross-Linking" section for the intended
  scoring approach).
- **Pagefind** static search is not wired in; flexsearch is used instead (see
  above).
- **`i18n/en.yaml`** was not created — custom layouts hardcode English
  strings directly rather than routing through Hugo's i18n system, since
  this site has no translation requirement.
