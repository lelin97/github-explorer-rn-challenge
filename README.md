# GitHub Explorer (React Native)

> Technical challenge built for a job application process. Shared here as a code sample.

A mobile app built with **Expo** and **TypeScript** to search public GitHub repositories, open their details, and save favorites that persist on the device.

## The challenge

Build a React Native app that consumes the GitHub REST API with three flows: searching repositories, viewing a repository's details, and saving favorites locally. The evaluation focused on code organization, state management, navigation, and handling of loading, empty and error states.

## What was delivered

- **Repository search** with a debounced input and "load more" pagination
- **Repository details**: description, stats and topics
- **Favorites** saved locally and kept across app restarts
- Clear loading, empty and error states, including GitHub rate-limit errors

## Tech stack

Expo SDK 54 · React Native 0.81 · React 19 · TypeScript · React Navigation 7 (bottom tabs + native stack) · Zustand · AsyncStorage

## Getting started

**Requirements:** Node.js (LTS), npm or yarn, and **Expo Go** on a physical device or an iOS/Android emulator.

```bash
npm install
npx expo start
```

If your phone can't reach the bundler on your local network, use `npx expo start --tunnel`.

No GitHub token is needed for public read access. Without one, GitHub applies a strict rate limit, which the app reports clearly when it's reached.

## Architecture

- **`src/api`** — HTTP client plus `searchRepositories` and `getSingleRepository`, with typed results (`success` / `message`)
- **`src/screens`** — Search, detail and saved screens; components used by a single screen live next to it
- **`src/navigation`** — Bottom tabs (Search / Saved), the search stack, and the shared detail route
- **`src/store` + `src/storage`** — **Zustand** for in-memory favorites and **AsyncStorage** for persistence, hydrated on startup in `App.tsx`
- **`src/types`** — Models and unions aligned with the API and the UI

## Design decisions

- **Zustand over Context alone:** little boilerplate for a global favorites list, with simple selectors such as `useIsFavorite`.
- **Pagination without stale closures:** the result list is kept in a `useRef` across `await` calls, while the current page lives in `useState` to drive "load more".
- **Defensive storage reads:** favorites JSON is validated when parsed, so corrupted or legacy data can't crash the app.
- **No flicker on the favorite toggle:** the detail screen only shows it once favorites have loaded from disk, so the saved state always matches storage.
- **Deviation from the brief, on purpose:** the challenge suggested a detail screen inside the Search tab's stack only. Here, `RepositoryDetail` lives on the root stack next to the tabs, so both Search and Saved navigate to the same screen with `{ owner, repo }`. This avoids duplicating the screen in two stacks and keeps header, favorites and back navigation in one place.

## AI-assisted development

Built with help from **Cursor**, used for scaffolding, refactors, TypeScript typing and the React Navigation structure. I reviewed every part of the codebase and can walk through any of it.

## With more time, I would add

- Unit tests for favorites storage and helpers, plus API tests with mocks
- Skeleton loaders instead of a spinner-only state
- Infinite scroll with protection against out-of-order responses
- Local cache of the last search or detail for partial offline reading
- Optional GitHub token via environment variable for higher rate limits
