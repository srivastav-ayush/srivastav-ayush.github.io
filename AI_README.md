# AI_README.md — technical reference for AI assistants editing this site

Read this in full before editing anything. This site is intentionally hand-built with no framework and no build step; the goal of every edit is to look and behave like it was written by the same person who wrote everything else. Do not "improve," refactor, or modernize anything beyond what's asked.

---

## 1. What this is, in one paragraph

A static personal website for Ayush Srivastav (mechanical/systems engineer). Six pages, each a single self-contained `.dc.html` file with no external CSS/JS files besides two shared runtime scripts (`support.js`, `image-slot.js`) that you never edit. All styling is inline. All content lives in plain JavaScript object arrays inside each file. GitHub Pages serves the files exactly as committed — no server, no bundler, no npm.

## 2. File inventory

| File | Role |
|---|---|
| `index.html` | Homepage, served at `/`. **Must stay byte-identical to `Homepage.dc.html`** — see §8. |
| `Homepage.dc.html` | Working copy of the homepage (hero, milestones log, experience timeline). |
| `Work.dc.html` | Project portfolio — list + detail view in one file. |
| `Publications.dc.html` | Journal papers, book chapters, conference papers, patents. |
| `Talks.dc.html` | Conference talks timeline. |
| `Articles.dc.html` | Writing/blog index — lists the long-form decision essays below (data array `allArticles`, each linking to its own page). |
| `Wearable Watch Decision.dc.html` | Long-form essay page (Writing) — data-array-driven tables/charts, links back to `Articles.dc.html`. |
| `Himalayan Trek Planner.dc.html` | Long-form essay page (Writing) — same structure. |
| `iPhone Ownership Strategy.dc.html` | Long-form essay page (Writing) — same structure. |
| `GMAT vs GRE Decision.dc.html` | Long-form essay page (Writing) — same structure. |
| `CV.dc.html` | Full CV — mostly hand-written HTML in the template, not data-array-driven. |
| `support.js` | The template-rendering runtime (turns `{{ }}` holes, `<sc-for>`, `<sc-if>` into a live page). **Never edit.** |
| `image-slot.js` | Drag-and-drop image placeholder web component (`<image-slot>` / `<x-import component-from-global-scope="image-slot">`). **Never edit.** |
| `.image-slots.state.json` | Persisted state for images dropped into `image-slot` components (hero photo, publication/patent figures). **Never delete** — figures silently disappear without it. |
| `.nojekyll` | Empty marker file. Tells GitHub Pages not to run Jekyll (which would otherwise ignore/mangle files starting with `_` or dotfiles). **Never delete.** |
| `.thumbnail` | Bundler/preview thumbnail artifact. Harmless, leave alone. |
| `README.md` | Short human-facing readme. |
| `AI_README.md` | This file. |
| `assets/img/` | `ayush.jpg` (hero photo fallback), `favicon.png`, `og-card.png` (social share image). |
| `assets/img/portfolio/` | Work page project images. |
| `assets/logos/` | Company/institution/award logos used on the CV and homepage (transparent-safe, square-ish, referenced at 36–67px). |
| `assets/talks/` | Talk event photos, referenced via `customImage` in `Talks.dc.html`. |

## 3. Anatomy of a `.dc.html` page

Every page follows the same skeleton:

```html
<!DOCTYPE html>
<html><head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>…</title>
  <meta name="description" content="…">
  <meta property="og:type" content="website">
  <meta property="og:title" content="…">
  <meta property="og:description" content="…">
  <meta property="og:image" content="https://srivastav-ayush.github.io/assets/img/og-card.png">
  <meta property="og:url" content="https://srivastav-ayush.github.io/…">
  <meta name="twitter:card" content="summary_large_image">
  <script src="./support.js"></script>
</head><body>
<x-dc>
<helmet>
  <!-- Google Fonts <link>, favicon <link>, dark-mode-flash-prevention inline <script>, and a <style> block with
       ONLY: body/html reset, a/a:hover default colors, @keyframes, ::selection, scrollbar hiding. Nothing else. -->
</helmet>

<div style="{{ rootStyle }}">
  <!-- page content -->
</div>

</x-dc>
<script type="text/x-dc" data-dc-script>
class Component extends DCLogic {
  // data arrays, theme(), renderVals()
}
</script>
</body></html>
```

