# CLAUDE.md

## Project
Floppy Days: a local-first, retro-desktop "pixel journal" web app. Each year is a "floppy disk" and each life area is a "file" on it. Users track life areas (sleep, water, mood...) as yearly colour grids, colour in each day by scribbling with mouse/finger, tick daily check-ins on a Today screen, and view the whole year as a Mosaic.

Read before working:
- `docs/PRD.md` – what and why (scope, user stories, non-goals)
- `docs/SPEC.md` – how (stack, architecture, data model, painter spec, design tokens)
- `docs/TASKS.md` – ordered task list; work on ONE task at a time

## Stack
Vite, React, TypeScript (strict), Tailwind CSS + CSS variables, React Router, Zustand, Dexie (IndexedDB) with dexie-react-hooks, date-fns, Vitest + React Testing Library, Playwright, ESLint + Prettier. Hosted on Vercel. No backend in the MVP.

## Commands
```bash
npm install
npm run dev        # local dev server
npm run build      # production build
npm run preview    # preview build
npm run lint
npm run typecheck  # tsc --noEmit
npm test           # vitest
npm run test:e2e   # playwright
```

## Folder structure
```
src/app        app shell, router, providers
src/desktop    retro OS chrome (Window, Desktop, DesktopIcon, Dock)
src/features   today/, journal/, painter/, mosaic/, trackers/, settings/
src/data       db.ts (Dexie), repo/ (all DB access), presets.ts, seed.ts
src/lib        dates, levels, colour, seeded random
src/styles     tokens.css, fonts
docs/          PRD, SPEC, TASKS, PROMPTS
design/
  inspo/       reference images (gitignored, other people's work; view only, never copy assets from them)
  sketches/    Sophia's own wireframes (committed)
```

## Conventions
- Functional components + hooks. One component per file, PascalCase filenames.
- Components never import Dexie directly; use `src/data/repo/*`.
- Store level IDs on entries, never colours. Colours are derived from tracker levels.
- Dates are local `YYYY-MM-DD` strings via `src/lib/dates.ts`. Never use `toISOString()` for calendar dates.
- All colours and spacing come from theme tokens in `tokens.css`. No hard-coded hex in components (preset data excepted).
- Pixel fonts only for headings/labels ≥14px; body text uses the readable font.
- Every interactive element is keyboard reachable with a visible focus state and an accessible label. Colour is never the only signal.
- Respect `prefers-reduced-motion`.
- Write unit tests for anything in `src/lib` and `src/data`. Add a component test for any new user-facing behaviour.
- Conventional Commits. Small commits with clear messages.

## Workflow rules for Claude Code
- Work on one TASKS.md item per session/branch. Propose a plan before writing code.
- Do not start the next task or add unrequested features.
- Run `npm run lint && npm run typecheck && npm test` before saying a task is done.
- Explain non-obvious code with brief comments (why, not what).
- Ask before adding a new dependency; say why it is needed.
- At the end of a task, summarise what changed, which files, and how to test it manually.

## Avoid
- Adding a backend, auth, or analytics (not in MVP scope).
- Pure black (#000) or pure red for "bad" days; follow the palette in SPEC.
- Shaming language or streak penalties anywhere in the UI.
- Using copyrighted characters, fonts, or icon packs without a permissive licence.
- Large refactors outside the current task.
- Copying artwork, icons, or text from `design/inspo/`. Use it for style direction only.
- Committing anything in `design/inspo/`.
