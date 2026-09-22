---
name: browser-ui-verification
description: Launches and verifies the mechanics playground in a real browser, checking layout, interactive beam controls, plots, forms, responsive behavior, API integration, browser console errors, and end-to-end user workflows. Use after frontend changes or when debugging user-facing behavior.
---

# Browser UI Verification

Do not consider frontend behavior verified solely because:

- TypeScript compiles
- unit tests pass
- the production build succeeds
- source code appears correct

Run the application and inspect actual browser behavior.

## Startup verification

Before browser testing:

1. start the backend
2. confirm the backend health or API endpoint responds
3. start the frontend
4. confirm the correct frontend URL
5. open the application in the browser

Record startup errors rather than bypassing them.

## Primary workflow

Verify the user can:

1. load the application
2. define or load a beam
3. define supports
4. define loads
5. define material and section properties
6. run analysis
7. view reactions
8. view shear diagram
9. view bending moment diagram
10. view deflection
11. view stress information

## Built-in example verification

Test each built-in example.

Compare displayed values with known expected results where practical.

At minimum inspect:

- cantilever with tip load
- simply supported beam with center point load
- simply supported beam with distributed load
- cantilever with distributed load

## UI inspection

Check:

- overlapping components
- clipped text
- unreadable labels
- awkward whitespace
- inconsistent spacing
- charts extending outside containers
- incorrect units
- missing legends
- missing axes
- incorrect load arrows
- incorrect support symbols
- poor error messages
- disabled controls that appear enabled
- broken loading indicators

## Engineering visualization checks

Confirm graphical direction agrees with numerical sign conventions.

Check that:

- downward loads appear downward if defined that way
- reaction arrows correspond to returned signs
- moment symbols reflect the chosen moment convention
- diagrams use correct units
- zero lines are visible
- extrema are identifiable
- plotted deflection corresponds to numerical displacement values

## Browser diagnostics

Inspect:

- browser console
- network failures
- API responses
- uncaught exceptions
- React warnings where relevant
- failed resource loads

Do not ignore repeated console errors.

## Responsive behavior

Inspect at least:

- normal desktop width
- laptop-width viewport
- narrower layout

The initial release does not need to be optimized for every mobile device, but the interface must remain usable and must not catastrophically break.

## Screenshots

Capture useful screenshots when:

- evaluating major UI changes
- documenting releases
- comparing visual regressions
- preparing README documentation

Avoid presenting screenshots as proof of numerical correctness.

## Completion report

When browser verification finishes, report:

- workflows tested
- browsers or browser environment used
- UI issues found
- console errors found
- issues fixed
- unresolved issues