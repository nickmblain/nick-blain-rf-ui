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
    pages/
      GuidePage.svelte       the "Event setup guide" content (Steps 1-3)
      ExhibitorsPage.svelte  Exhibitors list, search, and add-exhibitor UI
      ComingSoonPage.svelte  placeholder used for nav items with no design
    Sidebar.svelte    left navigation (icon rail + nav panel, collapses to a drawer on mobile)
    MainContent.svelte  shared shell (event header, top bar) that swaps in the active page
  styles/
    _variables.scss  colors, spacing, breakpoints
    _reset.scss      base element reset
    main.scss        global styles entry point
  App.svelte
  main.js
```

## Responsive behavior

The layout is full-width on desktop. Below the tablet breakpoint, the sidebar collapses into a slide-in drawer triggered by a hamburger button, and card grids stack to a single column down to a 320px viewport.

## Extra functionality

Beyond the static mockup, the sidebar navigation is fully wired up:

- **Working navigation** — every nav item and sub-item (Guide, Attendees and its children, Content, Exhibitors) is a real, clickable control that switches the active page in the main content area and updates the active/highlighted state in the sidebar.
- **Anchor links to real sections** — where a sub-item corresponds to an actual section of the page, clicking it also smooth-scrolls straight to that section (e.g. "Attendees" jumps to the Attendee module, "Attendee types" jumps to the Step 1 card that defines attendee types). Sub-items with no matching section on the page just navigate normally.
- **Sidebar search** — typing in the sidebar's search box filters the nav list live, matching against both top-level items and their children (a matching child keeps its parent group visible even if the parent's label doesn't match).
- **A built-out Exhibitors page** — since only the Attendees/"Event setup guide" screen was provided in the design, the Exhibitors page was designed from scratch using the same visual language (colors, spacing, card and typography styles) as the rest of the app. It includes a sample exhibitor list, its own search-to-filter box, and an "Add Exhibitor" action.
- The "Content" nav item routes to a small placeholder page, since no design was provided for it either.
