# Ayush Srivastav — Personal Website (srivastav-ayush.github.io)

This document explains how the site is built and how to make changes yourself using any AI chat (or by hand), then deploy to GitHub Pages. Attach this file plus the page you want to change to a chat, and the model will have everything it needs.

---

## 1. What the site is

A fully static website — no build step, no framework install, no server. Every file is served as-is by GitHub Pages. Open any `.dc.html` file in a browser locally and it works.

| File | Purpose |
|---|---|
| `index.html` | The homepage, served directly at `/` (a full copy of `Homepage.dc.html` — no redirect, so there's no flash on load) |
| `Homepage.dc.html` | Hero, milestones log, experience timeline |
| `Work.dc.html` | Project portfolio (list + detail views in one file) |
| `Publications.dc.html` | Journal papers, book chapters, conference papers, patents |
| `Talks.dc.html` | Conference talks timeline |
| `Articles.dc.html` | Writing (currently a placeholder) |
| `CV.dc.html` | Full CV with sidebar |
| `support.js` | The rendering engine (see §2). **Never edit.** |
| `image-slot.js` | Drag-and-drop image component. **Never edit.** |
| `.image-slots.state.json` | Stores images dropped into slots (publication figures, hero photo fallback). **Do not delete** — thumbnails break. |
| `.nojekyll` | Tells GitHub Pages to serve the hidden files above. **Do not delete.** |
| `assets/` | All images: `img/` (photo, og-card), `img/portfolio/`, `logos/`, `talks/` |

## 2. How a page file works

Each `.dc.html` file has two halves:

**A) The template** — between `<x-dc>` and `</x-dc>`. Regular HTML with three special things:

- `{{ name }}` — a "hole" filled with a value computed by the JavaScript half.
- `<sc-for list="{{ items }}" as="item">…</sc-for>` — repeats its contents once per item (like a loop).
- `<sc-if value="{{ condition }}">…</sc-if>` — renders its contents only if the condition is truthy.

All styling is **inline** (`style="…"`) using CSS variables like `var(--accent)`, `var(--text-muted)` — there is no separate stylesheet. Hover effects use a custom `style-hover="…"` attribute. The `<helmet>` block at the top holds fonts, the favicon, and the few global CSS rules (body reset, link colors, keyframes).

**B) The logic** — inside `<script type="text/x-dc" data-dc-script>` at the bottom. A JavaScript class where:

- **The data lives in plain arrays** near the top of the class (this is where 95% of edits happen):
  - Homepage: `rawTimeline` (experience entries) and `allMilestones` (the milestone log)
  - Work: `allProjects`
  - Publications: `allJournalPapers`, `allBookChapters`, `allConferencePapers`, `allPatents`
  - Talks: `allTalks`
- `theme(dark)` returns the color palette (light + dark mode). Identical copy in every page — a color change must be made in **all 5 page files**.
- `renderVals()` computes every `{{ hole }}` the template uses (filtering, sorting, styles).

