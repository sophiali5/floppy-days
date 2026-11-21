# Task List: Floppy Days MVP

Each task = one branch + one PR. Keep PRs small enough to review in 10 minutes. Tick boxes as you merge.

Branch names: `feat/...`, `fix/...`, `chore/...`, `docs/...`, `test/...`
Commits: Conventional Commits, e.g. `feat(painter): add coverage detection`

---

## Milestone 0: Setup (half a day)
- [ ] M0.1 `chore: scaffold Vite + React + TS app` – runs locally, default page replaced with "Hello Floppy Days"
- [ ] M0.2 `chore: add Tailwind, ESLint, Prettier, Vitest` – `npm run lint`, `npm test` pass
- [ ] M0.3 `chore: add GitHub Actions CI` – lint + typecheck + test on PR
- [ ] M0.4 `chore: deploy to Vercel` – live URL, SPA rewrite in `vercel.json`, preview deploys on PRs

## Milestone 1: Design system + retro shell (2-3 days)
- [ ] M1.1 `feat(theme): tokens.css with cream-soda and mint-os themes + fonts`
- [ ] M1.2 `feat(desktop): Window component` (title bar, dots, close, border, shadow) + Storybook-style demo page `/dev/ui`
- [ ] M1.3 `feat(desktop): Desktop with wallpaper and DesktopIcon grid`
- [ ] M1.4 `feat(desktop): open/close windows via Zustand store + routes`
- [ ] M1.5 `feat(desktop): mobile layout (full-screen windows under 768px)`
- ✅ Checkpoint: you can click icons, open empty windows, switch theme, and it looks like the inspo on desktop and phone.

## Milestone 2: Data layer (2 days)
- [ ] M2.1 `feat(data): Dexie schema + types from SPEC section 3`
- [ ] M2.2 `feat(data): repos for journal, tracker, entry` + unit tests
- [ ] M2.3 `feat(lib): local date helpers + resolveLevel` + unit tests (boundaries, Feb 29, DST)
- [ ] M2.4 `feat(data): presets.ts`
- [ ] M2.5 `feat(data): sample-year seed generator` + determinism test
- ✅ Checkpoint: tests pass; dev page can create a journal and dump data.

## Milestone 3: Painter spike (2-3 days) ⭐ signature feature
- [ ] M3.1 `feat(painter): canvas with pointer-event strokes` (mouse, trackpad, touch)
- [ ] M3.2 `feat(painter): coverage grid + 70% settle` + unit test for coverage math
- [ ] M3.3 `feat(painter): marker swatches from legend, quick fill, clear, keyboard path`
- [ ] M3.4 `feat(painter): settle animation + reduced-motion support`
- ✅ Checkpoint: test on your actual phone. It should feel good. Iterate here before moving on.

## Milestone 4: Journal + trackers (3-4 days)
- [ ] M4.1 `feat(journal): create journal for current year; disk window`
- [ ] M4.2 `feat(trackers): preset library window; add tracker from preset`
- [ ] M4.3 `feat(journal): tracker year grid (12x31) + legend; invalid dates disabled`
- [ ] M4.4 `feat(journal): click cell → Painter → save entry → grid updates`
- [ ] M4.5 `feat(journal): seeded marker texture on filled cells`
- [ ] M4.6 `feat(trackers): custom tracker editor (levels, colours, ranges, validation)`
- [ ] M4.7 `feat(trackers): archive to Trash + restore`

## Milestone 5: Today + reminders (2-3 days)
- [ ] M5.1 `feat(today): Today window with today-cells per tracker → Painter`
- [ ] M5.2 `feat(reminders): reminder CRUD (title, time, days, linked tracker/level)`
- [ ] M5.3 `feat(today): check-in list; ticking linked reminder writes entry`
- [ ] M5.4 `test(e2e): linked reminder colours tracker`

## Milestone 6: Mosaic + settings (2 days)
- [ ] M6.1 `feat(mosaic): all trackers side by side, toggles to hide`
- [ ] M6.2 `feat(settings): theme, sound, reduced motion`
- [ ] M6.3 `feat(settings): export/import JSON (with schema version) + round-trip test`
- [ ] M6.4 `feat(onboarding): first-visit prompt to load sample year; clear sample data`

## Milestone 7: Ship MVP (2 days)
- [ ] M7.1 Accessibility pass (focus states, labels, contrast, Lighthouse 90+)
- [ ] M7.2 E2E flows from SPEC section 10
- [ ] M7.3 README with GIF, screenshots, tech decisions, what I learned
- [ ] M7.4 Tag `v1.0.0`, share with 5-10 testers, collect feedback in GitHub Issues

## After MVP (pick based on feedback)
- [ ] Insights (per-tracker stats, streaks)
- [ ] Count check-ins (+1 water)
- [ ] Mosaic PNG export ("Year Wrapped")
- [ ] Sounds
- [ ] Supabase accounts + sync
- [ ] Avatar + gentle stats profile
- [ ] Quilt view, correlations, draggable windows, PWA
