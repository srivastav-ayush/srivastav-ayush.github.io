# AI_README.md — technical reference for AI assistants editing this site

Read this in full before editing anything. This site is intentionally hand-built with no framework and no build step; the goal of every edit is to look and behave like it was written by the same person who wrote everything else. Do not "improve," refactor, or modernize anything beyond what's asked.

---

## 1. What this is, in one paragraph

A static personal website for Ayush Srivastav (mechanical/systems engineer). Seven section pages (Home, Work, Publications, Talks, Reading, Writing, CV) plus eight Writing essay pages, each a single self-contained `.dc.html` file with no external CSS/JS files besides two shared runtime scripts (`support.js`, `image-slot.js`) that you never edit. All styling is inline. All content lives in plain JavaScript object arrays inside each file. GitHub Pages serves the files exactly as committed — no server, no bundler, no npm.

## 2. File inventory

Pages are grouped into per-section folders; only the homepage and shared runtime/assets live at the root. Every internal link/asset path already accounts for this (root files use plain relative paths, files one folder deep use `../` to reach shared assets and to cross into a sibling section folder).

| File | Role |
|---|---|
| `index.html` | Homepage, served at `/`. **Must stay byte-identical to `Homepage.dc.html`** — see §8. |
| `work/index.html`, `publications/index.html`, `talks/index.html`, `reading/index.html`, `writing/index.html`, `cv/index.html` | Clean-URL entry points (`/work/`, `/cv/`, …). Each **must stay byte-identical to the `.dc.html` working copy in the same folder** — see §8. |
| `robots.txt` / `sitemap.xml` / `feed.xml` | Crawler files at the root. Add a `<url>` entry to `sitemap.xml` **and an `<item>` to `feed.xml`** for every new essay. `feed.xml` is the Writing RSS feed (newest first, hand-maintained, linked from every page's `<head>` via `<link rel="alternate">`). |
| `404.html` | Plain static not-found page (no DC runtime — no `support.js`, no `<x-dc>`, inline styles + one small inline script that recolours it when `localStorage["ayush-theme"]` is `dark`). GitHub Pages serves it for any unmatched URL. Links use root-absolute paths (`/work/index.html`) because it can be served from any depth. |
| `.gitignore` | Excludes `uploads/` (raw source material, ~39 MB), `site-deploy/` and `screenshots/` (audit/debug captures) from the deployed repo. |
| `Homepage.dc.html` | Working copy of the homepage (hero, milestones log, experience timeline). |
| `work/Work.dc.html` | Project portfolio — list + detail view in one file. |
| `publications/Publications.dc.html` | Patents, journal papers, conference papers, book chapters — **sections render in that order** (changed Aug 2026 on request; the data arrays are still declared journal/book/conference/patents, only the template sections were reordered, and the last section carries the `96px` bottom padding). |
| `talks/Talks.dc.html` | Conference talks timeline. |
| `writing/Articles.dc.html` | Writing index — **one flat list of essay cards** (Sep 2026 redesign; the old two-level category hub, its `categoryMeta` array and the URL-hash category state are all gone). Toolbar: a Category dropdown, a "Selected only" star toggle and a Sort menu (newest/oldest), all held in component state — **no URL hash is read or written**, so never link to `writing/index.html#travel`-style anchors. The single data array is `allArticles` (8 live essays: `{ key, href, tag, title, hook, summary, readTime, date, sortDate, selected? }`), each linking to its own page in the same `writing/` folder. Tag colours come from the theme-branched `tagColors` map in `theme()` (Travel / Tech / Education / Tools), not from hardcoded hexes. **There is no Books category here** — book reviews live on `reading/`. |
| `writing/Wearable-Watch-Decision.dc.html` | Long-form essay page (Writing) — data-array-driven tables/charts, links back to `../writing/`. |
| `writing/Himalayan-Trek-Planner.dc.html` | Long-form essay page (Writing) — same structure. |
| `writing/iPhone-Ownership-Strategy.dc.html` | Long-form essay page (Writing) — same structure. |
| `writing/GMAT-vs-GRE-Decision.dc.html` | Long-form essay page (Writing) — same structure. |
| `writing/ISB-vs-IIM-Cost-Comparison.dc.html` | Long-form essay page (Writing, Education) — same structure; 6 tables, cost sliders. |
| `writing/Tap-to-Track.dc.html` | Long-form essay page (Writing, **Tools**) — build note for the `srivastav-ayush/tap-to-track` repo. Prose + tables, no data arrays; closes with a Repository card and a "Where the AI actually helped" section (the Tools-category convention). |
| `writing/Timeplan-Dashboard.dc.html` | Long-form essay page (Writing, **Tools**) — build note for the `srivastav-ayush/timeplan-dashboard` repo. Same conventions; carries the one screenshot in the section (`assets/img/writing/timeplan-gantt.jpg`). |
| `writing/Kudremukh-Trek.dc.html` | Long-form essay page (Writing) — personal trip narrative (not data-array-driven; prose + `image-slot` photo placeholders written directly in the template), unlike the decision-framework essays above. |
| `cv/CV.dc.html` | Full CV — mostly hand-written HTML in the template, not data-array-driven. |
| `reading/Reading.dc.html` / `reading/index.html` | Reading page — flat `allBooks` array (books, not essays), expandable rows, genre filter chips + sort/status menu. **Must stay byte-identical to `reading/index.html`** — see §8. Book covers are real files at `assets/img/books/<key>.jpg`, not `image-slot`s. Category taxonomy is documented at the end of §6 — categorize every new book against it. |
| `support.js` | The template-rendering runtime (turns `{{ }}` holes, `<sc-for>`, `<sc-if>` into a live page). **Never edit.** |
| `image-slot.js` | Drag-and-drop image placeholder web component (`<image-slot>` / `<x-import component-from-global-scope="image-slot">`). **Never edit.** |
| `.image-slots.state.json` | **Deleted Aug 2026 (all five copies were 0-byte) and no longer needed.** Publication/patent figures are real files in `assets/img/publications/<slotId>.webp`, wired via `src="{{ pub.figure }}"` on each slot; the hero photo and article photos use plain `src` paths. If you drop a new image into a slot in the editor, the runtime will create this sidecar again in that page's folder and it will take precedence over `src` — that's fine, but prefer saving the file into `assets/` and pointing `src` at it. |
| `.nojekyll` | Empty marker file. Tells GitHub Pages not to run Jekyll (which would otherwise ignore/mangle files starting with `_` or dotfiles). **Never delete.** |
| `.thumbnail` | Bundler/preview thumbnail artifact. Harmless, leave alone. |
| `README.md` | Short human-facing readme. |
| `AI_README.md` | This file. |
| `assets/img/` | `ayush.jpg` (hero photo), `favicon-light.png` / `favicon-dark.png`, `apple-touch-icon.png`, `og-card.png` (social share image). |
| `assets/img/publications/` | The 13 publication/patent figures, one `.webp` per `slotId`. |
| `assets/img/portfolio/` | Work page project images. |
| `assets/img/writing/` | Writing-section photography — the Kudremukh trek photos (≤1100px, 35–253 KB, re-encoded Aug 2026) and `timeplan-gantt.jpg`. `kudremukh-base-point.jpg` opens the essay (the National Park base point sign, the trek's start and end point); `kudremukh-summit-fog.jpg` is now the essay's last photo. `kudremukh-outpost.jpg` was **deleted Aug 2026** — the closing outpost photo had been cut from the essay and the file was 165 KB of dead weight. **These JPEGs are already near-optimal; do not "convert them to WebP"** — measured Aug 2026, re-encoding at visually-equivalent quality (q≈0.78) saves only ~14% and costs a generation of loss, and several files come out *larger* at q≥0.86. |
| `assets/logos/` | Company/institution/award logos used on the CV and homepage (transparent-safe, square-ish, referenced at 36–67px). |
| `assets/talks/` | Talk event photos, referenced via `customImage` in `Talks.dc.html`. |
| `site-deploy/` | A full **deployment mirror** of every root file (same filenames, same content) plus `.nojekyll`/`.image-slots.state.json`. Exists so the downloadable zip already contains a ready-to-push copy. **Must be kept byte-identical to root on every content change** — see §8. |

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
  <link rel="canonical" content="https://srivastav-ayush.github.io/…">
  <!-- Google Analytics gtag snippet -->
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
- **Every style is inline** (`style="…"`), with two documented exceptions that live in `<helmet><style>` because they cannot be expressed inline: (a) the shared reset/keyframes/reduced-motion/print blocks, and (b) the `.art-table` / `.art-table-compact` table rules on the five Writing essay pages (they need `thead th { position:sticky }` and descendant selectors). Do not add new class-based styling beyond these. There is no `<link rel="stylesheet">`. Color values reference CSS custom properties (`var(--accent)`, `var(--text-muted)`, etc.) that are set once, inline, on the page's root `<div style="{{ rootStyle }}">`.
- Hover states use the non-standard `style-hover="…"` attribute (handled by `support.js`) — e.g. `style="color:var(--text);" style-hover="color:var(--accent) !important;"`. Always include `!important` in `style-hover` values, matching existing usage, or the hover won't override the base inline style.
- The `<helmet>` block is the only place `<link>`/`<style>`/`<script>` tags belong. A `<script>` placed later in the template body will not run until the stream reaches it and is not how this site does anything — don't add one.

## 4. The logic class (`<script type="text/x-dc" data-dc-script>`)

A plain ES class named `Component extends DCLogic`, no imports, no TypeScript. Three things matter:

1. **Data arrays near the top of the class** — this is where ~95% of content edits happen:
   - `Homepage.dc.html` / `index.html` (job title is **"System Engineer, ECU Cooling"** verbatim everywhere — hero eyebrow, timeline card, milestone text and CV): `rawTimeline` (career/education timeline entries, rich per-entry structure with `hook`, `introPre`/`introSegments`, `process` steps, `pills`, `statBoxLabel`), and `allMilestones` (the scrolling milestone log — flat, one line each).
   - `Work.dc.html`: `allProjects`.
   - `Publications.dc.html`: `allJournalPapers`, `allBookChapters`, `allConferencePapers`, `allPatents`.
   - `Talks.dc.html`: `allTalks`.
   - `CV.dc.html` has **no data arrays** — it's plain markup in the template; edit the HTML directly for CV changes.
2. **`theme(dark, accentKey?)`** — returns the full color palette object (see §5). Nearly-identical copy exists in `Homepage.dc.html`/`index.html`, `Work.dc.html`, `Publications.dc.html`, `Talks.dc.html`, `Articles.dc.html`, `CV.dc.html`. **A global color change must be applied to all of them individually** — there is no shared token file by design (inline-only styling rule).
3. **`renderVals()`** — computes everything the template's `{{ holes }}` reference: filtering, sorting, derived styles, computed counts. If a template hole shows nothing, the fix is almost always here, not in the template.

## 5. Design system (colors, type, spacing)

**Palette** — light mode is warm desaturated paper; dark mode is a cool slate (retoned Sep 2026 — the old warm `#181410` set survives only in this file's history, so don't reintroduce it):
- Light mode: background `#faf7f0` (bgAlt `#f1ebde`, card `#fff9f0`, chip `#efe8d8`), text `#241f19`, body text `#57503f`, muted/faint text `#6f6656`, border `#e4dbca` (strong `#d8cfc0`).
- Dark mode: background `#13151c` (bgAlt `#181b23`, card `#1d212b`, chip `#242835`), text `#eef0f5`, body text `#b6bac6`, muted/faint text `#848a99`, border `#2a2f3b` (strong `#363c4a`), footer `#0a0c11`, nav `rgba(19,21,28,.88)`. **All 16 pages plus `404.html` carry these same values** — a page left on the warm set changes the site's colour temperature mid-navigation (that was `404.html`'s bug until Sep 2026).
- **Contrast floor: every text/background pair must clear WCAG AA (4.5:1).** The muted values above were darkened/lightened in Jul 2026 for exactly this reason (the old `#8d8271` was 3.53:1 on paper). `footerTextMuted` is `#a49a85` in light mode because the light footer sits on the dark `#241f19` block. **Before changing any text colour, compute the ratio against `bg`, `bgAlt`, `bgChip`, `bgCard` and `footerBg`.**
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

**Animation**: minimal and functional only, no decorative motion. The one recurring pattern is a fade/rise-in on list entries — `@keyframes fadeInUp { from { opacity:0; transform:translateY(18px); } to { opacity:1; transform:translateY(0); } }` (defined once in `<helmet><style>`), applied inline per-item as `animation:fadeInUp 0.4s ease both; animation-delay:{{ item.delay }};` with a small computed stagger (e.g. Homepage milestones, `renderVals()` assigns each visible item an increasing `delay`). Reuse this exact pattern for any new staggered-list animation rather than inventing a new easing/duration.

**Every page's `<helmet><style>` ends with two required blocks** — copy them verbatim into any new page:
1. `@media (prefers-reduced-motion: reduce)` — neutralises all animation/transition durations and forces `.reveal-card` visible.
2. `@media print` — kills animations, un-fixes the nav, hides the dark-mode toggle and anything marked `[data-print-hide]`, and overrides the CSS custom properties on `div[style*="--bg:"]` to a light ink-on-white palette so both light and dark mode print identically. Do not add `@page` rules or print CSS anywhere else.

**Article image sizing (rule — apply to every essay photo, no exceptions).** Every inline photo in a Writing essay renders in the SAME box: full content width, `aspect-ratio:16/10`, `border-radius:12px`, `overflow:hidden`, `1px solid var(--border)`, `margin-bottom:8px`, with the `image-slot` at `width:100%; height:100%` (default `fit`, i.e. cover) and a 13px `var(--text-faint)` centred caption below at `margin:0 0 34px`. Do not introduce a second photo ratio (4/3 and 7/5 existed once and were unified Aug 2026) — a portrait source is simply centre-cropped by the box, which is why `kudremukh-summit-fog.jpg` (960×1280) still reads correctly. Export new essay photos at **1100×688** so the file matches the box. **Maps, charts and screenshots are the one exception**: they keep their own natural ratio (set `aspect-ratio` to the file's own W/H) because cropping them destroys content — never letterbox one with `fit="contain"` + a white background inside a fixed-height box (that is what made the Kudremukh trail map sit in white bars inside a double-bordered card until Aug 2026; it is now a plain `width:min(100%,560px)` centred block at its native 1000/952 ratio, no card, no inner border, so it reads as an inset figure rather than a hero).

**Images**: every `<img>` carries `loading="lazy" decoding="async"` and a meaningful `alt` (logos read "<Org> logo"). Source files are capped at the size they're actually displayed at — portfolio covers ≤1400px, talk/article photos ≤1000–1300px, logos ≤240px, hero ≤760px. **Do not commit a 1800px, 400 KB image for a 280px thumbnail.**

**Tap targets**: nav links, footer links and filter chips are padded to ≥40–44px tall. Keep new controls at that floor.

**Minimum type size is 11px — nothing on the site may be smaller.** That covers mono chrome (labels, dates, tags, badges, pills, chips, legends, figure captions) as well as body copy; article table cells sit at 12px. When auditing this, search for the *pattern* `font-size:N px` and compare numerically — a literal search for `font-size:10px` misses `10.5px`, `9.5px` and `8.5px`, which is exactly how sub-11px text survived one cleanup pass. SVG labels size via a `fontSize="11"` **attribute**, not CSS — sweep that pattern too.

**Two runtime gotchas that silently swallow markup** (both found in the Jul 2026 audit, both fixed):

1. **`<colgroup>` is dropped by the DC runtime.** Authored `<col style="width:30%">` elements never reach the DOM, so with `table-layout:fixed` every column rendered equal-width and long cell text broke mid-word. Put column widths on the **`<th>`** in the first `<thead>` row instead (`<th style="border-bottom:none; width:30%;">`) — under `table-layout:fixed` those are authoritative and survive. Never re-add a `<colgroup>`.
2. **A `{{ hole }}` inside an SVG `<text>` renders nothing.** The runtime wraps interpolated content in an HTML `<span>`, which SVG cannot paint — the iPhone and Wearable chart x-axis tick labels were invisible from the day they were written. Build SVG text nodes in `renderVals()` with `React.createElement("text", {…}, label)` and drop the array in through one hole (see `deprChart.xAxis` / `utilityChart.xAxis`). Static text inside SVG `<text>` is fine; interpolated text is not.

**Known-benign console warnings — do not "fix" these.** Every essay page with a data table logs one `[dc-runtime] {{ row.x }} never resolved — rendered as empty` warning per distinct hole inside a `<sc-for>` that sits in a `<tbody>` (15 on iPhone, similar on the others). Cause: the HTML parser does not allow a custom element inside table context, so `<sc-for>` is foster-parented out of the table at parse time and the runtime evaluates the row's holes once with no loop variable in scope. **The tables themselves render 100% correctly** — verified 0 empty cells — and the warning fires once per page load, never on re-render. Proven with an isolated probe: the same loop inside a `<div>` produces no warning; inside `<tbody>` it always does. `hint-placeholder-count="0"` and `<sc-if>` gating do **not** suppress it. The only real fixes are (a) replace `<table>` with `role="table"` divs, which costs the working sticky `thead` and real table semantics, or (b) build rows via `React.createElement`, which makes every cell uneditable in the editor. Both are worse than the warning — leave it alone.

## 6. Content/data schemas

Copy an existing object and edit fields — don't invent new field names casually, since `renderVals()` reads specific keys.

**`allMilestones`** (Homepage): `{ year, month, dateLabel, category, text, upcoming? }`. `category` ∈ `career | education | talk | publication | patent | award`. `upcoming: true` for future-dated entries (renders distinctly). Sorted automatically — insertion order doesn't matter. Hero stat counts (Patents/Publications/Talks) are **derived from this array** — don't hand-edit counts elsewhere.

**`rawTimeline`** (Homepage): one object per job/degree — `role, org, date, website, logo, virtual?, statBoxLabel, statBoxLines, statBoxUrl?, pills?, hook, introPre/introSegments, introLinkText/introLinkUrl/introPost?, process: [{step, text} | {step, segments:[{text, kind}]}]`. `pills` values are **manual** (not derived) — update by hand.

**`allProjects`** (Work): includes `category` ∈ `work | research | academic | passion`, an `images` array (first = cover, up to 6), stored in `assets/img/portfolio/`.

**`allJournalPapers` / `allBookChapters` / `allConferencePapers`** (Publications): `{ slotId, title, venue, authors, year, journalLink, pdfLink, selected? }`. `slotId` must be unique — it's the drop-target id for the `image-slot` figure and must never collide across arrays.

**`allPatents`** (Publications): similar shape plus `drawingLabel`.

**`allTalks`** (Talks): `{ slotId, date (ISO), customImage, event, orgUrl, pairKey? }`. Two talks sharing `pairKey` merge into one card (same paper presented at two venues) — see the grouping logic right after the array in `Talks.dc.html`.

**`CV.dc.html`**: no arrays; edit the HTML directly. Sections: header (name/title/résumé download button), Education (cards), Experience (timeline with logo/org/role/dates/bullets), Research Experience (timeline with guide links, repo/thesis/journal links), Awards & Achievements (grid: logo · heading+body · year), footer.

**`allBooks`** (Reading): `{ key, title, author, genre, subGenre, rating (0-5), status? ("reading"|"incomplete", omit for finished), blurb, date, review }`. `key` is also the cover filename (`assets/img/books/<key>.jpg`) — add the cover file when adding a book. `genre` is one of the 8 top-level filter categories below; `subGenre` is the specific leaf under it. **Reading page book taxonomy** (set Sep 2026, by request — categorize every new book against this before appending it to `allBooks`; top-level names are the exact filter-chip labels, CSS uppercases them):
```
Fiction: Literary fiction / Historical fiction / Mystery and crime / Thriller / Fantasy / Science fiction
Biography & Memoir: Biography / Autobiography / Memoir
History & Society: History / Politics and geopolitics / Sociology / Culture and anthropology
Science & Technology: Science / Engineering / AI and computers / Medicine / Mathematics
Business & Economics: Business strategy / Management and leadership / Entrepreneurship / Economics / Marketing
Money & Investing: Personal finance / Investing / Stock markets / Wealth building
Personal Development: Habits and productivity / Psychology / Communication and relationships / Career / Health and fitness
Travel, Arts & Culture: Travel / Art and design / Music and film / Architecture / Food and cultural writing
```
`bookGenres` (the chip list, `["All", ...]`) must stay exactly these 8 names + "All" — don't add a 9th top-level category without the user asking; add new leaves under an existing top-level instead.

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

- **`site-deploy/` must be kept byte-identical to the root copy of every file it mirrors.** After editing any root file, copy the same change into `site-deploy/<same filename>` in the same turn — diff (`readFile` both, compare) if unsure what drifted. This is what ships in the downloadable zip.
- **Clean-URL mirrors must stay byte-identical to their `.dc.html` working copy in the same folder**: `work/index.html` ↔ `work/Work.dc.html`, `publications/index.html` ↔ `publications/Publications.dc.html`, `talks/index.html` ↔ `talks/Talks.dc.html`, `writing/index.html` ↔ `writing/Articles.dc.html`, `cv/index.html` ↔ `cv/CV.dc.html`. Edit the `.dc.html`, then copy it over `index.html` in the same turn.
- **All internal links use explicit `index.html` paths** — `href="work/index.html"` from the root, `href="../cv/index.html"` from a subfolder, `href="../index.html"` for the homepage. **Do not "clean" these to `href="work/"`.** GitHub Pages resolves a folder URL to its `index.html`, but a plain static file server (including the Claude design preview) does **not** — it returns 404 and every nav link breaks. The explicit path works on both. The section `.dc.html` files are working copies, never link targets. Essay pages keep their `.dc.html` filenames and are hyphenated (no spaces) — `writing/Kudremukh-Trek.dc.html`.
- **`sitemap.xml` and the `canonical`/`og:url` tags DO use the clean form** (`https://…/work/`) — those are only read by crawlers hitting the live GitHub Pages host, which resolves them correctly. Keep that split: clean URLs in metadata, explicit `index.html` in `href`s.
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
- Editing a section `.dc.html` and forgetting to copy it over the sibling `index.html` → the clean URL serves a stale page.
- Adding a page without a `<title>`, `description`, canonical link and og/twitter tags → shared links render bare (this happened to all five essay pages once).
- Hardcoding a fixed-px grid (`grid-template-columns:150px 1fr 60px`) with no mobile branch → the text column collapses on phones. Drive every multi-column grid from an `isMobile` branch in `renderVals()`, like `awardRowStyle` in `CV.dc.html`.
- Dropping a large source image straight into `assets/` without resizing it to its display size.

## 12. Lessons learned — read before editing (mistakes already made once; don't repeat them)

- **Thesis-name capitalization drifted.** `Work.dc.html` twice used lowercase "major project thesis" (a role line and an abstract sentence) despite §7/CLAUDE.md requiring the verbatim "Major Project Thesis" / "Minor Project Thesis" everywhere outside the CV's "(Bachelor's Thesis)" exception. Fixed 2026-07 — but after touching any thesis mention, `grep`-check case-sensitively (`major project thesis`, `minor project thesis`) that no lowercase copy crept back in.
- **`site-deploy/` silently goes stale.** It's a full mirror, not a symlink — editing a root file does not update it. Confirmed 2026-07 that only files actually edited that session had drifted; everything else stayed in sync. Always copy changed root files into `site-deploy/` (or diff the two trees) before finishing a turn that touches content.
- **CV bullets should read like a person describing their work, not a job posting.** Avoid gerund-stacked fragments ("Owns X, defining Y, leading Z…"). Write short, direct, present-tense sentences — one clear action per bullet, plain phrasing over compressed HR-speak.
- **Site-wide audit, Jul 2026 — what was fixed and must not regress.** `lang="en"` + canonical + full social metadata on all 12 pages; muted text darkened/lightened for WCAG AA; CV award rows made responsive (`awardRowStyle`/`awardRowStyleLast`/`awardYearStyle`, CV breakpoint raised to 1000px); CV education logo chips unified at 42px box / 36px logo; Work project cards given `role="button" tabIndex="0" aria-label` + Enter/Space handling + a focus ring, and Esc/←/→ wired for the detail view and lightbox; essay tables raised from 10px to 12px with sticky headers and `min-width` so they scroll instead of crushing; all images lazy-loaded, alt-texted and downscaled (total assets ~7 MB → ~4 MB); the duplicated 218 KB `.image-slots.state.json` sidecars replaced by real figure files; clean folder URLs + `robots.txt` + `sitemap.xml` + `.gitignore` added; print styles added to every page.
- **Never delete a whole asset folder to "swap in" optimised copies.** During the Jul 2026 optimisation pass `assets/logos/` and `assets/img/writing/` were deleted and refilled from an `_opt/` staging folder — but two files had been *skipped* by the compressor (already small enough) so they had no `_opt` copy, and were destroyed: `volvo.png` and `kudremukh-summit-fog.jpg`. Both have since been restored from originals the user re-supplied (`assets/logos/volvo.png` is the official Volvo Group iron mark, background knocked out to alpha, mark 204px inside a 240px frame; `kudremukh-summit-fog.jpg` is the user's own frame at 960×1280). **When optimising images, overwrite files in place or stage a complete copy — never delete the source directory first.**
- **Site-wide audit, Aug 2026 — what was fixed and must not regress.** (a) `Homepage.dc.html` had drifted a full version behind `index.html` (missing the hero-stats block) — re-synced; diff the pair every time. (b) The Homepage Experience-timeline stats block was `flex:none` with `white-space:nowrap` and got clipped off-screen below 760px — it now runs through `e.statsRowStyle` / `e.statBoxStyle` / `e.statBoxLineStyle` / `e.pillsGridStyle`, all `isMobile`-branched; never re-hardcode that row. (c) Light-mode category hues that failed AA were darkened — talk `#c1703f`→`#89502d`, publication/research/book `#4a7a43`→`#3f6839`, patent/passion `#a34a82`→`#914274`, award `#b8892e`→`#78591e`; solid white-text chips use `#ac6438` (talk/academic) and `#956f25` (award); dark patent tint `#d391bd`→`#d493be`. (d) The five Writing essay pages gained a **semantic colour token set** in `theme()` — `sem` (mode-aware TEXT colours: green/amber/red/purple/blue) and `fill` (constant FILL colours that keep white text ≥4.5:1: green `#4a7a43`, amber `#a46829`, red `#b3453a`, purple `#6a4d9c`, blue `#3568a0`, neutral `#57503f`), plus `onAccent`. They surface as `--sem-*` / `--fill-*` / `--on-accent` on the root style. **Never hardcode `#4a7a43`/`#b8752e`/`#b3453a`/`#6a4d9c` in an essay again** — use `var(--sem-x)` for text/dots/borders, `var(--fill-x)` (or `t.fill.x`) wherever white text sits on the colour, and `var(--on-accent)` for text on an `--accent` fill. Before the fix, dark mode reused the light values and produced white-on-`#f3ede0` at 1.17:1. (e) The **active** nav link on every section page was the one `nav-link` missing `display:inline-flex; align-items:center; min-height:44px; padding:0 2px` — a 16px tap target; keep it on all six. (f) Filter chips went `padding:8px 18px`→`11px 18px` and sort buttons `8px 16px`→`11px 16px` to clear the 40px floor.
- **Site-wide audit, Aug 2026 (second pass) — what was fixed and must not regress.** (a) **The reduced-motion gate was missing on seven of the eight Writing essays** — only `Articles.dc.html` and `Himalayan-Trek-Planner` had it. Every essay's `titleRef` FLIP now carries `reduceMotion()` and `if (typeof el.animate === "function" && !this.reduceMotion())`. Any new `element.animate()` anywhere on this site must be gated the same way; the CSS block cannot touch WAAPI. (b) Sub-11px text was raised to the 11px floor in three separate forms, and **all three must be swept, because each hides from the others' search**: (i) 20 CSS literals `font-size:10.5px` (essay back-links, ISB/iPhone section labels, the Wearable table group row, Himalayan's Read-next tags — the last two were missed on the first pass because that file was excluded from the sweep list); (ii) **SVG chart labels built in `renderVals()` as `React.createElement("text", { fontSize: 10.5 })`** — a `font-size:Npx` grep never matches these; sweep `fontSize:\s*N` too (34 of them across the ISB and iPhone axis builders: `deprChart.xAxis`, `utilityChart.xAxis` and the ISB equivalents); (iii) **relative-sized HTML** — a bare `<sup>` inside a 12.5px block computes to 10.42px, and no px literal exists to find. The `<sup>` tags in the iPhone formula box and sources note now carry an explicit `font-size:11px`. **Verify this finding by COMPUTED size, never by source string:** in the rendered page, walk `document.querySelectorAll("*")` and flag any leaf node whose `getComputedStyle(el).fontSize` parses below 11 (both files now measure 0). (c) **Read-time labels were overstated on six essays** and are now derived, not guessed: `round(bodyWords / 230) + 1 if the piece has ≥3 tables`. ISB 12→10, Wearable 14→11, Himalayan 10→8, iPhone 12→10, Tap-to-Track 12→11, Timeplan 12→10 — changed in **both** the essay byline and `allArticles.readTime` in `Articles.dc.html`. (d) **Every named source in the essays' "Data sources" note is now a link** (accent-coloured, `target="_blank" rel="noopener"`) to a canonical root page — deliberately root pages (isb.edu, ets.org/gre, dcrainmaker.com, mospi.gov.in …), not deep paths that rot. Six essays were shipping named sources with zero verifiable links. Do not remove them. (e) The Writing hub subhead omitted the Tools category; it now reads "…and the small tools I built for myself." Any new category must be reflected there **and** in the hub meta description. (f) `scope="col"` added to every `<th>` in the essay tables. **Careful:** a regex of `/<th(?![^>]*scope=)/` also matches `<thead>` and silently rewrites it to `<th scope="col"ead>`, which blanks the table — this happened once; always re-check `<thead>` count after such a sweep. (g) The footer copyright and hub category-card meta row wrapped mid-phrase around 900–1000px — fixed with `white-space:nowrap` on the spans plus `flex-wrap:wrap; gap:6px 12px` on the row. (h) The 9 Kudremukh photos were re-encoded in place at ≤1100px / q0.76 (1.8 MB → 1.2 MB) and each wrapper gained `role="img" aria-label="…"` — `<image-slot>`'s inner `<img>` is hardcoded `alt=""` inside shadow DOM, so the wrapper label is the only way to give those photos an accessible name. (i) `article:published_time` / `article:author` / `article:section` / `author` meta added to all eight essays, and `feed.xml` created. (j) Dead files removed: `writing/support.js` and `work/support.js` (69 KB each, a *different* build of the runtime — nothing loaded them; every subfolder page uses `../support.js`), all five 0-byte `.image-slots.state.json` sidecars, and the orphaned `assets/img/portfolio/timeplan-dashboard-1.png`. `screenshots/` was added to `.gitignore`. **`assets/img/portfolio/tap-to-track-1.png` is still an orphan** — it is an AI-generated diagram with garbled labels, unfit to publish; the Tap-to-Track essay needs a real screenshot instead.
- **Critical theme CSS + the theme/favicon script live in the REAL `<head>`, not in `<helmet>`.** They sit immediately before `<script src="support.js">`: the `<link rel="icon">` pair, `<style>html{background:#faf7f0}html.dark-init{background:#181410}body{margin:0;background:inherit}</style>`, and the `try{…ayush-theme…}` script. **Never move them back into `<helmet>`.** Helmet content is inside `<x-dc>` in the BODY, so it only executes after `support.js` boots and the DC runtime processes the helmet — by then the browser has already painted the default light `#faf7f0`, giving every navigation a white flash before dark mode applies. In `<head>` the `dark-init` class lands on the first paint (verified: the `<html>` background sequence goes straight to `#181410` with no light frame).
- **The hero eyebrow line was removed (Aug 2026).** The mono uppercase "System Engineer, ECU Cooling · Volvo Group" line with the accent dot above the `<h1>` is gone; the intro paragraph now carries the role, so the fact isn't lost. Don't re-add it — it duplicated the first sentence. (The job title itself is still "System Engineer, ECU Cooling" everywhere it appears: nav-less pages, CV, timeline, meta descriptions.)
- **The hero intro is TWO paragraphs, not one.** The first (`18.5px`, `--text-body`, `margin:0 0 14px`) is the work statement and carries the job title — the eyebrow line above it was removed, so any rewrite must keep "ECU cooling at Volvo Group" here. The second (`17px`, `--text-muted`, `margin:0 0 38px`) is the personal aside. The split is deliberate: one block buried the personal line at the end of a five-line slab; as a quieter second paragraph it gets its own beat and the hero stays scannable at three lines + two. Both are `max-width:580px` with `text-wrap:pretty`. Keep the aside genuinely short — no numbers, no patent/publication counts (the user explicitly does not want the hero to brag); it is about what he likes doing, not what he has achieved. Contrast checked: 9.45:1 and 5.80:1 in dark mode.
- **The hero stats row was removed (Aug 2026).** The four counters ("4+ Years Experience / 3 Patents / 8 Publications / 7 Talks") and their `heroStatsWrapStyle` + `heroStats` entries in `renderVals()` are gone by deliberate editorial choice — the counts were résumé filler and the Experience timeline below tells the same story better. Don't re-add a stats strip to the hero.
- **Accessibility scaffolding added Aug 2026 — keep it on every page.** Each page has, in this exact order: a `<a href="#main" class="skip-link">Skip to content</a>` link immediately before `<nav>`; then `</nav>`, the 84px spacer div, then `<main id="main">`; then `</main>` immediately before `<footer>`. The footer sits OUTSIDE `<main>`. The active nav link carries `aria-current="page"` on the six section index pages and `aria-current="true"` on the five Writing essays (an essay is a child of Writing, not Writing itself). The skip link uses `background:var(--accent); color:var(--bg)` so it stays legible in both themes. **Its positioning MUST stay in the helmet `<style>` block** (`.skip-link{position:absolute;left:-9999px;top:0;z-index:900}` + `.skip-link:focus{left:0;…}`) — this is one of the rare legitimate non-inline cases. An inline `left:-9999px` plus `style-focus="left:0"` does NOT work: the generated `:focus` class rule loses to the inline style, so the link stays off-screen while focused and a keyboard user tabs onto an invisible target. Only the visual chrome (background, color, padding, radius, font) belongs inline.
- **The favicon `<link>` has no `id`.** It used to be `id="favicon-icon"` with `getElementById`, which produced a duplicate id at runtime once the helmet was mirrored into `<head>`. The DC helmet-mirroring step inserts a SECOND `link[rel="icon"]` into `<head>` after both the inline run and `DOMContentLoaded` have fired, and the browser resolves the last one — so a one-shot re-run silently served the light favicon to dark-mode visitors. `__setFav()` therefore **dedupes**: it removes every `link[rel="icon"]` after the first, then sets `href` on the survivor. A `MutationObserver` on `document.head` re-runs it whenever a node is added. Don't reintroduce the id, and don't reduce this to a single one-shot call.
- **Work org logos carry intrinsic `width`/`height`.** `orgImgDims(shape, size)` mirrors the max-box in `orgImgStyle()` (wide-lg 220×48 / 213×43, wide 130×32 / 125×28, square 62 / 64) and feeds `p.orgImgW`/`orgImgH` and `selectedProject.orgImgW`/`orgImgH`. If you change a logo box size, change both functions together.
- **"Archived" articles are commented out, not deleted.** `Articles.dc.html`'s `allArticles` array hides an entry by wrapping its whole line in `/* … */` (see the comment right above the array). Before telling the user what's live/archived on the Writing page, actually read the array for `/* */`-wrapped lines — don't assume everything present is showing.
- **Writing-section audit, Aug 2026 — fluid type and no fixed-px grids.** (a) The Writing hub's category grid was `minmax(300px,1fr)`, which gave 3 columns + an orphan at the 1000px max width; it is now `repeat(auto-fit,minmax(min(100%,360px),1fr))` + `align-items:start` = a clean 2×2 on desktop, one column below ~800px. The `min(100%,…)` wrapper is what stops the track overflowing a 320px phone — use that form for any new `auto-fit` grid. (b) The category cards carried a leftover `margin-bottom:22px` on the article-title list from the removed "Open X →" CTA; that plus asymmetric `28px … 24px` padding made every shelf ~46px taller than its content. Padding is now symmetrical `clamp(22px,3vw,26px) clamp(18px,3vw,28px)`. (c) All display type in the section is now fluid: hub `<h1>` `clamp(34px,6vw,46px)` (re-applied Sep 2026 after the flat-list redesign shipped it as a fixed 46px; the same clamp is now on the Work/Publications/Talks/Reading `<h1>`s and `clamp(32px,5.6vw,44px)` on the CV's — the essay-card `<h2>` on the hub is a deliberate fixed 23px), card `<h2>` `clamp(26px,3.6vw,32px)`, essay section `<h2>` 28px → `clamp(23px,3.4vw,28px)`, essay stat/callout numbers 22/24/25px → matching clamps. **Do not reintroduce a fixed px size above 20px anywhere in `writing/`** — the only intentional fixed large size left is the 30px nav "AS." logo. (d) Three essay bar-chart rows used the exact fixed-px-grid antipattern §11 warns about (`120px 1fr 44px`, `150px 1fr 34px`, `170px 1fr 74px`); the label column is now `clamp(84–96px,24–26vw,orig)` so the bar cannot collapse on a phone. Two-up photo/comparison grids (`1fr 1fr`) became `repeat(auto-fit,minmax(min(100%,220px),1fr))` so they stack instead of crushing. (e) **The `prefers-reduced-motion` CSS block does not stop the shared-element FLIP transitions** — they run through `element.animate()` (Web Animations API), which CSS cannot touch. Any new WAAPI animation on this site must be gated the same way; adding it to the CSS block alone does nothing. **Superseded Sep 2026: the cross-page FLIP morphs were removed** — `Articles.dc.html` now only restores scroll position (`sessionStorage["art-scroll"]`), and no page calls `element.animate()` any more, so the CSS block alone genuinely covers the whole site. If you add a WAAPI animation, re-add the `reduceMotion()` helper with it.
- **The Himalayan Trek Planner essay is interactive (Aug 2026).** `writing/Himalayan-Trek-Planner.dc.html` carries seven importance sliders (`state.w`, 0–10 each) that re-rank all 65 treks live. **The sliders are importance values, not percentages** — `normalise()` converts them to integer percentages that sum to exactly 100 using largest-remainder rounding, and falls back to equal weight when every slider is at zero. Do not "fix" this into direct percentage sliders; the whole point is that the user can drag any slider freely without the others fighting back. Per-trek scores exist for `views`, `summit`, `culture`, `safety`; `weather`, `vibe`, `food` come from `QUARTER_DIMS` because they are properties of the season, not the trail — that split is stated in the article body and the sources note, so keep both in sync if the data changes. The `ALL_TREKS` array is the single source of truth for the master table, the bar chart, the top-five cards, the quarterly champions and Fig. 1; every one of those is derived in `renderVals()`, so adding a trek only means adding an array entry. The "editorial" column is the originally published score and deliberately diverges from the slider score (Chadar 9.0 vs ~7.0) — that gap has its own section in the article and is not a bug.
- **Site-wide audit, Aug 2026 (third pass) — what was fixed and must not regress.** (a) **`rel="noopener noreferrer"` is now on every one of the ~157 external `target="_blank"` links** across all 21 pages (CV alone had 44 unprotected). Any new `target="_blank"` anchor must carry it. (b) **Intrinsic `width`/`height` attributes on all 17 CV logos and the 2 Work prev/next thumbnails.** The 13 fixed-size CV logos take the same numbers as their inline CSS px; the 3 award logos styled `max-width:100%; max-height:100%` take their **natural file dimensions** (`narotam-sekhsaria.jpg` 240×85, `dr-kalam-fellowship.jpg` 240×52, `moyas-full.jpg` 240×122) so the browser knows the ratio before load. The remaining un-attributed images are correct as-is: cover/talk photos are `width:100%; height:100%` inside an already-sized box, and the Work lightbox `src` is dynamic. If you resize a logo, change the CSS px **and** the attribute together. (c) **`<meta name="theme-color">` on all 21 pages**, defaulting to `#faf7f0` and switched to `#181410` by the existing real-`<head>` theme script (a statement appended right after `d.style.background="#181410";` inside the `if(__d)` branch). It must stay JS-driven — the theme comes from `localStorage`, not `prefers-color-scheme`, so a `media`-scoped meta pair would be wrong. (d) **`<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>`** now sits beside the existing `fonts.googleapis.com` preconnect on every page. The stylesheet and the font *files* come from different hosts; without both, first text paint waits on an extra DNS+TLS round-trip. Keep the `crossorigin` attribute — a font preconnect without it opens the wrong kind of connection and is ignored. (e) **`og:site_name` / `og:locale` (`en_IN`) / `og:image:alt` / `twitter:title` / `twitter:description` on all 21 pages**, inserted directly after `twitter:card`. (f) **`404.html` brought to parity** — full og/twitter block, `theme-color`, gstatic preconnect, a `@media print` block, and the GA `gtag` snippet (so 404 hits are actually measurable). It deliberately still has **no skip-link and no reduced-motion block**: it has no nav to skip past and no animation to reduce. (g) **JSON-LD structured data added — three kinds, and they share one `@id`.** `index.html`/`Homepage.dc.html` carry a `@graph` of `Person` (`@id` `https://srivastav-ayush.github.io/#person`, with `jobTitle`, `worksFor`, `alumniOf`, `knowsAbout`, `sameAs` → LinkedIn/Scholar/GitHub/X) and `WebSite`; the 8 essays each carry a `BlogPosting` whose `author`/`publisher` reference that same `#person` `@id`; `publications/` carries an `ItemList` of the **8 scholarly works** (6 journal + 1 book chapter + 1 conference) with DOI `identifier` and `Periodical` `isPartOf`. **The 5 patents are deliberately absent — schema.org has no Patent type**, so there is nothing valid to emit; don't invent one. Every block is inserted immediately before `<link rel="canonical">` in the real `<head>`. **If a publication or essay is added, the JSON-LD must be hand-updated too** — it is static and cannot read the data arrays. (h) `sitemap.xml` `lastmod` is now `2026-08-12` on all 14 URLs; bump it whenever you touch a page.
- **iOS landscape text inflation is disabled in the `<head>` style, not per-element.** Rotating an iPhone to landscape made every page's type jump size: Safari's automatic text-size-adjust inflates font sizes when the layout width grows. Fixed Aug 2026 by extending the real-`<head>` rule to `html{background:#faf7f0;-webkit-text-size-adjust:100%;text-size-adjust:100%}` on **all 18 site pages** (root + `.dc.html` mirrors + `site-deploy/`). Keep both properties, and keep them in that `<head>` `<style>` — a helmet rule lands too late and the inflation is visible before it applies. (Layout itself already re-flows on rotation: pages with an `isMobile` branch carry a `resize` listener writing `state.width`; the five essay pages use `clamp()` only and need none.)
- **Timeplan essay updated from the project's own readmes (Aug 2026).** `writing/Timeplan-Dashboard.dc.html` was reconciled against the tool's shipped `README.md` + `README-AI.md` (2026-08-18 workbook revision). Facts that changed and must not regress: the panel formerly called **Warnings** is **Risks** with three tabs (at risk / Delays — the old "knock-on view" / File checks); **there is no sheet protection anywhere** any more, so never write that a save "preserves sheet protection" (it preserves formulas, conditional formats, colours and dropdowns); **Last updated fills itself in** from a workbook formula and the dashboard only stamps that cell when it holds a literal; **six** demo timeplans ship, not twelve (the title's "twelve" is about owners, not example files); Status is a **14-value** dropdown and Tracker is a formula with **five** outcomes (Done / Closed / Incomplete / Due this week / On Track); baseline captures are all kept, numbered within a month; the programme heading and the week-axis **milestones** are dashboard-owned and persist to a shared `Dashboard_Settings.json`; PDF export is landscape Letter/A3/A2. The screenshot `assets/img/writing/timeplan-gantt.jpg` was replaced with the user's own dashboard capture, cropped from the right of a 3360×1500 source to 2850px wide and resized to **1600×842** — the `<img>` `width`/`height` attributes track that ratio, so re-crop and re-attribute together. The CV "View PDF" Drive link was also re-pointed (file id `1ZA_L2xy6CzA20obXmoduureKBqRs1PvZ`) in all four CV copies.
