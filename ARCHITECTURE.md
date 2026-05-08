# ARCHITECTURE.md — Mood Tracker App

## Overview

This is a **static, browser-only Mood Tracker App** designed for deployment on **GitHub Pages**. The app should let a user quickly record moods, review past entries, and see simple trends without any backend, account system, or external API.

The architecture should stay small, readable, and durable so GitHub Copilot can extend it safely.

## Architecture goals

- Keep the app **local-first** and **privacy-friendly**
- Make mood logging fast and low-friction
- Store and load data entirely in the browser
- Use a component structure that is easy to understand and modify
- Keep the build compatible with GitHub Pages
- Avoid unnecessary dependencies and complexity

## Recommended stack

Use this stack unless the repository already has a different one:

- **React**
- **TypeScript**
- **Vite**
- Optional: **Tailwind CSS** for styling
- Optional: a small chart library only if needed for simple trend visuals

## High-level system design

The app should be organized into four layers:

1. **UI layer**  
   React components for forms, lists, charts, filters, and settings.

2. **State layer**  
   React state, context, or a lightweight store for app data and UI state.

3. **Domain layer**  
   Pure utility functions for mood entry validation, sorting, filtering, summarizing, and date handling.

4. **Persistence layer**  
   Local browser storage read/write helpers, ideally in one dedicated module.

Keep these layers separated so UI components do not directly handle storage logic.

## Data flow

1. App starts.
2. Storage layer loads saved data from the browser.
3. Data is validated and normalized.
4. State is initialized with the loaded entries/settings.
5. User creates, edits, deletes, filters, or exports entries.
6. State changes are saved back to browser storage.

This flow should always work without a backend.

## Data model

Use a versioned schema so future changes are safe.

### MoodEntry

```ts
type MoodEntry = {
  id: string;
  date: string; // ISO date string, preferably local-date consistent
  mood: number; // example: 1-5
  label?: string;
  note?: string;
  tags: string[];
  createdAt: string;
  updatedAt: string;
};
```

### AppSettings

```ts
type AppSettings = {
  version: number;
  theme?: "light" | "dark" | "system";
  moodScale?: {
    min: number;
    max: number;
    labels?: Record<number, string>;
  };
};
```

### PersistedData

```ts
type PersistedData = {
  version: number;
  entries: MoodEntry[];
  settings: AppSettings;
};
```

## Storage architecture

Use browser storage as the only persistence mechanism.

### Preferred approach
- Start with **localStorage** for simplicity.
- If data size or complexity grows, migrate to **IndexedDB** only if necessary.

### Storage rules
- Use one clearly named key for app data, for example `mood-tracker-data`.
- Validate all loaded data before using it.
- Gracefully handle missing, empty, or corrupted storage.
- Never assume storage read/write will succeed.
- Wrap storage access in try/catch.
- Save after meaningful changes, not on every keystroke unless necessary.

### Storage responsibilities
A dedicated module such as `src/lib/storage.ts` should:
- load persisted data
- save persisted data
- export data as JSON-friendly structure
- import and validate data
- handle schema versioning and defaults

## Suggested folder structure

```text
src/
  components/
    MoodForm.tsx
    MoodList.tsx
    MoodCard.tsx
    MoodFilters.tsx
    MoodChart.tsx
    SettingsPanel.tsx
    ExportImportPanel.tsx
  hooks/
    useMoodEntries.ts
    useLocalStorage.ts
  lib/
    storage.ts
    moodMath.ts
    dates.ts
    validation.ts
    exportImport.ts
  types/
    mood.ts
  App.tsx
  main.tsx
```

This structure is only a guide. Keep files small and coherent.

## Core modules

### `storage.ts`
Owns browser persistence, versioning, and safe load/save behavior.

### `validation.ts`
Checks that imported or loaded data matches expected shape.

### `moodMath.ts`
Provides helpers for:
- sorting entries
- grouping by day/week/month
- calculating averages
- counting tags
- identifying trends

### `dates.ts`
Contains date formatting and normalization helpers.

### `exportImport.ts`
Converts app data to/from a portable JSON format.

## UI architecture

The UI should be simple and mobile-friendly.

### Main screens or sections
- **Quick Add**: the first thing users see
- **History**: list or calendar of entries
- **Insights**: charts and summaries
- **Settings**: appearance and storage controls

### Component responsibilities
- `MoodForm`: create and edit entries
- `MoodList`: show all entries
- `MoodCard`: display one entry
- `MoodFilters`: date, mood, tag filtering
- `MoodChart`: simple trend visualization
- `SettingsPanel`: preferences and reset controls
- `ExportImportPanel`: backup and restore data

Keep components presentational where possible. Put logic into hooks or utility functions.

## State management

Use the lightest state approach that works.

Recommended state pieces:
- `entries`
- `settings`
- `selectedEntry`
- `filters`
- `uiMode` such as add/edit view

Preferred patterns:
- React `useState` for local UI state
- React `useEffect` for initial load and persistence sync
- Context only if shared state becomes awkward
- Avoid heavy global state libraries unless absolutely needed

## Validation rules

Before saving or importing entries:
- require a valid `id`
- require a valid `date`
- ensure `mood` is within the supported range
- ensure `tags` is an array of strings
- ensure timestamps are valid ISO strings
- strip unknown fields when practical

Reject invalid import files with a clear, user-friendly message.

## Error handling

The app should fail safely.

Handle these cases:
- storage unavailable
- malformed JSON
- version mismatch
- bad imported file
- empty first run
- unsupported browser behavior

Show a calm and clear message instead of crashing.

## Accessibility requirements

- Use semantic HTML
- Provide labels for all inputs
- Make all controls keyboard accessible
- Avoid color-only meaning
- Keep contrast readable
- Ensure buttons are large enough on mobile

## Performance considerations

- Keep entry rendering efficient
- Avoid unnecessary re-renders
- Memoize only when there is a clear benefit
- Keep charts simple
- Do not block the UI during basic save/load operations

## GitHub Pages constraints

The final build must work as static files on GitHub Pages.

Important:
- no server-side rendering required
- no backend routes
- no runtime API dependencies
- use relative-safe asset paths
- if routing is used, prefer hash routing or a static-safe approach

## Suggested app behavior

### First launch
- show empty state
- invite the user to log their first mood
- explain that data is saved locally

### Normal use
- add mood quickly in a few clicks
- show the newest entries near the top
- allow editing and deleting with confirmation

### Insights
- show average mood over time
- show mood frequency
- show most common tags
- keep charts simple and readable

### Backup and restore
- export as JSON
- import from JSON
- validate before replacing or merging data
- allow user confirmation before destructive import actions

## Versioning strategy

Use a schema version number in persisted data.

When the schema changes:
- add a migration step
- preserve user data when possible
- keep old data readable if practical
- never silently drop entries unless explicitly required

## Quality bar

The code is ready when:
- mood entries persist across refreshes
- data loads correctly on startup
- editing and deleting work
- export and import work safely
- the app builds and runs on GitHub Pages
- the repository stays simple enough for Copilot to extend
