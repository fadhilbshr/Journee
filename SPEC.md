# Journaling App — SPEC.md

This is the working spec for the app. Reference this at the start of every Claude Code session so it has consistent context.

## Overview
A minimal daily journaling app for Android (Flutter, future iOS support). Core interaction: each day, log a one-line entry plus a mood/rating. Primary view is a calendar showing mood at a glance, not a scrolling feed.

## Tech Stack
- **Framework:** Flutter (Dart) — chosen for single-codebase iteration speed with Claude Code, hot reload, and future iOS portability.
- **Storage:** Drift (local SQLite) — fully on-device, no cloud, no auth.
- **Target platform:** Android, minSdk 29 (Android 10+). iOS support planned later — avoid Android-only plugins where a cross-platform equivalent exists.
- **Monetization:** None. Free app, no IAP, no ads.

## Data Model
**Entry**
- `id` — unique identifier
- `date` — the day this entry belongs to (one entry per day)
- `text` — short one-line note (plain text, no rich formatting in v1)
- `mood` — integer 1–5, each value mapped to a fixed emoji (e.g. 1=😢, 2=😕, 3=😐, 4=🙂, 5=😄). Stored as the integer; emoji is a display-layer mapping, not stored separately.
- `createdAt` — timestamp
- `updatedAt` — timestamp

No tags, photos, voice, or location fields in v1.

## Core Features (v1)
1. **Calendar view** (home screen) — month grid, each day shows the mood emoji/color if an entry exists for that day. No separate chronological list view.
2. **Add/edit entry** — tapping a day opens a simple form: one-line text + mood picker. Editing an existing entry's day reopens the same form pre-filled.
3. **Trends tab** — styled after the *Brain Age* results-screen convention: a large central number/score representing today's mood front and center, with a history trend graph (line/bar, mood value per day across the month) displayed below it.
4. **Streaks/stats** — current streak (consecutive days logged), and likely longest streak / total entries (implementation detail to refine later).
5. **Daily reminder notification** — local notification at a user-set time reminding them to log the day's entry.
6. **Settings tab** — reminder time configuration; space for future preferences (e.g. theme).

## Navigation
Bottom navigation bar, three tabs:
- **Calendar** (home/default tab)
- **Trends**
- **Settings**

## Design Direction
Minimal/clean aesthetic. Lots of whitespace, simple typography, mood conveyed primarily through emoji/color rather than decorative UI. No visual clutter competing with the calendar grid.

**Trends tab visual reference:** Nintendo DS *Brain Age*'s results-screen convention — a large, prominent central number (today's mood score) as the focal point, with a supporting history graph beneath it. Keep this in the same minimal visual language (no skeuomorphic decoration), just borrowing the "big number + graph" layout logic, not Brain Age's specific art style.

## Explicit Non-Goals
- No social or sharing features, ever.
- No cloud sync/backend in v1 (may revisit only if iOS + multi-device sync becomes a real need later).
- No rich text, photo, or voice attachments in v1.
- No monetization/IAP.

## Testing & CI
- Unit tests for the Drift data layer: CRUD operations, streak calculation logic.
- A small number of widget tests for calendar rendering (e.g., a day with an entry shows the correct emoji).
- GitHub Actions workflow running `flutter test` on every push. Kept lightweight — not aiming for high coverage, just a safety net for the data layer and core rendering logic.

## Release Plan
- Target: Google Play Store (public release).
- Requires: signed release build (keystore), privacy policy (relevant since the app stores personal mood/journal data — must be accurate even though data is local-only), and the Play Console Data Safety form filled out correctly.
- No closed/open testing track requirements decided yet — plan for at least a personal closed test before going to production.

## Open Items to Refine During Build
- Exact streak/stats definitions (what counts as "longest streak," whether stats reset or persist across gaps).
- Reminder notification permission handling (Android 13+ requires runtime notification permission — Claude Code should handle this explicitly).
- Empty states: what the Calendar/Trends tabs look like before any entries exist.
