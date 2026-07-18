# Rome Curator — Frontend (rome-curator-frontend)

Curated AI travel-itinerary web app. This repo is the entire frontend:
one file, `index.html` (HTML + CSS + vanilla JS, no framework, no build
step). `vercel.json` handles SPA routing. Vercel auto-deploys on push.

**For full project context, decisions, and history, read `HANDOVER_NOTE.md`
in this repo's root before starting non-trivial work.** This file only
covers what's true every session.

## Owner context
Ashwin is non-technical — always explain a change in plain language
before making it, and confirm before anything destructive or ambiguous.
He uploads files to GitHub manually (drag-and-drop in the web UI), so
prefer complete file replacements over instructions requiring git CLI
knowledge, unless he's specifically asked to use git directly.

## Deploy
No build step — Vercel deploys `index.html` as-is on push to `main`.
**Always deploy the backend (`rome-curator-backend`) first and confirm
it's live on Render before pushing a frontend change that depends on it.**

## Critical don'ts
- Never reintroduce html2canvas or html2pdf.js for PDF export. The
  current `window.print()` + `@media print` approach is the settled
  solution after three failed attempts with those libraries — see
  HANDOVER_NOTE.md for why.
- Never put secrets in this file (it's fully public/client-side). PayPal
  Payment Link URLs are fine here — they're public by design, not secrets.
- After editing anything inside the two inline `<script>` blocks, run a
  syntax check (e.g. `node -e "new Function(fs.readFileSync('index.html'...)"`
  extracting the script contents) before considering the change done. A
  single missing brace anywhere in either script silently breaks the
  ENTIRE page's JS, not just the section touched — this has happened
  before.
- `MAX_DAYS_SINGLE` here must always match the same constant in the
  backend's `index.js` — they're duplicated, not shared, so a change to
  one without the other will silently desync frontend/backend limits.

## Traveller safety (applies to any AI-facing content this repo surfaces)
If working on destination content, city data, or anything that echoes
AI-generated recommendations: independent/self-guided access to informal
settlements (slums, favelas) should never be presented as safe or
encouraged, religious/community sites with real social tension shouldn't
be surfaced as casual sightseeing, and invented-sounding specific vendor
names should be avoided in favour of categories when uncertain. Full
reasoning and the four-rule breakdown is in HANDOVER_NOTE.md under
"TRAVELLER SAFETY GUARDRAILS."

## Key structures worth knowing before editing city/destination data
- `COUNTRY_CITIES`, `CITY_TAGLINES`, `CITY_WEATHER` (12 months),
  `CITY_SPECIALS` (3 interest chips), `LOADING_MSGS` (3 per city) —
  every city needs an entry in ALL FOUR, plus a matching
  `DESTINATION_CONTEXT` entry in the backend's `index.js`. Missing one is
  a silent content gap, not an error.
- `COUNTRY_CITY_GROUPS` — optional per-country state/region grouping for
  the city-picker dropdown (currently only defined for India). Add a
  new entry here if any other country grows large enough to need it.
- City/Region UI is a collapsible searchable dropdown, not a plain
  checkbox list — see `buildCityCheckboxHTML()`, `toggleCityDropdown()`,
  `filterCityOptions()`. Underlying multi-select logic still just reads
  `#cityCheckboxes input:checked` — don't change that contract without
  checking every function that calls `getSelectedCities()`.

## Style
Terracotta (#B85C38) brand colour, DM Sans font, mobile-first — test at
375px viewport width.
