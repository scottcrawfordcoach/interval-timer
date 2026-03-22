# Crawford Coaching Interval Timer — Project Summary

## Overview

A **Progressive Web App (PWA)** for fitness interval timing, built with vanilla HTML/CSS/JavaScript. It supports configurable workouts with sets, timed intervals, rest periods, and colour-coded phases, with special support for **EMOM (Every Minute On the Minute)** workouts. The app is fully offline-capable, installable on mobile devices, and deployed as a static site on Vercel.

---

## Project Structure

| File | Role |
|---|---|
| `timer.html` | Single-page application — all UI, styling, and core logic |
| `EMOM_DB.js` | Exercise database (171 exercises with metadata) |
| `exercises.json` | JSON version of the exercise database |
| `exercises.csv` | CSV version for spreadsheet editing / external tools |
| `QUICK-EMOM-SPEC.md` | Feature spec for the Quick EMOM Workout generator |
| `manifest.json` | PWA manifest (name, icons, display mode) |
| `sw.js` | Service Worker — offline caching (network-first strategy) |
| `vercel.json` | Vercel deployment config (rewrites `/` → `timer.html`) |
| `scripts/sync-emom-data.js` | Regenerates `EMOM_DB.js` and `exercises.json` from `exercises.csv` |

---

## Workout Modes

1. **Quick EMOM Timer** — Preset EMOM structure where the user defines exercises, sets, and rest.
2. **Quick EMOM Workout** — Auto-generates a randomised EMOM workout from the exercise database, filtered by equipment, difficulty, and body part.
3. **Build a Custom Timer** — Full manual control over sets, intervals, durations, and colour coding.
4. **My Saved Workouts** — Load and re-run previously saved workout configurations.

---

## Exercise Database

171 curated exercises across:

- **Equipment**: Bodyweight, Kettlebell, Dumbbell, Resistance Bands, TRX, Stability Ball, Slam Ball, Step, Sturdy Chair
- **Difficulty**: Easy, Intermediate, Advanced
- **Focus**: Upper, Lower, Core, Full Body
- **Fields per exercise**: equipment, secondary equipment, name, difficulty, body part, recommended reps, coaching instructions

The database is maintained in three parallel formats (JS, JSON, CSV) for flexibility.

---

## Quick EMOM Generation Algorithm

- Solves for valid (exercises, sets) pairs fitting the user's chosen duration using the formula:  
  `totalMinutes = (exercises × sets) + (sets − 1) × 1`
- Prefers more exercises (4–6 range) for variety, with 3–5 sets.
- Selects exercises with **body-part diversity** via round-robin.
- Supports re-rolling for a new random selection.
- Displays a disclaimer/waiver modal before the first workout each session.

---

## Key Capabilities

- **Countdown timer** with large display, dynamic labels, and exercise/rep callouts
- **Colour-coded phases** — per-interval or per-set colour assignment from a palette
- **Audio cues** — beeps and notifications to mark interval transitions
- **Save & load** — workouts persisted to `localStorage` as JSON
- **Offline support** — Service Worker pre-caches core assets; network-first with cache fallback
- **Installable PWA** — standalone display mode, themed splash screen, home-screen icon
- **Responsive & touch-optimised** — mobile-first layout, `clamp()` typography, 44px touch targets, safe-area insets for notched devices
- **Dark theme** — CSS custom properties (`--bg: #0b1020`, `--accent: #66a3ff`)

---

## Technology Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla JS, CSS Grid/Flexbox, CSS custom properties |
| Data | Embedded JSON array, browser `localStorage` |
| Offline | Service Worker (cache version `v8`) |
| PWA | Web App Manifest, standalone display |
| Hosting | Vercel (static site) |
| Backend | None — fully client-side |

---

## Data Maintenance

Run this command after editing `exercises.csv`:

`node scripts/sync-emom-data.js`

It rebuilds both runtime exercise data files:

- `EMOM_DB.js`
- `exercises.json`

---

## Architecture

The entire application is a **single HTML file** with embedded CSS and JavaScript — no build tools, bundlers, or frameworks. The exercise database is loaded as a separate JS file. A Service Worker provides offline access, and Vercel handles deployment with a single rewrite rule pointing `/` to `timer.html`.
