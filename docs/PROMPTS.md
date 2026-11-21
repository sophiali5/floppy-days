# Example Claude Code prompts

Pattern for every task: **plan first, then build, then verify, then explain.** Start each task on a fresh branch and a fresh Claude Code session (or `/clear`) so context stays focused.

---

### Milestone 0
> Read CLAUDE.md and docs/. We're doing task M0.1 and M0.2 from docs/TASKS.md. Scaffold a Vite + React + TypeScript app in this repo (strict TS), then add Tailwind, ESLint, Prettier, and Vitest with the npm scripts listed in CLAUDE.md. Show me your plan before running anything. When done, run lint, typecheck, and tests and tell me what each config file does.

> Task M0.3: add a GitHub Actions workflow that runs lint, typecheck, and tests on pull requests to main. Explain each step of the YAML.

### Milestone 1
> Task M1.1 + M1.2. Using the tokens in SPEC section 7, create src/styles/tokens.css with the cream-soda and mint-os themes, load Pixelify Sans and DM Sans, and build a reusable `<Window>` component (title bar with dots, close button, 2px ink border, 4px hard shadow). Add a /dev/ui page showing windows in both themes. Use design/inspo/retro-windows.png for style direction (don't copy it). Plan first.

> Task M1.5: make the desktop responsive. Under 768px, show icons as a home screen and open each window full screen with a back button. Test at 375px and 1280px and tell me what you checked.

### Milestone 2
> Task M2.1–M2.3. Implement the Dexie schema and TypeScript types exactly as in SPEC section 3, the repo functions, and src/lib/dates.ts + resolveLevel. Write unit tests first for resolveLevel boundaries and date helpers (including Feb 29 and month lengths), then make them pass.

> Task M2.5: write a deterministic sample-year generator (seeded random) for 6 presets with realistic patterns described in SPEC section 8. Add a test that the same seed gives the same output.

### Milestone 3 (painter)
> Task M3.1–M3.2. Build the Painter per SPEC section 6 as an isolated component on /dev/painter. Use pointer events with setPointerCapture, touch-action none, DPR scaling, interpolated round strokes, multiply blend, and a 16x16 coverage grid that triggers onSettle at 70%. Put the coverage math in a pure function with unit tests. Walk me through how pointer capture and coverage work after.

> The painter feels [laggy / too easy to fill / strokes have gaps] on my phone. Investigate why and propose 2 options before changing anything.

### Milestone 4
> Task M4.3–M4.4. Build the tracker year grid (12 columns J–D, 31 rows, invalid dates disabled, legend on the right like the attached bullet journal photo). Clicking a cell opens the Painter for that tracker/date and saves the entry via entryRepo. Grid should update live via useLiveQuery.

> Task M4.6: custom tracker editor. Validate: name required, at least 2 levels, numeric ranges must not overlap or have gaps, every level has a label. Block deleting a level that has entries unless the user picks a level to reassign them to. Add component tests for validation.

### Milestone 5–6
> Task M5.3: on the Today window, ticking a reminder linked to a tracker writes an entry with linkedLevelId for today; unticking removes it. Add a Playwright test for this flow.

> Task M6.3: export/import all data as JSON with a schemaVersion field. Import must validate the shape and refuse bad files with a friendly message. Add a round-trip test.

### Review prompts (use often)
> Review the diff on this branch like a senior engineer. Point out bugs, accessibility issues, and anything that breaks CLAUDE.md conventions. Don't fix yet, just list.

> Explain this file to me line by line as if I need to defend it in an interview.

> Write the PR description for this branch: what changed, why, how to test, screenshots needed.