**Template rules actually in force here:**
- `{{ name }}` = a value hole, resolved by `renderVals()` in the logic class below.
- `<sc-for list="{{ items }}" as="item">…</sc-for>` = repeat block; `$index` is available inside.
- `<sc-if value="{{ cond }}">…</sc-if>` = conditional render.
- **Every style is inline** (`style="…"`). There is no `<link rel="stylesheet">` and no CSS class-based styling anywhere in the body. Color values reference CSS custom properties (`var(--accent)`, `var(--text-muted)`, etc.) that are set once, inline, on the page's root `<div style="{{ rootStyle }}">`.
- Hover states use the non-standard `style-hover="…"` attribute (handled by `support.js`) — e.g. `style="color:var(--text);" style-hover="color:var(--accent) !important;"`. Always include `!important` in `style-hover` values, matching existing usage, or the hover won't override the base inline style.
- The `<helmet>` block is the only place `<link>`/`<style>`/`<script>` tags belong. A `<script>` placed later in the template body will not run until the stream reaches it and is not how this site does anything — don't add one.

## 4. The logic class (`<script type="text/x-dc" data-dc-script>`)

A plain ES class named `Component extends DCLogic`, no imports, no TypeScript. Three things matter:

1. **Data arrays near the top of the class** — this is where ~95% of content edits happen:
   - `Homepage.dc.html` / `index.html`: `rawTimeline` (career/education timeline entries, rich per-entry structure with `hook`, `introPre`/`introSegments`, `process` steps, `pills`, `statBoxLabel`), and `allMilestones` (the scrolling milestone log — flat, one line each).
   - `Work.dc.html`: `allProjects`.
   - `Publications.dc.html`: `allJournalPapers`, `allBookChapters`, `allConferencePapers`, `allPatents`.
   - `Talks.dc.html`: `allTalks`.
   - `CV.dc.html` has **no data arrays** — it's plain markup in the template; edit the HTML directly for CV changes.
2. **`theme(dark, accentKey?)`** — returns the full color palette object (see §5). Nearly-identical copy exists in `Homepage.dc.html`/`index.html`, `Work.dc.html`, `Publications.dc.html`, `Talks.dc.html`, `Articles.dc.html`, `CV.dc.html`. **A global color change must be applied to all of them individually** — there is no shared token file by design (inline-only styling rule).
3. **`renderVals()`** — computes everything the template's `{{ holes }}` reference: filtering, sorting, derived styles, computed counts. If a template hole shows nothing, the fix is almost always here, not in the template.

## 5. Design system (colors, type, spacing)

**Palette** — warm, slightly desaturated paper tones, not pure black/white:
- Light mode: background `#faf7f0` (bgAlt `#f1ebde`, card `#fff9f0`, chip `#efe8d8`), text `#241f19`, body text `#57503f`, muted/faint text `#8d8271`, border `#e4dbca` (strong `#d8cfc0`).
- Dark mode: background `#181410` (bgAlt `#1d1712`, card `#231e18`, chip `#2b251d`), text `#f3ede0`, body text `#c4b9a6`, muted/faint text `#8f8571`, border `#3a3327` (strong `#4a4234`).
- Primary accent: light `#3568a0` / hover `#244f78`; dark `#87aed6` / hover `#a6c5e6`. This is the default `accentColor`; Homepage supports alternate accent keys via `accentThemes` (e.g. green `#4a7a43`, orange `#c1703f`) used for milestone category coloring, not for switching the whole site's identity color.
- Milestone/category colors (solid, used for filter chips and dots): `career #3568a0`, `education #5b4a9e`, `talk #c1703f`, `publication #4a7a43`, `patent #a34a82`, `award #b8892e` (dark-mode variants are lighter tints defined alongside).
- Buttons: `btnBg`/`btnText` invert relative to the page background (dark button on light bg and vice versa) — it's the highest-contrast neutral, not the accent color.
- Footer is always the "opposite" tone block (`footerBg`/`footerText`) regardless of light/dark mode nuance — check existing values before changing.
- All colors are consumed as CSS custom properties set once per render (`--bg`, `--text`, `--accent`, etc.) on the root wrapper `style` attribute, then referenced via `var(--x)` everywhere inline. When adding a new color use, add the CSS var to the root-style template string in `renderVals()` too, in every file that needs it.

**Type**: three Google Fonts loaded per-page in `<helmet>`:
- **Instrument Serif** (italic for accent words/logo, regular for large headings) — display/serif moments only.
- **Archivo** — body text, default sans.
- **JetBrains Mono** — small caps labels, dates, tags, monospace UI chrome (uppercase, letter-spacing ~0.05–0.08em, 11–12.5px).

**Spacing/shape**: max content width `1200px`, generous section padding (`clamp(16px,5vw,64px)` horizontal is the standard pattern), border-radius 8–18px depending on element size, 1px hairline borders using `var(--border)`, no drop shadows except the hero photo card (`0 24px 48px rgba(0,0,0,0.2)`).

**Dark mode**: toggled by a floating button (bottom-right, `toggleDark`), persisted to `localStorage["ayush-theme"]`. A tiny inline `<script>` in `<helmet>` reads that key before paint and sets `html.dark-init` + inline background so there's no white flash. This exists in every page — don't remove it when editing a `<helmet>` block.

