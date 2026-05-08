# AGENTS.md — Mood Tracker App

## Purpose

Build a lightweight, privacy-first **Mood Tracker App** that runs entirely in the browser and is deployable on **GitHub Pages**. The app should help a user quickly log moods, view patterns over time, and manage their own data without a backend.

## Core product goals

- Make it fast to log a mood in a few seconds.
- Store all user data **in the browser**.
- Work well on mobile and desktop.
- Be pleasant, calm, and easy to understand.
- Avoid unnecessary complexity.

## Hard requirements

- **Static-only app**: must work on GitHub Pages.
- **No backend**: do not require servers, databases, logins, or external APIs.
- **Browser persistence**: save and load all data locally in the browser.
- Prefer `localStorage` for simplicity unless the app truly needs `IndexedDB`.
- Do not lose data on refresh or page close.
- Do not use paid services or hidden network dependencies.
- Keep setup simple enough for GitHub Copilot to complete with minimal guidance.

## Suggested tech stack

Use a modern frontend stack that fits static hosting well:

- **React + Vite + TypeScript** preferred
- Tailwind CSS optional if it speeds up clean UI work
- Use small, readable components
- Keep dependencies minimal

If the project already uses another stack, continue with it rather than rewriting everything.

## App features

### Required
- Add a mood entry with:
  - date
  - mood rating or emoji
  - optional note
  - optional tags
- View a list or calendar of past entries
- Edit and delete entries
- Show basic trends, such as:
  - mood over time
  - mood frequency
  - most common tags
- Save everything locally in the browser
- Load existing data on startup

### Nice to have
- Search and filter by date, mood, or tag
- Streaks or weekly summaries
- Dark mode
- Export/import data as JSON
- Simple charts
- Reminder notifications only if they do not require a backend

## Data model

Use a clean, explicit schema. Keep it easy to export/import.

Example entry shape:

```ts
type MoodEntry = {
  id: string;
  date: string; // ISO date string, local date is fine if handled consistently
  mood: number; // for example 1-5, or use a fixed set of values
  label?: string; // optional human-readable mood label
  note?: string;
  tags: string[];
  createdAt: string;
  updatedAt: string;
};
```

If using emoji or named moods instead of numbers, keep the mapping centralized and consistent.

## Browser storage rules

- Persist all entries in a single local storage key or a small set of clearly named keys.
- Handle missing or corrupted data gracefully.
- Always validate data before use.
- Never crash if storage is empty.
- Keep storage format versioned if possible.
- Provide export/import so users can back up their data.
- When importing, validate and merge carefully.

Recommended storage key example:

- `mood-tracker-data`
- `mood-tracker-settings`

## UX principles

- Calm, supportive tone.
- Minimal clutter.
- Large tap targets for mobile.
- Quick entry flow should be the first thing users see.
- Use simple visual patterns and gentle colors.
- Make destructive actions obvious and confirm deletion.

## Pages or views

A simple single-page app is enough, but the app may include sections like:

- Dashboard
- Log Mood
- History
- Insights
- Settings

Keep navigation lightweight.

## Component guidance

Prefer small components with one job each, such as:

- `MoodForm`
- `MoodList`
- `MoodCard`
- `MoodChart`
- `FilterBar`
- `SettingsPanel`
- `StorageManager`

Keep state logic separate from presentational UI when practical.

## State management

- Use React state, context, or a small store if needed.
- Do not add heavy state libraries unless the app becomes complex.
- Keep persistence logic isolated in a dedicated utility or hook.
- Update storage whenever entries/settings change.

## Error handling

- Catch storage read/write failures.
- Show a friendly message if browser storage is unavailable.
- Prevent invalid dates or malformed entries.
- Fail safely if JSON import is wrong.

## Accessibility

- Use semantic HTML.
- Ensure keyboard navigation works.
- Provide labels for inputs.
- Maintain sufficient contrast.
- Support screen readers where practical.
- Do not rely on color alone to express mood.

## Performance

- The app should feel instant.
- Avoid unnecessary re-renders.
- Memoize only when useful.
- Keep charts and list rendering efficient.
- Since the app is local-first, optimize for responsiveness.

## GitHub Pages deployment

- The final app must build as static files.
- Use relative-safe asset paths compatible with GitHub Pages.
- Ensure routing works without a server.
- If using client-side routing, prefer hash routing or a static-friendly approach.
- Include build instructions in the README.

## File and project conventions

- Use clear, descriptive names.
- Keep files small and organized.
- Prefer `src/components`, `src/hooks`, `src/lib`, and `src/types`.
- Put storage helpers in one place.
- Put mood constants in one place.
- Keep styling consistent across the app.

Example structure:

```text
src/
  components/
  hooks/
  lib/
  types/
  assets/
  App.tsx
  main.tsx
```

## Coding style

- Write readable, direct code.
- Prefer simple logic over clever abstractions.
- Add comments only where the code would be confusing without them.
- Keep functions focused.
- Avoid magic numbers where a named constant would be clearer.
- Prefer TypeScript types for all app data.

## Testing expectations

If tests are added:

- Cover storage load/save behavior
- Cover entry validation
- Cover import/export logic
- Cover core UI interactions
- Cover any date-based calculations

At minimum, make sure the app can be manually verified by:
- refreshing the page after saving data
- closing and reopening the browser
- exporting and importing data
- editing and deleting an entry

## What Copilot should optimize for

When generating code, prefer solutions that are:

- static-host friendly
- browser-only
- easy to maintain
- simple to understand
- reliable across refreshes
- friendly for future feature additions

## What to avoid

- Do not add a backend.
- Do not require user accounts.
- Do not depend on cloud sync.
- Do not overcomplicate the UI.
- Do not store sensitive information unless the user explicitly adds it.
- Do not assume browser storage is always available without checking.
- Do not introduce unnecessary packages.

## Implementation priority order

1. App shell and navigation
2. Mood entry form
3. Local browser persistence
4. History list
5. Editing and deleting
6. Insights and charts
7. Export/import
8. Polish and accessibility

## Definition of done

The app is ready when:

- a user can log a mood entry
- entries persist after refresh
- the history view works
- the user can edit and delete entries
- the app builds successfully for GitHub Pages
- the app does not require a backend
- the code is clean enough for future Copilot-assisted changes
