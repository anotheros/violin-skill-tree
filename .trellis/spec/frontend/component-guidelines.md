# Component Guidelines

> How components are built in this project.

---

## Overview

<!--
Document your project's component conventions here.

Questions to answer:
- What component patterns do you use?
- How are props defined?
- How do you handle composition?
- What accessibility standards apply?
-->

The product is a single offline HTML page. It uses semantic HTML, inline CSS, and vanilla JavaScript.

Rendering functions own view markup. Stable element IDs and `data-*` attributes form the DOM contract for event wiring.

---

## Component Structure

<!-- Standard structure of a component file -->

- Keep the three top-level regions: `#sidebar`, the main content area, and `#detail`.
- Keep the three view containers: `#mapView`, `#libraryView`, and `#journalView`.
- Put view-specific templates in the existing `renderMap`, `renderLibrary`, `renderJournal`, and `renderDetail` functions.
- Use delegated `data-action` events for dynamically rendered controls.
- Keep layout-only state in DOM classes or small runtime variables. Do not add it to the saved progress object.

---

## Props Conventions

<!-- How props should be defined and typed -->

This project has no component props or framework state. Pass node IDs through `data-id` attributes and resolve nodes through the existing lookup functions.

---

## Styling Patterns

<!-- How styles are applied (CSS modules, styled-components, Tailwind, etc.) -->

- Keep styles in `index.html`.
- Reuse the existing CSS custom properties for colors, widths, and radii.
- Use the existing `1030px` and `730px` breakpoints for panel and mobile behavior.
- Use `min-width: 0`, flexible tracks, and controlled wrapping before adding horizontal scrolling.
- Keep the map viewport independently scrollable. Do not re-render it for a panel-only toggle.

---

## Accessibility

<!-- A11y requirements and patterns -->

- Give every icon-only button a readable `aria-label` and matching `title`.
- Use `aria-controls` and `aria-expanded` for panel toggles.
- Keep `aria-hidden` and `inert` synchronized with panel visibility.
- Preserve visible keyboard focus with the existing `:focus-visible` rule.
- Support Escape for temporary drawers and respect `prefers-reduced-motion`.

---

## Common Mistakes

<!-- Component-related mistakes your team has made -->

- Do not call `save`, `checkpoint`, or `render` for layout-only state changes.
- Do not leave focusable controls inside a closed drawer without setting `inert`.
- Do not assume a desktop grid state also describes a mobile drawer state. Sync both from the current viewport.
- Do not add fixed-width controls that can exceed the page viewport at `730px` or below.
