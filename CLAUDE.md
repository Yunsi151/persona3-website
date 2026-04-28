# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev          # Start Vite dev server
npm run build        # Production build
npm run build:watch  # Watch mode build (scripts/watch-build.mjs)
npm run lint         # Run ESLint
npm run preview      # Preview production build
```

There is no test suite configured.

## Assets

The project requires media assets (videos and images) that are not in the repository. They must be downloaded separately from the Google Drive link in README.md and placed in `src/assets/`. The `App.jsx` imports reference: `Mainn.mp4`, `main1.mp4`, `main2.mp4`, `main3.mp4`, plus various `.jpeg`/`.png` character/icon files.

## Architecture

This is a React 19 + Vite SPA styled as a Persona 3 (JRPG) UI. Routing is handled by `react-router-dom` v7, with animated page transitions via `framer-motion`.

**Routing (`src/App.jsx`):** Defines four routes wrapped in `PageTransition`:
- `/` → `MenuScreen` (main menu with looping background video + `P3Menu`)
- `/about` → `AboutMe`
- `/resume` → `ResumePage`
- `/socials` → `Socials`

**`PageTransition` (`src/PageTransition.jsx`):** Wraps every page in an `AnimatePresence` block. Each route gets a different animated overlay variant (`"about"`, `"resume"`, `"socials"`, or `"default"`). Variants are defined as separate components (`AboutTransition`, `SocialsTransition`, etc.) inside this file.

**Page components** (`AboutMe`, `Socials`, `ResumePage`): Each page is self-contained — it owns its own CSS via a `<style>` block injected directly into JSX, keyboard navigation via `useEffect`/`window.addEventListener`, and its own static data arrays (e.g. `ITEMS`, `ROLES`, `CHARS`). All pages use `navigate(-1)` / `Escape`/`Backspace` to go back.

**`P3Menu` (`src/P3Menu.jsx`):** The main menu component. Menu items are defined in the `ITEMS` array at the top of the file (id, label, page slug, font size, offsets, skew values). Keyboard navigation (↑↓ + Enter) and mouse hover both work. The `onNavigate` callback is passed from `App.jsx`.

**Styling approach:** No CSS modules or external component library. Global resets live in `src/index.css` and `src/App.css`. Each page component injects its own scoped styles via a `<style>` tag in JSX. Google Fonts (Anton, Bebas Neue, Barlow Condensed, Montserrat) are loaded via `@import` inside those `<style>` blocks.

**ESLint:** `no-unused-vars` is configured to ignore names matching `/^[A-Z_]/` (i.e. uppercase constants), so top-level data arrays like `CLIP_SHAPES` won't trigger the rule even if not directly referenced.