`support.js` glues the two halves together (it's a small React-based runtime). You never need to touch it.

## 3. How to make common edits

Always edit the data arrays, not the HTML template, unless changing layout.

**Add a milestone (Homepage):** add one object to `allMilestones`:
```js
{ year: 2026, month: 9, dateLabel: "Sep 2026", category: "talk", text: "Presented …" },
```
Categories: `career, education, talk, publication, patent, award`. Add `upcoming: true` for future events. Order in the array doesn't matter — it's sorted automatically. ⚠️ Watch for accidental double commas `},,` — they corrupt the count.

**Counts are automatic vs manual:** the hero stats (Patents / Publications / Talks) are **computed** from `allMilestones` — they update themselves. The colored pills on experience entries (`pills: [...]` in `rawTimeline`) are **manual numbers** — update them by hand when relevant.

**Add a publication/patent:** copy an existing object in the right array in `Publications.dc.html`, change the fields. If you add a milestone for it too, keep the dates matching. Give it a new unique `slotId`; its figure can then be dragged onto the card in the Claude design tool, or skip the figure.

**Add a talk:** copy an object in `allTalks` in `Talks.dc.html`. Put the event photo in `assets/talks/` and reference it as `customImage`. Two talks sharing a `pairKey` merge into one card (same paper, two venues).

**Add a project:** copy an object in `allProjects` in `Work.dc.html`. Put 1–6 images in `assets/img/portfolio/`; first entry in `images` is the cover. `category` must be `work | research | academic | passion`.

**Change text/copy:** search for the text in the file and edit it — it's either in a data array or directly in the template.

**Update the CV:** `CV.dc.html` is mostly plain HTML in the template (no data arrays) — edit the markup directly. Also update the "updated <Month Year>" line and the Google Drive PDF link at the top.

## 4. Site-wide things

- **Dark mode**: automatic; stores choice in the browser's `localStorage` under key `ayush-theme`. No maintenance needed.
- **Theme colors**: defined in `theme(dark)` in each of the 5 page files. Light accent `#3568a0`, dark accent `#87aed6`, warm paper background `#faf7f0` / `#181410`.
- **Tab titles & search snippets**: `<title>` and `<meta name="description">` in each file's `<head>`.
- **Social preview** (LinkedIn/WhatsApp cards): `og:` meta tags in each `<head>`, pointing to `https://srivastav-ayush.github.io/assets/img/og-card.png`. If the domain ever changes, update these URLs in all 6 files.
- **Favicon**: an inline SVG data-URI in each file's `<helmet>` (the "AS" mark).
- **Fonts**: Instrument Serif (headings), Archivo (body), JetBrains Mono (labels) — loaded from Google Fonts in each `<helmet>`.
- **Responsive**: pages listen to window width in their logic class (breakpoints 640/760/860px) and swap grid/flex styles — no media queries.

## 5. Deploying this zip to GitHub Pages (first time)

1. **Unzip** the download. Keep the folder structure exactly as-is — don't move `assets/` or the `.dc.html` files around.
2. **Show hidden files before you do anything else.** The zip contains three dotfiles (`.nojekyll`, `.image-slots.state.json`, `.thumbnail`) that your OS hides by default:
   - Mac Finder: `Cmd+Shift+.`
   - Windows Explorer: View → Show → Hidden items
   If you skip this, you'll likely upload everything *except* these files, and the site will break (Jekyll will mangle the build without `.nojekyll`; image drop-zones lose their contents without `.image-slots.state.json`).
3. **Create the GitHub repo.** For the site to load at `https://srivastav-ayush.github.io/` (no sub-path), the repo name must be **exactly** `srivastav-ayush.github.io` — this is a GitHub Pages rule, not optional. Any other repo name serves the site at `https://srivastav-ayush.github.io/that-repo-name/` instead, which will break the absolute `og:image`/`og:url` links in every page's `<head>`.
4. **Push the files.** Command line (safest — never silently drops hidden files):
   ```
   cd path/to/unzipped-folder
   git init
   git add -A
   git commit -m "Initial deploy"
   git branch -M main
   git remote add origin https://github.com/srivastav-ayush/srivastav-ayush.github.io.git
   git push -u origin main
   ```
   If you'd rather not use the terminal, GitHub Desktop works too — but the github.com web upload button is riskier: its file picker often won't show/select dotfiles, so files silently get skipped.
5. **Turn on Pages.** In the repo: Settings → Pages → Source → "Deploy from a branch" → Branch: `main`, folder `/ (root)` → Save.
6. **Wait ~1 minute**, then visit `https://srivastav-ayush.github.io/`.

## 6. Mistakes to avoid

- **Wrong repo name.** Must be `srivastav-ayush.github.io` verbatim, or the site loads under a sub-path and social preview cards break.
- **Dropped dotfiles.** `.nojekyll`, `.image-slots.state.json` must be committed. Check they're present on GitHub after pushing (github.com hides dotfiles from the default folder view too — use the repo's file search or `git ls-files` to confirm, don't just eyeball the file list).
- **Case mismatches.** GitHub Pages serves files case-sensitively (unlike Windows/Mac local filesystems). `Homepage.dc.html` ≠ `homepage.dc.html` — if you ever rename a file, update every link to it with matching case.
- **Editing `support.js` or `image-slot.js`.** These are the rendering engine; hand-editing them breaks every page at once.
- **Trailing/double commas** in a data array inside a `.dc.html` file's `<script>` block — this silently breaks that whole page (blank screen). Check the browser console if a page goes blank after an edit.
- **Uploading via drag-and-drop into GitHub's web UI from a Finder/Explorer window with hidden files still hidden** — same dotfile problem as step 2, worth repeating since it's the single most common way this deploy breaks.
- **Forgetting to update `og:url`/`og:image` domain** in all 6 files if you ever change the custom domain or GitHub username.
- **Editing `Homepage.dc.html` without re-copying it to `index.html`.** They must stay identical — `index.html` is served at `/` directly (no redirect), `Homepage.dc.html` is served at `/Homepage.dc.html`. After any homepage edit, copy the full contents of `Homepage.dc.html` over `index.html`.

## 7. Workflow for future changes (no Claude design tool needed)

1. Open the repo folder on your computer (`git pull` first if edited elsewhere).
2. Attach this README + the relevant `.dc.html` file to a normal AI chat and describe the change ("add this milestone", "change this date").
3. Replace the file with the returned version.
4. **Test locally**: double-click the file — it opens and runs in your browser as-is.
5. Deploy:
   ```
   git add -A
   git commit -m "describe the change"
   git push
   ```
6. GitHub Pages updates `https://srivastav-ayush.github.io/` in ~1 minute.

## 8. Rules for any AI making edits (paste this along with your request)

- Edit only the data arrays or the specific text asked for; do not restructure, reformat, or "improve" other code.
- Keep all styling inline; do not introduce external CSS files or class-based styles.
- Never edit `support.js`, `image-slot.js`, or `.image-slots.state.json`.
- Preserve the `<x-dc>` / `data-dc-script` structure exactly — the site will not render without it.
- Keep dates consistent everywhere: a publication's year must match on the Publications page AND its Homepage milestone.
- No trailing/double commas in data arrays.
- Return the complete file, not a snippet.
