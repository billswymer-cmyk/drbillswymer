# Updating this site

This site is built with [Hugo](https://gohugo.io) (extended) and the Hugo Blox
theme. Content lives as plain Markdown files — no build scripts to learn for
day-to-day edits.

## Easiest path: the update form

Fill out `SITE_UPDATE_FORM.txt` (repo root) — new publication, bio edit,
teaching change, contact update, co-author, photo, whatever — and hand it to
your AI assistant. It has its own instructions at the bottom telling the
assistant exactly which files to touch and how. Reuse the same file for every
future update; just clear your old answers first.

The rest of this document is the manual, by-hand version of the same edits,
for anyone who'd rather skip the form.

## Adding a publication

Create a new folder under `content/publication/`, e.g.
`content/publication/my-new-paper/index.md`:

```yaml
---
title: "Paper Title"
date: 2026-01-15
authors: ["Bill Swymer", "Co-Author Name"]
publication_types: ["journal_article"]   # or: book, book_chapter, report, data, software, presentation
publication: "Journal Name, Vol(Issue), Pages"
abstract: "..."
links:
  - type: doi
    url: "https://doi.org/..."
    label: "Publisher's Version"
tags: ["keyword"]
---
```

Add matching tags from `data/research_areas.json` if you want the paper to
show up under a Research Area on the homepage and Writings page.

## Editing your bio, homepage intro, or teaching page

Edit the Markdown body in `content/cv/_index.md`, `content/_index.md`, or
`content/research/_index.md` directly.

**Note on the current content:** the homepage intro, full bio, teaching page,
and research areas were drafted from your Ph.D., your one indexed
publication, and your UA Culverhouse profile — because the intake form was
mostly blank. Please review all of it for accuracy before treating the site
as final, and expand the teaching page with your actual course list (there's
a commented-out example at the bottom of `content/teaching/_index.md`).

## Adding a co-author

Add their name (exactly as it appears in a publication's `authors:` list) and
a link to `data/coauthors.json`:

```json
{
  "Darren K. Hayunga": "https://www.terry.uga.edu/directory/darren-k-hayunga/"
}
```

The Co-Authors page (`/people/`) is auto-populated from every publication's
`authors:` field — **co-author links were resolved by web search and should
be double-checked**; fix or remove any that point to the wrong person.

## Changing your photo

Replace `static/images/bill-swymer.jpg` with a new image of the same
filename, or update the path referenced in `layouts/landing/list.html`.

## Rebuilding and previewing locally

```bash
hugo server
```

Then open http://localhost:1313/. Pushing to the `main` branch on GitHub
automatically rebuilds and redeploys the live site via GitHub Actions
(`.github/workflows/deploy.yml`).

## Footer credit / discovery marker

Two settings in `hugo.yaml` under `params.mysite` control the small
"Created using GaryKing.org/mysite" footer line (`credit`) and an invisible,
visitor-safe `<meta>`/schema.org marker that lets the mysite team find and
support sites built with their tool (`discovery`). Set either to `false` to
turn it off.
