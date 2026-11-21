# Technical Spec: Floppy Days

Companion to `PRD.md`. This is the doc Claude Code builds from. If something here conflicts with the PRD, the PRD wins on *what*, this doc wins on *how*.

---

## 1. Stack

| Layer | Choice | Why |
|---|---|---|
| Build tool | **Vite** | Fast dev server, simple config, no server needed for a local-first app |
| UI | **React + TypeScript** | Industry standard; types catch data-model bugs early |
| Styling | **Tailwind CSS** + CSS variables for theme tokens | Fast to iterate; themes swap by changing variables |
| Routing | **React Router** | Standard SPA routing |
| UI state | **Zustand** | Tiny, no boilerplate (selected tracker, open windows, current marker) |
| Persistence | **IndexedDB via Dexie** (+ `dexie-react-hooks` `useLiveQuery`) | Local-first, works offline, no backend to host; structured queries by date |
| Dates | **date-fns** | Small, tree-shakeable, good local-date helpers |
| Painting | **HTML Canvas + Pointer Events** | One API for mouse, pen, and touch |
| Testing | **Vitest** + **React Testing Library**; **Playwright** for 2-3 end-to-end flows | Vite-native, fast |
| Lint/format | ESLint + Prettier | Consistency for PR reviews |
| CI | GitHub Actions: lint, typecheck, test on every PR | Shows professional workflow |
| Hosting | **Vercel** (preview deploy per PR) | Free, auto-deploys from GitHub |
| Icons | `pixelarticons` (MIT) + custom sprites | Consistent pixel style, licence-safe |

Use current stable versions of everything. Do not add a dependency without a reason noted in the PR.

**v1.1 (not MVP):** Supabase (Postgres + Auth + Row Level Security) as a sync layer. The Dexie data layer is behind a repository interface so it can be swapped or mirrored later.

## 2. Architecture

```
src/
  app/            # App shell, router, providers, theme
  desktop/        # Retro OS chrome: Desktop, Window, Taskbar/Dock, DesktopIcon
  features/
    today/        # Today window: check-ins, reminders, quick colouring
    journal/      # Disk view (journal), tracker grid (year view), tracker grid (year view)
    painter/      # Colouring canvas + coverage detection
    mosaic/       # Whole-year overview
    trackers/     # Preset library, custom tracker editor, legend editor
    settings/     # Theme, export/import, sample data, sound
  data/
    db.ts         # Dexie schema + migrations
    repo/         # journalRepo, trackerRepo, entryRepo, reminderRepo (all DB access goes here)
    presets.ts    # Preset tracker definitions
    seed.ts       # Sample-year generator for demo mode
  lib/            # date utils (local YYYY-MM-DD), level resolution, seeded random, colour utils
  styles/         # tokens.css (themes), fonts
  test/           # test utils, fixtures
```

Data flow: components read via `useLiveQuery` hooks that call repo functions. Components never import Dexie directly. Writes go through repo functions, which validate input.

## 3. Data model

```ts
type ISODate = string; // 'YYYY-MM-DD' in the user's LOCAL timezone. Never use Date.toISOString() for this.

interface Journal {
  id: string;            // nanoid
  year: number;          // 2026
  name: string;          // "My 2026"
  createdAt: number;
}

type TrackerKind = 'scale' | 'category' | 'check';
// scale    = ordered levels, optionally from a number (sleep hours, water glasses)
// category = unordered labels (mood: happy, calm, anxious...)
// check    = done / not done (meds, chores)

interface Level {
  id: string;
  label: string;         // "7-9 hrs"
  color: string;         // hex
  min?: number;          // inclusive, for numeric scales
  max?: number;          // exclusive; undefined = no upper bound
}

interface Tracker {
  id: string;
  journalId: string;
  name: string;          // "Sleep"
  icon: string;          // pixelarticons name or custom sprite id
  kind: TrackerKind;
  unit?: string;         // "hours", "glasses"
  levels: Level[];       // ordered; drives the legend and colours
  order: number;         // position on disk / mosaic
  presetId?: string;
  archived: boolean;     // archived trackers live in the "Trash" icon, restorable
  createdAt: number;
}

interface Entry {
  id: string;            // `${trackerId}:${date}` (one entry per tracker per day)
  trackerId: string;
  date: ISODate;
  levelId: string;
  value?: number;        // raw number if entered (7.5 hours)
  note?: string;         // short note (should-have)
  updatedAt: number;
}

interface Reminder {
  id: string;
  title: string;         // "Take meds"
  time?: string;         // 'HH:MM' local; optional (some are all-day)
  days: number[];        // 0-6, Sunday = 0
  linkedTrackerId?: string;
  linkedLevelId?: string;   // level applied when ticked (for 'check' trackers)
  countStep?: number;       // for count check-ins: +1 per tap (should-have)
  enabled: boolean;
  order: number;
}

interface ReminderLog {
  id: string;            // `${reminderId}:${date}`
  reminderId: string;
  date: ISODate;
  count: number;         // 1 for a simple tick
  updatedAt: number;
}

interface Settings {
  id: 'settings';
  theme: 'cream-soda' | 'mint-os' | string;
  soundOn: boolean;
  reducedMotion: 'system' | 'on' | 'off';
  demoMode: boolean;
}
```

