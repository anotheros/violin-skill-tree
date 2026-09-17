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

---

## Testing Requirements

<!-- What level of testing is expected -->

Run the following checks for responsive layout changes:

1. Extract the inline application script and compile it with `new Function(...)` or an equivalent syntax check.
2. Run `git diff --check`.
3. Open the page at `375×812`, `390×844`, `768×900`, `1280×800`, and `1440×900`.
4. Assert no document horizontal overflow.
5. Test panel toggles, Escape, keyboard activation, all three views, search, filtering, zoom, and state preservation.
6. Test reduced motion when a new transition is introduced.

---

## Code Review Checklist

<!-- What reviewers should check -->

- Confirm that the change stays within `index.html` unless a broader scope is required.
- Confirm that layout-only events do not call persistence or progress mutation functions.
- Confirm that closed panels are not keyboard-focusable.
- Confirm that each breakpoint has a usable layout and no page overflow.
- Confirm that existing view and learning actions still use their original data flow.
