# AS Website — project rules
- `index.html` is the deployed GitHub Pages homepage and `Homepage.dc.html` is its working copy. They MUST stay byte-identical: any edit to one must be applied to the other in the same turn (verify with a diff script if unsure).
- The same rule applies to every section folder's clean-URL mirror: `work/index.html` ↔ `work/Work.dc.html`, `publications/index.html` ↔ `publications/Publications.dc.html`, `talks/index.html` ↔ `talks/Talks.dc.html`, `writing/index.html` ↔ `writing/Articles.dc.html`, `cv/index.html` ↔ `cv/CV.dc.html`.
- Internal links use explicit `index.html` paths (`work/index.html`, `../cv/index.html`, `../index.html` for home) — never `Work.dc.html`-style paths, and never bare folder URLs like `work/` (those 404 outside GitHub Pages, including in the design preview). Clean URLs belong only in `sitemap.xml` / `canonical` / `og:url`.
- Job title is "System Engineer, ECU Cooling" everywhere.
- Thesis wording: "Major Project Thesis" (KTH / SocketSense) and "Minor Project Thesis" (FGM cylinder) everywhere; exception — the CV SocketSense title line uses "(Bachelor's Thesis)" in the bracket.
- Every text/background colour pair must clear WCAG AA (4.5:1). Every `<img>` needs `loading="lazy" decoding="async"` + a meaningful `alt`, and must be sized to its display size. Every page keeps its `prefers-reduced-motion` and `@media print` blocks.
- Read `AI_README.md` in full before editing.
