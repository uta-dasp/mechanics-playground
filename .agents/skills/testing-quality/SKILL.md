---
name: testing-quality
description: Designs and executes automated tests for the mechanics playground, including Python unit tests, numerical regression tests, API tests, TypeScript tests, integration tests, and browser end-to-end tests. Use whenever implementing, modifying, debugging, or reviewing application behavior.
---

# Testing Quality

Use testing as part of development rather than as a final cleanup activity.

Every meaningful feature should have automated verification at the lowest appropriate level.

## Testing layers

Use several complementary layers.

### Numerical unit tests

Test individual mechanics functions such as:

- element stiffness matrices
- section-property calculations
- equivalent nodal load vectors
- coordinate handling
- unit conversions
- interpolation
- stress calculations

### Solver regression tests

Compare structural solver outputs with known analytical solutions.

These tests are critical and must not be replaced with snapshots.

### API tests

Verify:

- request validation
- response schemas
- correct numerical results
- invalid input handling
- useful error responses

### Frontend tests

Verify important component behavior including:

- input validation
- support editing
- load editing
- unit selection
- examples
- results display
- error states

### End-to-end tests

Test important workflows using the real frontend and backend.

At minimum maintain a workflow that:

1. launches the application
2. loads a known example
3. runs the analysis
4. verifies that results appear
5. verifies expected reaction values
6. verifies that shear, moment, and deflection plots render

## Test design

Prefer deterministic tests.

Avoid excessive snapshot testing.

Use engineering assertions for numerical quantities.

Use explicit tolerances for floating-point comparisons.

Document tolerance rationale where the choice is not obvious.

Test both valid and invalid inputs.

## Regression policy

When fixing a bug:

1. reproduce the bug
2. create a failing regression test
3. implement the fix
4. confirm that the new test passes
5. run related tests
6. run the full relevant test suite

Do not remove useful tests merely because they expose a defect.

## Before important commits

Run the applicable checks for the modified area.

For backend changes, normally run:

- Python tests
- formatting checks
- linting
- type checks if configured

For frontend changes, normally run:

- TypeScript type checking
- frontend tests
- linting
- production build

For cross-stack changes, run integration or end-to-end tests.

## CI

Continuous integration should execute the important automated checks on pushes and pull requests.

A failing critical numerical validation test should block release readiness.

## Reporting

When reporting test status, distinguish clearly among:

- tests passed
- tests failed
- tests not run
- tests unavailable
- manually inspected behavior

Never claim functionality is verified when only compilation was checked.