Dexie indexes: `entries: id, trackerId, date, [trackerId+date]`, `trackers: id, journalId, order`, `reminderLogs: id, reminderId, date`.

**Key rule: store the level, derive the colour.** Changing a tracker's colours or the app theme must never rewrite entries.

**Level resolution:** for numeric scales, `resolveLevel(levels, value)` returns the level whose `[min, max)` contains the value. Unit-test the boundaries (e.g. exactly 7 hours).

**Deleting a level** that has entries: block it, or require choosing a level to reassign those entries to.

## 4. Presets (starting set)

| Tracker | Kind | Levels (low to high) | Notes |
|---|---|---|---|
| Sleep | scale (hours) | <3, 3-5, 5-7, 7-9, 9+ | maroon `#B5485D` → magenta `#D46A9F` → purple `#9B7FD1` → blue `#6C9BD2` → navy `#34507A` |
| Water | scale (glasses) | 0-2, 3-4, 5-6, 7-8, 9+ | pale to deep aqua |
| Mood | category | energetic, happy, calm, normal, stressed, anxious, lonely, sad, angry | pastel rainbow, labels always visible |
| Exercise | scale (minutes) | 0, 1-20, 20-45, 45+ | |
| Screen time | scale (hours) | <2, 2-4, 4-6, 6+ | lower is "better"; palette goes light → heavy, not green → red |
| Study / Work | scale (hours) | 0, <2, 2-4, 4-6, 6+ | |
| Budget | category | under, on budget, over, no-spend day | |
| Chores | scale | none, some, all | |
| Meds | check | taken, missed | blank = not logged |
| Steps | scale | <3k, 3-6k, 6-10k, 10k+ | |

All preset palettes must pass a quick check: adjacent levels are distinguishable, and every level has a text label (never colour-only meaning).

## 5. Screens

1. **Desktop (home)** – wallpaper, desktop icons: `Today.exe`, `2026.flp` (journal floppy disk), `Mosaic`, `Library` (presets), `Settings`, `Trash`. On load, `Today` window is open.
2. **Today window** – date header; list of today's check-ins (tick / +1); a row of large "today cells", one per tracker, each tappable to open the Painter.
3. **Disk window (journal)** – opens like a disk drive in a retro file explorer; tracker files shown as pastel file icons with name and mini preview; "+ New tracker" file; status bar shows "x of 365 days saved".
4. **Tracker window** – year grid (12 columns J-D, 31 rows) with legend on the right, like a paper spread. Click a cell to open the Painter for that day. Basic stats below (should-have).
5. **Painter (modal window)** – large single cell, legend as marker swatches, scribble to fill, "Quick fill" and "Clear" buttons.
6. **Mosaic window** – all tracker grids side by side, small cells, tracker names on top; toggle to hide trackers; "Export PNG" (should-have).
7. **Tracker editor** – name, icon, kind, unit, levels (add/remove/reorder, colour picker, ranges), live legend preview.
8. **Settings** – theme picker, sound, reduced motion, export/import JSON, "Load sample year", "Reset all data" (with confirm).

**Responsive behaviour:** at widths under 768px, the desktop becomes a home screen of icons and each window opens full screen with a back button. Desktop windows are fixed-position in the MVP (draggable is stretch).

## 6. Painter interaction spec (the signature feature)

