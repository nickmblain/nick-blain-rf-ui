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

This outputs a static site to the `build/` folder. It's fully self-contained: open `build/index.html` directly in a browser (no server required) to view the finished app.

The build uses [`vite-plugin-singlefile`](https://github.com/richardtallent/vite-plugin-singlefile) to inline all JS, CSS, and images into `index.html` itself. This is needed because opening a normal Vite build's `index.html` straight from disk (`file://`) fails in Chromium-based browsers: they block the `<script type="module" src="...">` tag Vite generates by default from fetching its module over the `file://` protocol (a CORS restriction), leaving a blank page. Inlining everything into one file avoids that fetch entirely.

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
    sections/
      GuideSection.svelte       the "Event setup guide" section (Steps 1-3)
      ExhibitorsSection.svelte  Exhibitors section: list, search, add-exhibitor modal
      ComingSoonSection.svelte  placeholder used for the nav item with no design
    Modal.svelte      reusable modal (backdrop, Escape-to-close, focus-friendly)
    Sidebar.svelte    left navigation (icon rail + sticky nav panel, collapses to a drawer on mobile)
    MainContent.svelte  event header, top bar, and all sections stacked in order on one page
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

This is a true single-page app: Guide, Content, and Exhibitors are all sections stacked on the one page (not separate routes/views), and the sidebar nav is just a set of anchor links into that page.

- **Working navigation**: every nav item and sub-item (Guide, Attendees and its children, Content, Exhibitors) is a real, clickable control that smooth-scrolls the page to the corresponding section.
- **Scroll-spy nav highlighting**: the sidebar's active item updates automatically as you scroll, based on which section is actually in view (via `IntersectionObserver`), not just on click. Guide is the default active item at the top of the page.
- **Anchor links to the right spot within a section**: sub-items that correspond to a more specific spot scroll straight there (e.g. "Attendees" scrolls to the top of the Attendee module, "Attendee types" scrolls to the Step 1 card that defines attendee types). Sub-items with no matching spot on the page just scroll to the section itself.
- **Sidebar search**: typing in the sidebar's search box filters the nav list live, matching against both top-level items and their children (a matching child keeps its parent group visible even if the parent's label doesn't match).
- **Sticky sidebar**: the icon rail and nav panel stay pinned in the viewport as the page scrolls, so navigation is always reachable.
- **A built-out Exhibitors section**: since only the Attendees/"Event setup guide" screen was provided in the design, this section was designed from scratch using the same visual language (colors, spacing, card and typography styles) as the rest of the app. It includes a sample exhibitor list with its own search-to-filter box.
- **Add Exhibitor modal, persisted locally**: the "Add Exhibitor" button opens a modal form (name, category, booth, status). Submitting it adds the exhibitor to the list and saves it to the browser's `localStorage`, so it's still there on your next visit, no backend involved.
- **Sticky compact edit button (desktop)**: the "Edit event" button lives next to the event title at the top of the page. Once you scroll far enough that it leaves view, a small icon-only edit button pops into the sidebar next to the event name (with a little scale-in animation), so the action stays reachable while scrolling.
- **Fixed compact header (mobile)**: below the tablet breakpoint, the top bar stays fixed to the top of the viewport as you scroll. Once the full event header (logo, title, date, location) scrolls out of view, a shrunken version of it (small logo, title, date/location, and the edit icon) fades into the top bar so that context and the edit action stay reachable.
- The "Content" nav item scrolls to a small placeholder section, since no design was provided for it either.
