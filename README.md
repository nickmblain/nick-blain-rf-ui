# RainFocus UI Challenge

A single-page recreation of the provided RainFocus event dashboard mockup, built with Svelte + Vite and hand-written SCSS (no CSS frameworks).

## Stack

- [Svelte 5](https://svelte.dev/) + [Vite](https://vitejs.dev/)
- SCSS (compiled via `sass`), authored with BEM class naming
- No component/styling libraries (no Bootstrap, Tailwind, etc.)

## Running the project

Requires [Node.js](https://nodejs.org/) 22+.

```bash
npm install
npm run dev
```

Then open the printed local URL (defaults to http://localhost:5173).

## Building for production

```bash
npm run build
```

This outputs a static site to the `build/` folder. It's fully self-contained — open `build/index.html` directly in a browser (no server required) to view the finished app.

To preview the production build locally instead of opening the file directly:

```bash
npm run preview
```

## Project structure

```
src/
  assets/           logo images used in the UI
  lib/
    icons/           small inline SVG icon components
    Sidebar.svelte    left navigation (icon rail + nav panel, collapses to a drawer on mobile)
    MainContent.svelte  event header + "Event setup guide" content
  styles/
    _variables.scss  colors, spacing, breakpoints
    _reset.scss      base element reset
    main.scss        global styles entry point
  App.svelte
  main.js
```

## Responsive behavior

The layout is full-width on desktop. Below the tablet breakpoint, the sidebar collapses into a slide-in drawer triggered by a hamburger button, and card grids stack to a single column down to a 320px viewport.
