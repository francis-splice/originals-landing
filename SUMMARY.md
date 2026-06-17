# Originals Relaunch Landing — Living Summary

> Source of truth for design and product decisions in this repo. Maintained via the `update-doc` skill. When resuming a session, read this file first.

## Most recent change
2026-06-17: Published to its own **public GitHub repo** (`francis-splice/originals-landing`) and enabled **GitHub Pages** (main/root). Live at https://francis-splice.github.io/originals-landing/ (page + covers + fonts verified 200; noindex prototype). Earlier the same day: migrated out of the `sfa-tokens` token-studies repo into this standalone working folder, fixed the standalone links, added `.claude/launch.json` (preview on port 8124) and `.nojekyll`.

## What this is
A hi-fi, clickable, responsive **conversion landing page** for the Originals relaunch: drive Splice paid-marketing traffic to INSTRUMENT subscriptions. "All of Originals. One subscription." for £12.99/month. Built from the [PRD](https://app.notion.com/p/381cb340c45c8172a97fef2525af1be4). This is a design prototype and the visual reference for the eventual Sanity production build, not the production page itself.

## Live URLs
- Live (GitHub Pages): https://francis-splice.github.io/originals-landing/
- Repo: https://github.com/francis-splice/originals-landing (public; `main` branch root, Pages source = `main`/`/`; publish = `git push`). `.nojekyll` present; `.gitignore` keeps `.claude/` out.
- Local preview: http://localhost:8124/ (via `.claude/launch.json`, `python3 -m http.server 8124`).
- Existing checkout every CTA targets: https://splice.com/plans/checkout?products=1064fbac-424b-4a68-8eab-981ad9cc2aef
- Plans page: https://splice.com/instrument/plans

## Project shape
- `index.html` — the whole page: inlined CSS + a small vanilla JS that renders the title grid and hero marquee from a data array and runs the audio player. Self-contained, deployable as-is.
- `assets/covers/` — 19 real Originals square covers, `sips -Z 800` downscaled (~3.8MB total).
- `assets/fonts/` — self-hosted Inter 400–900 woff2 (the Splice-approximating typeface).
- `.claude/launch.json` — Claude Preview config (`static`: `python3 -m http.server 8124`).
- `.nojekyll` — so GitHub Pages serves asset folders as-is if deployed there.
- Audio previews stream live from the Spitfire CDN (no local MP3s).

## Audience and tone
First-party Spitfire/Splice design work (the user is a designer there), so no third-party copyright concern. It is a Splice destination page selling a subscription, so it wears the Splice INSTRUMENT brand, not the Spitfire product-page styling.

## Design decisions, newest first

### Brand & structure
- **2026-06-17** — Built from the PRD as a hi-fi clickable HTML prototype (not the Sanity production build, not a spec doc). **Splice INSTRUMENT brand**: dark base + pink→magenta gradient, self-hosted Inter, explicitly NOT the Spitfire GT tokens, since it reads as a Splice page. Real Spitfire CDN cover art for the 19 live titles + 4 styled "New" tiles for the not-yet-live perpetuals (Emotional Cello, Intimate Brass, Intimate Woodwinds, Crystal Keys). Single Subscribe CTA → existing checkout throughout, zero free-download. Prototype safeguards: status bar, `[Prototype]` title, `noindex`.
- **2026-06-17** — Section order (PRD narrative): sticky nav → hero ("All of Originals. One subscription.") → 4 value beats → Originals showcase → £600+→£12.99 value math → proof (walkthrough video + quote) → "more than Originals" platform band → Craft spotlight → two-plan pricing (INSTRUMENT featured + Creator) → closing band.

### Audio (surface previews early)
- **2026-06-17** — It is a conversion page for a sounds product, so audible proof belongs near first contact. The showcase covers are playable, a hero "Hear the collection" hook + 3 signature chips (Cinematic Soft Piano, Felt Piano, Epic Strings) start a preview above the fold, and a sticky mini-player (art, title, prev/play/next, seek, close) docks on first play and follows the scroll to the CTA. One detached `<audio>` object is the single source of truth (only one track plays, one tile highlights). Real Spitfire demo MP3s stream from the CDN via a slug→URL map in the page script.

### Craft (anti-slop)
- **2026-06-17** — Impeccable slop pass (brand register): no em dashes, no gradient text (the £12.99 figure is solid pink), no side-stripe borders (the testimonial uses an oversized quote mark). The two card grids are de-templated: 4 value beats are a numbered editorial list (01–04, hairline rules, no icons); 6 platform features are a borderless two-column spec list.

## Open questions
- Title count reads "23" (19 live + 4 new) vs the PRD's "22 (figure to confirm)". Self-consistent with the grid; adjust if the official count lands at 22.
- Deploy: resolved 2026-06-17 to public GitHub Pages (`francis-splice/originals-landing`), matching the `sfa-tokens` pattern. Audio streams from the Spitfire CDN, so the live page depends on those URLs staying put.

## Rejected directions
- **Local-download / placeholder audio** (2026-06-17). Considered downloading the 19 demo MP3s into `assets/audio/` (fully self-contained) or styling the player around silent placeholders. Chose **streaming the real MP3s from the Spitfire CDN** (lighter repo, audio-accurate, but drifts if Spitfire moves the files). Self-hosting stays the path to archival independence if needed.

## Discoveries (technical)
- **Spitfire demo audio URLs** live in each product page's `data-audio-file="//www.spitfireaudio.com/cdn/shop/files/<name>.mp3?v=..."` attribute (read by the `<audio-demo>` web component), not in any JSON. Scrape with `grep -oE 'data-audio-file="//[^"]+\.mp3[^"]*"'`. The MP3s serve `206 audio/mpeg` with range support and play cross-origin in a plain `<audio>` element (media playback needs no CORS; only canvas/WebAudio analysis does), so stream the absolute https URLs rather than downloading.
- **Originals cover art** pattern: `https://www.spitfireaudio.com/cdn/shop/files/smcXXXX_square_press.{jpg,png}` (also via the collection JSON `https://www.spitfireaudio.com/collections/originals/products.json`). The four newest perpetuals were 404 (not yet live), hence the styled "New" tiles.
- **`new Audio()` is detached from the DOM**, so `document.querySelector('audio')` finds nothing; verify playback via the `play`/`timeupdate` events plus the network panel (the `.mp3 → 206` request), not by querying for an audio node.
- **Claude Preview's "desktop" native window is ~541px wide**, and forcing a custom width (e.g. 1440) renders screenshots black. Confirm wide-desktop layout via `getComputedStyle` (e.g. grid column count) rather than screenshots; use the mobile preset for visual mobile checks. Programmatic `location.reload()` can land the tab on the server root, so re-navigate to the page URL before inspecting.

## Adjacent files
- PRD: [Originals Relaunch INSTRUMENT Subscription Landing Page](https://app.notion.com/p/381cb340c45c8172a97fef2525af1be4) (Notion).
- Plan file (from the build session): `~/.claude/plans/brainstorm-creating-a-landing-fuzzy-ripple.md`.
- Origin: migrated 2026-06-17 from `sfa-tokens` (commit history for the build lives there up to that point).
