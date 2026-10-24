# CLAUDE.md

Portfolio of Harshavardhan J (AWS Cloud × DevOps) — a Framer-exported static
site served through **Next.js 15 App Router + TypeScript**.

## Architecture

- `app/` — App Router routes: `/`, `/about`, `/contact`, `/resume`, each a
  `force-static` `route.ts` returning the original Framer-export document
  (`index.html`, `about/index.html`, `contact/index.html`, `resume/index.html`)
  via `lib/framerPage.ts`. The documents carry their own title/meta tags.
- Not React pages on purpose: a Server Component wrapper re-embedded the whole
  markup in the RSC payload (2× HTML) and shipped Next's client runtime for
  nothing — the Framer runtime hydrates the page itself. Client-side
  `next/link` navigation is therefore not available between these routes.
- Page transitions: the `<head>` of each document opts out of the Framer
  shutter (`<html data-no-trans>`), uses a 200 ms cross-document
  `@view-transition` fade, and prefetches the four routes on hover
  (speculation rules). Keep the four documents' snippets in sync.
- `next.config.ts` sets short cache lifetimes for `/_fr/*` and `/assets/*`.
- `public/_fr/` and `public/assets/` — Framer runtime modules and images,
  served at their original absolute paths `/_fr/*` and `/assets/*`.
- The Formspree contact form and all animations live inside the Framer
  runtime; app code does not own them.

## Commands

- `npm run dev` — dev server
- `npm run build` — production build (also the typecheck gate; no tests/linter)
- `npm start` — production server

## Rules

- Structure conversion only. Do not redesign, restyle, or rewrite content.
- Keep the original HTML sources in place — they are read at render time.
- Don't convert the Framer markup to React components or reorganize `public/`.
- `script_main.*.mjs` skips Framer's badge mount when
  `#__framer-badge-container` is absent (it threw React #405 on every page).
- The server-rendered markup inside `#main` is still the Framer template
  (e.g. "MARA VOSS", "Work I'm proud of"); the runtime replaces it, logging
  React #418 hydration warnings. Only a fresh Framer export fixes that.
- `/_fr/*` is cached 10 minutes: after editing a runtime module, hard-reload
  to see the change.
