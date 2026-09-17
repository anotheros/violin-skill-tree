# Quality Guidelines

> Code quality standards for frontend development.

---

## Overview

<!--
Document your project's quality standards here.

Questions to answer:
- What patterns are forbidden?
- What linting rules do you enforce?
- What are your testing requirements?
- What code review standards apply?
-->

The project has no package manager, linter, type checker, or automated test runner. Quality checks use JavaScript syntax validation, `git diff --check`, and browser verification against the single page.

---

## Forbidden Patterns

<!-- Patterns that should never be used and why -->

- Do not change the saved progress schema for a layout feature.
- Do not add runtime network requests or third-party dependencies to the offline page.
- Do not use page-level fixed widths that create horizontal overflow.
- Do not duplicate event handlers for the same panel control.
- Do not remove existing code comments without an explicit requirement.

---

## Required Patterns

<!-- Patterns that must always be used -->

- Keep desktop panel state session-only unless the product explicitly requests persistence.
- Keep mobile navigation and detail panels as independent drawers with a dismissible backdrop where applicable.
- Update accessibility state whenever a responsive panel changes.
- Include reduced-motion rules for new transitions.
- Verify that layout toggles preserve the selected node, view, zoom, scroll position, and saved state.

### Map-only responsive state

Keep fullscreen, mobile branch, and map course filters in runtime variables.

Do not add these values to `state`, `save()`, import, export, undo, or checkpoint data.

```js
let mapFullscreen = false;
let mapLane = 'all';
let mapShowBooks = true;
let mapShowEtudes = true;
```

Render the map through one visibility predicate.

Keep `nodes` as the complete source for the library and learning logic.

### Saved node identity when splitting skills

Retain the original ID for the node that preserves the original assessment scope. For example, `pizzleft` remains the introductory skill; `pizzleftcombo` is a new assessment with no inherited completion.

Preserve checklist meanings and index order because `state.checks[id]` stores numeric indexes. Keep notes, historical log text, focus IDs, and user prerequisite overrides intact. Never strengthen the old node's prerequisites silently: `pruneInvalid()` could erase valid historical completion.

Verify old-save import and export roundtrips, unchanged growth points, readiness without automatic completion, and independent completion/rollback of the new node.

### Mobile map boundary

Phones start in a compact global overview. Core mode shows lanes 1, 2, and 3 as parallel vertical tracks with shared stage bands. Course mode shows a single lane (0 or 4).

Keep `mobileMapMode` session-only. Course filters control Suzuki and etude visibility, never library content or learning prerequisites.

`calculateCoreLayout(visibleNodes, width)` returns card dimensions with coordinates. Rendering, edges, and centering use these dimensions. Assert actual card bounds and three distinct lane positions; clipped overflow alone does not prove usability.

Overview uses 100% scale. Core mode caps scale at 100% to keep all tracks visible. Fullscreen must allow opening and closing node details.

Keep the map viewport vertically scrollable.

Set the mobile map sizer to the viewport width and clip transformed overflow.

Assert both document width and default map width in browser checks.

---

## Testing Requirements

<!-- What level of testing is expected -->

Run the following checks for responsive layout changes:

1. Extract the inline application script and compile it with `new Function(...)` or an equivalent syntax check.
2. Run `git diff --check`.
3. Open the page at `375×812`, `390×844`, `768×900`, `1280×800`, and `1440×900`.
4. Assert no document horizontal overflow.
5. Assert the default mobile `#viewport.scrollWidth` does not exceed its client width.
6. Test panel toggles, Escape, keyboard activation, all three views, search, filtering, zoom, and state preservation.
7. Test fullscreen `aria-pressed`, exit behavior, and node scale at desktop and mobile sizes.
8. Test reduced motion when a new transition is introduced.

---

## Code Review Checklist

<!-- What reviewers should check -->

- Confirm that the change stays within `index.html` unless a broader scope is required.
- Confirm that layout-only events do not call persistence or progress mutation functions.
- Confirm that closed panels are not keyboard-focusable.
- Confirm that each breakpoint has a usable layout and no page overflow.
- Confirm that existing view and learning actions still use their original data flow.