## 6. Content/data schemas

Copy an existing object and edit fields — don't invent new field names casually, since `renderVals()` reads specific keys.

**`allMilestones`** (Homepage): `{ year, month, dateLabel, category, text, upcoming? }`. `category` ∈ `career | education | talk | publication | patent | award`. `upcoming: true` for future-dated entries (renders distinctly). Sorted automatically — insertion order doesn't matter. Hero stat counts (Patents/Publications/Talks) are **derived from this array** — don't hand-edit counts elsewhere.

**`rawTimeline`** (Homepage): one object per job/degree — `role, org, date, website, logo, virtual?, statBoxLabel, statBoxLines, statBoxUrl?, pills?, hook, introPre/introSegments, introLinkText/introLinkUrl/introPost?, process: [{step, text} | {step, segments:[{text, kind}]}]`. `pills` values are **manual** (not derived) — update by hand.

**`allProjects`** (Work): includes `category` ∈ `work | research | academic | passion`, an `images` array (first = cover, up to 6), stored in `assets/img/portfolio/`.

**`allJournalPapers` / `allBookChapters` / `allConferencePapers`** (Publications): `{ slotId, title, venue, authors, year, journalLink, pdfLink, selected? }`. `slotId` must be unique — it's the drop-target id for the `image-slot` figure and must never collide across arrays.

**`allPatents`** (Publications): similar shape plus `drawingLabel`.

**`allTalks`** (Talks): `{ slotId, date (ISO), customImage, event, orgUrl, pairKey? }`. Two talks sharing `pairKey` merge into one card (same paper presented at two venues) — see the grouping logic right after the array in `Talks.dc.html`.

**`CV.dc.html`**: no arrays; edit the HTML directly. Sections: header (name/title/résumé download button), Education (cards), Experience (timeline with logo/org/role/dates/bullets), Research Experience (timeline with guide links, repo/thesis/journal links), Awards & Achievements (grid: logo · heading+body · year), footer.

## 7. Link conventions (verified — keep these exact)

Organization/school links use these canonical URLs everywhere they appear (CV, Homepage `rawTimeline.website`):
- Volvo Group `https://www.volvogroup.com/en/` · Ola Electric `https://www.olaelectric.com/` · KU Leuven `https://www.kuleuven.be/english/kuleuven/` · KTH `https://www.kth.se/en` · MANIT `https://www.manit.ac.in/` · AIIMS Bhopal `https://www.aiimsbhopal.edu.in/` · Sagar Public School `https://www.spssn.ac.in/` · Narmada Valley International School `https://www.nvis.co.in/`.

