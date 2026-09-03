# srivastav-ayush.github.io

Personal website of Ayush Srivastav — System Engineer, ECU Cooling at Volvo Group.
Static, hand-built, no framework and no build step. GitHub Pages serves the files exactly as committed.

## Pages

| URL | File |
|---|---|
| `/` | `index.html` (mirror of `Homepage.dc.html`) |
| `/work/` | `work/index.html` (mirror of `work/Work.dc.html`) |
| `/publications/` | `publications/index.html` (mirror of `publications/Publications.dc.html`) |
| `/talks/` | `talks/index.html` (mirror of `talks/Talks.dc.html`) |
| `/writing/` | `writing/index.html` (mirror of `writing/Articles.dc.html`) |
| `/reading/` | `reading/index.html` (mirror of `reading/Reading.dc.html`) |
| `/cv/` | `cv/index.html` (mirror of `cv/CV.dc.html`) |
| `/writing/<Article-Name>.dc.html` | the eight long-form essays |

Each section folder has an `index.html`, so GitHub Pages serves the clean URL (`/work/`) as
well as the explicit one (`/work/index.html`). Links in the markup use the **explicit** form so
the site also works when opened from a plain file server or local folder, where bare folder
URLs 404. The `.dc.html` file is the editable working copy; the `index.html` next to it must
stay byte-identical.

## Editing

All content lives in plain JavaScript arrays inside each page file. All styling is inline.
Read `AI_README.md` before making changes — it documents the data schemas, the colour
system, the mirroring rules, and the things that will silently break the site.

## Deploying

1. Repo must be named exactly `srivastav-ayush.github.io`.
2. Show hidden files before uploading — `.nojekyll` must be included.
3. `git add -A && git commit && git push` from the repo root, then
   Settings → Pages → Deploy from branch → `main` / `/(root)`.

`uploads/` and `site-deploy/` are listed in `.gitignore`: `uploads/` is raw source material
(~39 MB of original photos and PDFs) and `site-deploy/` is a build mirror — neither belongs
in the deployed repo.

## Housekeeping

- `robots.txt` and `sitemap.xml` are at the root; update `sitemap.xml` when adding a page.
- Every page carries `lang="en"`, a canonical URL, Open Graph/Twitter metadata,
  print styles, and a `prefers-reduced-motion` block.
- Publication figures are real files in `assets/img/publications/` (named after each
  entry's `slotId`), not base64 in a sidecar — dropping a new figure in the editor
  writes a `.image-slots.state.json`, which then takes precedence.