- Canvas sized to the cell (about 240-320px square), scaled for `devicePixelRatio`.
- `touch-action: none` on the canvas; use `pointerdown/move/up` with `setPointerCapture` so strokes do not drop when leaving the canvas.
- Stroke: round caps and joins, marker colour at ~85% opacity, `globalCompositeOperation = 'multiply'` so overlapping strokes darken slightly like real marker. Interpolate between pointer samples so fast strokes stay continuous.
- **Coverage detection:** keep a 16x16 boolean grid over the canvas; each stroke segment marks grid squares within the brush radius. Coverage = marked / total.
- At **coverage ≥ 70%**: "settle" the cell (short fill animation, soft sound), save the entry, close or advance to the next tracker.
- Below threshold on pointer up: keep the strokes so the user can continue.
- Switching marker mid-colour clears and restarts with the new colour.
- Quick fill: a button and keyboard path (Enter on a focused swatch) that saves instantly. Required for accessibility.
- `prefers-reduced-motion`: skip the settle animation.
- Strokes are not persisted in the MVP. On the grid, filled cells get a subtle marker texture generated from a seeded random (seed = `trackerId + date`) so each cell looks hand-coloured but stays consistent across reloads.

## 7. Design system

**Look:** cream retro desktop, chunky 2px dark outlines, hard offset shadows, pastel window title bars with dot buttons, pixel icons.

```css
/* tokens.css – theme: cream-soda (default) */
--bg: #FFF8E7;          /* desktop / page */
--surface: #FFFDF6;     /* window body */
--ink: #3B3030;         /* outlines + text (soft brown-black, not pure black) */
--muted: #8C7F7A;
--accent-1: #C9B6EC;    /* lilac title bars */
--accent-2: #F7BFD0;    /* pink */
--accent-3: #FBE3A1;    /* butter */
--accent-4: #BFE3D0;    /* mint */
--accent-5: #BFE1EA;    /* sky */
--border: 2px solid var(--ink);
--shadow: 4px 4px 0 var(--ink);
--radius: 6px;

/* theme: mint-os – swaps --bg to #CFE8E2 and title bars to mint */
```

**Type:** `Pixelify Sans` (or `Silkscreen`) for headings, window titles, numbers on grids. A readable sans/mono (e.g. `DM Sans` or `Space Mono`) for body. Never pixel fonts below 14px.

**Motion:** 150-250ms, ease-out, small "pop" on settle. No continuous animations.

**Sound (should-have):** short marker scribble loop while drawing, soft chime on settle. CC0 sources only. Muted by default on first visit.

## 8. Demo mode

"Load sample year" (also offered on first visit) generates a realistic year for 6 trackers with plausible patterns (worse sleep in exam months, more exercise in summer) and some blank days. Sample data is flagged so "Clear sample data" removes only it.

## 9. Name and metaphor

**Floppy Days.** Every coloured pixel "saves" a day. The metaphor runs through the UI:

| Concept | Retro PC element |
|---|---|
| Journal (one year) | Floppy disk icon on the desktop, labelled `2026.flp`, with a hand-written-style pastel label |
| Tracker | A file saved on the disk |
| Colouring a day | Settle animation can show a tiny "Saving..." bar |
| Export / import backup | "Save to disk" / "Load disk" |
| Archived trackers | Trash |
| App icon / favicon | Pastel pixel floppy disk |

Keep the jokes light: copy should stay calm and readable (see PRD design principles). Repo name: `floppy-days`.

## 10. Testing approach

- **Unit (Vitest):** `resolveLevel` boundaries, local date helpers (DST, month lengths, Feb 29), coverage calculation, seed generator determinism, export/import round trip.
- **Component (RTL):** tracker editor validation, quick fill saves an entry, linked check-in colours the tracker.
- **E2E (Playwright):** (1) first visit → load sample → open Mosaic; (2) create custom tracker → colour today → reload → still coloured; (3) tick linked reminder → tracker cell filled.
- Manual: painter on a real phone (iOS Safari + Android Chrome) and trackpad.

## 11. Risks

| Risk | Mitigation |
|---|---|
| Scope creep (avatar, gamification, sync) | Strict MVP list; stretch items only after deploy |
| Painter feels laggy or janky on mobile | Build it as an isolated spike in Milestone 3 and test on a phone early |
| Timezone / date bugs | Local `YYYY-MM-DD` helpers only, unit tested |
| Users lose local data (cleared browser) | Export/import JSON; nudge to back up monthly; sync in v1.1 |
| Retro desktop UI awkward on phones | Full-screen windows under 768px |
| Colour-only meaning excludes colour-blind users | Labels on every level, legend always visible, tested palettes |
| Notifications don't work when tab is closed | MVP is in-app reminders only; documented as a known limit |