CV Awards section — each award **heading** is a link (with `color:inherit` + `style-hover="color:var(--accent) !important;"`, matching the org-link pattern) to the awarding body/event, separate from any "Certificate ↗" link in the body text:
- Narotam Sekhsaria Foundation → `https://pg.nsfoundation.co.in/`
- Dr. APJ Abdul Kalam Young Research Fellowship → `https://www.drkalamfellowship.com/` (do not re-link the inline "TERRE Policy Center" mention — it's plain text now that the heading carries the link)
- International Youth Exchange Programme (IYEP) → `https://mybharat.gov.in/mega_events/international-youth-exchange-programmes-iyep`; the "VII BRICS Youth Summit" mention inside its body links to `https://bricsyouthalliance.org/`
- HPAIR Delegate → `https://www.hpair.org/`
- AFTMME → `https://sites.google.com/iitrpr.ac.in/aftmme2021`
- eBAJA → `https://www.bajasaeindia.org/`
- JEE Main → `https://jeemain.nta.nic.in/`
- Academic Excellence Award has **no** outbound link and **no certificate** (there isn't one) — don't add either back.

Certificate ↗ links (Google Drive) per award, already corrected once after being shuffled — don't re-shuffle without explicit instruction:
IYEP → `.../1yKDCzDZ8XviAG-WlWq2wXxFHrm-C4r3B` · HPAIR → `.../1sH45blIprykhYSmRb1L_tMd8lDt52xO5` · AFTMME → `.../12HQql777tbg0-dUr-MJgEdX1WdVZqjOa` · eBAJA → `.../1xoZVinwn-v-kkRqAroA2ytIgI3KYy_zz` · APJ Kalam Fellowship → `.../1DuSLooKcoYUGRr8FZQUHWcwUEkDiYypm`.

Repository, thesis (Major Project Thesis = KTH/SocketSense; Minor Project Thesis = FGM cylinder), and journal DOI links throughout CV/Publications/Work/Talks have been individually verified against the live source (DOI resolves to the stated paper, PDF opens, GitHub repo exists) — treat existing values there as correct unless the user says otherwise.

## 8. Site-wide rules (non-negotiable — see also `CLAUDE.md`)

- **`index.html` and `Homepage.dc.html` must stay byte-identical.** `index.html` is what GitHub Pages serves at `/`; `Homepage.dc.html` is the editable working copy. **Every edit to one must be applied to the other in the same change.** If unsure, diff them (e.g. read both and compare) before finishing.
- Thesis naming is fixed: **"Major Project Thesis"** = the KTH/SocketSense bachelor's thesis, **"Minor Project Thesis"** = the FGM cylinder project — used verbatim everywhere except the CV's SocketSense title line, which uses "(Bachelor's Thesis)" in the bracket instead.
- Inline styles only — never introduce a `<link rel="stylesheet">`, a CSS class-based rule, or a shared tokens/CSS file. Repeat literal style strings per element/per page as the existing code does.
- Never edit `support.js`, `image-slot.js`, or hand-edit `.image-slots.state.json`.
- Preserve the exact `<x-dc>…</x-dc>` + `<script type="text/x-dc" data-dc-script>` structure — the runtime depends on it verbatim.
- No trailing/double commas in data arrays (`},,`) — this silently blanks the whole page; if a page goes blank after an edit, check the browser console first.
- `slotId` values must stay globally unique across a page's arrays (they're `image-slot` drop-target ids).
- Keep dates/facts consistent across pages: a publication's year must match on the Publications page **and** its Homepage milestone entry; a role's dates on the Homepage timeline should match the CV.
- `og:url`/`og:image` absolute URLs assume the domain `https://srivastav-ayush.github.io/` — if the GitHub username or custom domain ever changes, update these in all 6 pages' `<head>`.
- Always return the complete file when making an edit — never a diff/snippet — unless using a targeted string-replace tool.

## 9. Making common edits (recipes)

**Add a milestone** (Homepage — remember to mirror into `index.html` too): append to `allMilestones`:
```js
{ year: 2026, month: 9, dateLabel: "Sep 2026", category: "talk", text: "Presented …" },
```

**Add a publication/patent**: copy an object in the matching array in `Publications.dc.html`, edit fields, give it a fresh unique `slotId`. Optionally add a matching `allMilestones` entry on the Homepage with the same date/year.

**Add a talk**: copy an object in `allTalks` in `Talks.dc.html`; drop the event photo into `assets/talks/`, reference via `customImage`. Use `pairKey` to merge with a sibling talk on the same paper.

**Add a project**: copy an object in `allProjects` in `Work.dc.html`; put 1–6 images in `assets/img/portfolio/` (first = cover); set `category`.

**Update the CV**: edit `CV.dc.html`'s template markup directly (no data array). Also bump the "last updated <Month Year>" line near the top and, if the résumé changed, the Google Drive PDF link on the download button.

**Change a color globally**: edit the value in `theme()` in **every** page file that defines it (there is no shared token file — that's intentional, per the inline-styling-only rule).

## 10. First-time GitHub Pages deploy checklist

1. Unzip, keeping the folder structure exactly as-is.
2. **Show hidden files before doing anything else** (`.nojekyll`, `.image-slots.state.json`, `.thumbnail`) — OS file managers hide dotfiles by default (macOS: `Cmd+Shift+.`; Windows: View → Show → Hidden items). Skipping this silently drops them on upload and breaks the site.
3. Repo name must be **exactly** `srivastav-ayush.github.io` for the site to serve at the root domain (a GitHub Pages rule) — any other name serves it under a sub-path and breaks the absolute `og:image`/`og:url` links.
4. Push via command line (safest for dotfiles):
   ```
   cd path/to/unzipped-folder
   git init && git add -A && git commit -m "Initial deploy"
   git branch -M main
   git remote add origin https://github.com/srivastav-ayush/srivastav-ayush.github.io.git
   git push -u origin main
   ```
5. Settings → Pages → Source → Deploy from a branch → `main` / `/(root)` → Save. Wait ~1 minute, then visit the live URL.
6. Verify dotfiles actually landed (`git ls-files` or the repo's file search — github.com's default folder view also hides dotfiles from casual browsing).

## 11. Common failure modes

- Wrong repo name → site loads under a sub-path, social cards break.
- Dropped dotfiles on upload → Jekyll mangles the build without `.nojekyll`; image slots lose contents without `.image-slots.state.json`.
- Case mismatches in filenames/links — GitHub Pages is case-sensitive unlike most local filesystems.
- Trailing/double commas in a data array → blank page; check console.
- Editing `Homepage.dc.html` and forgetting `index.html` (or vice versa) → the two drift apart, violating the byte-identical rule.
- Re-introducing a stylesheet or CSS class where inline styles are the rule.
