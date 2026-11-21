# PRD: Floppy Days

> A retro-desktop pixel journal where you colour in your days by hand, and your whole year becomes one mosaic.

Owner: Sophia Li · Status: Draft v0.1 · Last updated: 2026-11-21

---

## 1. Problem statement

Paper bullet journals ("year in pixels", habit grids, screen-time bars) are calming and satisfying to fill in, but they are hard to keep up, hard to change once drawn, and give you no insight beyond what you can eyeball. Digital mood trackers fix the upkeep problem but feel clinical: you tap a button, a square changes colour, and the ritual is gone. Most of them also track only mood.

**Floppy Days keeps the ritual of colouring in a journal while adding the flexibility and big-picture view only software can give.**

## 2. Target users

| Persona | Description | What they want |
|---|---|---|
| **The bujo-curious student** (primary) | 18-25, likes the aesthetic of bullet journals, has tried one and dropped it in February | Something cute and low-effort they actually keep using |
| **The habit builder** | Trying to sleep more, drink water, hit the gym, cut screen time | See several habits side by side and notice what slips |
| **The reflective planner** | Likes year-end reviews, "Wrapped"-style recaps | A beautiful year overview they can save or share |

First real users: friends, PMC and AKPsi members. Recruit 5-10 testers for the MVP.

## 3. Goals

1. Make daily logging feel like a calming, tactile ritual (hand-colouring), not data entry.
2. Let users track any mix of life areas with preset or custom colour scales.
3. Show the big picture: every tracker for the whole year in one view.
4. Connect the daily to-dos (reminders/check-ins) to the journal so logging happens naturally.
5. Ship a polished, deployed MVP that a recruiter can try in under 30 seconds with no sign-up.

## 4. Non-goals (for MVP)

- Accounts, cloud sync, or multi-device (planned for v1.1)
- Native mobile apps (responsive web + PWA later instead)
- Push notifications when the app is closed
- Social features, friends, or public profiles
- Medical advice or clinical mood scoring
- Long-form diary writing (short daily note only)

## 5. Core concepts

- **Journal** = one year. Shown as a *floppy disk* on the retro desktop ("2026.flp"). Each year is a new disk you can "eject" and keep.
- **Tracker** = one life area (Sleep, Water, Mood...). Shown as a *file* saved on that year's disk. Each tracker has its own grid (12 months x 31 days) and its own **legend** (ordered colour levels).
- **Entry** = one tracker on one day. Stores the chosen **level** (and optionally a raw number like 7.5 hours). The colour is *derived* from the level, never stored.
- **Mosaic** = the whole-year overview that combines every tracker.
- **Today** = the home window: today's check-ins and reminders, plus quick colouring for each tracker.

## 6. User stories

### Must have (MVP)
- As a new user, I can open the app and immediately see a sample journal so I understand what it does.
- As a user, I can create a journal for the current year.
- As a user, I can add trackers from a preset library (Sleep, Water, Mood, Exercise, Screen time, Study/Work, Budget, Chores, Meds, Steps).
- As a user, I can create a custom tracker with my own name, icon, levels, labels, and colours.
- As a user, I can colour in a day by scribbling over it with my mouse, trackpad, or finger, like a marker.
- As a user, I can also tap/click or use the keyboard to fill a day quickly (accessibility and speed).
- As a user, I can edit or clear a past day.
- As a user, I can view one tracker's full-year grid with its legend.
- As a user, I can view the Mosaic: all trackers for the year on one screen.
- As a user, I can set daily reminders/check-ins (e.g. "Take meds 9:00", "Drink water") and tick them off on the Today screen.
- As a user, ticking a check-in that is linked to a tracker colours that tracker's day automatically.
- As a user, I can switch between at least 2 colour themes for the app.
- As a user, my data stays saved on my device between visits, and I can export/import it as a backup file.

### Should have (v1.0 polish)
- Simple insights per tracker: % of days at each level, longest "good" streak, monthly trend.
- Short daily note attached to a day.
- Count-style check-ins (tap +1 glass of water; level updates automatically).
- Export the Mosaic as a PNG poster ("Year Wrapped").
- Sound effects (marker squeak, soft chime), with a mute toggle.

### Could have (stretch)
- Accounts + cloud sync (Supabase).
- Pixel avatar creator and a gentle "stats" profile (no punishment for missed days).
- Quilt view: each day is one tile split into sub-pixels, one per tracker.
- Correlation insights ("on days you slept 7+ hours, mood was 'happy' 2x as often").
- Draggable desktop windows; installable PWA; browser notifications.

## 7. Success metrics

- Recruiter test: a first-time visitor reaches a full-looking Mosaic in under 30 seconds.
- 5+ testers use it for 7 consecutive days.
- Logging a full day (all trackers) takes under 60 seconds.
- Lighthouse accessibility score 90+.

## 8. Design principles

1. **Calm over clever.** Soft motion, gentle sounds, no red alerts.
2. **No shame.** Missed days stay blank, not red. No streak-breaking penalties.
3. **The ritual matters.** Colouring should feel good enough that people want to do it.
4. **Your data is yours.** Local-first; nothing leaves the device in the MVP.
5. **Readable first, retro second.** Pixel fonts for display only; body text stays legible.

## 9. Open questions

- Mobile-first or desktop-first layout for the MVP?
- Add accounts in v1.1, or stay local-first?
