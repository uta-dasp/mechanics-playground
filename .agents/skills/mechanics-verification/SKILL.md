---
name: mechanics-verification
description: Verifies structural mechanics calculations, beam finite-element formulations, sign conventions, units, boundary conditions, reactions, internal forces, stresses, and deflections against analytical solutions. Use when implementing, modifying, debugging, reviewing, or validating the engineering solver.
---

# Mechanics Verification

Treat numerical correctness as the highest priority.

This project contains engineering calculations. Never assume that a result is correct merely because the solver runs, produces smooth plots, or passes basic software tests.

## Core principles

Use deterministic mechanics calculations.

Do not use an LLM to generate numerical structural responses.

Check equations dimensionally whenever practical.

Maintain one explicit sign convention for:

- applied forces
- distributed loads
- applied moments
- reactions
- displacement
- rotation
- shear force
- bending moment
- stress

If a change affects one of these quantities, verify that all related calculations, API outputs, plots, labels, and documentation remain consistent.

## Euler-Bernoulli beam checks

For the initial beam formulation, confirm that the assumptions are appropriate:

- linear elasticity
- small displacement
- small rotation
- prismatic beam where required
- plane bending
- shear deformation neglected

For a two-node Euler-Bernoulli beam element with degrees of freedom:

[v1, theta1, v2, theta2]

verify that the element stiffness matrix is consistent with the chosen sign convention and coordinate system.

Do not change the formulation solely to make a failing test pass.

## Boundary conditions

For every structural model:

1. Identify constrained degrees of freedom.
2. Identify free degrees of freedom.
3. Check whether sufficient constraints exist to eliminate rigid-body motion.
4. Detect singular or unstable configurations.
5. Confirm that reaction forces are recovered from the original equilibrium equations.

Do not hide a singular stiffness matrix by adding arbitrary numerical stabilization unless that stabilization is physically and mathematically justified.

## Analytical verification cases

Maintain regression tests for at least the following cases.

### Cantilever with end load P

Expected magnitudes:

Fixed-end shear:

V = P

Fixed-end moment:

M = P L

Tip deflection:

delta = P L^3 / (3 E I)

### Simply supported beam with center point load P

Expected reaction magnitudes:

RA = RB = P / 2

Maximum moment magnitude:

Mmax = P L / 4

Midspan deflection magnitude:

delta = P L^3 / (48 E I)

### Simply supported beam with uniform load w

Reaction magnitudes:

RA = RB = w L / 2

Maximum moment magnitude:

Mmax = w L^2 / 8

Maximum deflection magnitude:

delta = 5 w L^4 / (384 E I)

### Cantilever with uniform load w

Fixed-end shear magnitude:

V = w L

Fixed-end moment magnitude:

M = w L^2 / 2

Tip deflection magnitude:

delta = w L^4 / (8 E I)

## Verification protocol

Whenever solver logic changes:

1. Run the complete analytical regression suite.
2. Compare numerical and analytical values.
3. Calculate relative error where meaningful.
4. Check reaction equilibrium.
5. Check force and moment equilibrium.
6. Inspect shear and moment sign behavior.
7. Inspect deflected shape.
8. Verify dimensions and units.
9. Report discrepancies explicitly.

For distributed finite-element approximations, study mesh convergence if the exact solution is not recovered directly.

## Equilibrium checks

Where applicable verify:

Sum of vertical forces = 0

Sum of moments = 0

Reaction forces + applied forces = equilibrium

Do not accept results that violate global equilibrium beyond justified numerical tolerance.

## Stress

For elementary linear bending:

sigma = M c / I

Verify:

- units
- sign convention
- correct outer-fiber distance c
- correct second moment of area I
- appropriate section orientation

Clearly distinguish bending stress from shear stress, von Mises stress, and other stress measures.

## Units

Calculations must use canonical SI units internally unless an architecture decision explicitly changes this.

Conversions belong in a dedicated unit module.

Never place unexplained conversion factors inside solver equations.

## Tolerances

Numerical tolerances must have documented justification.

If a regression test fails:

- investigate the formulation
- investigate units
- investigate load vector construction
- investigate boundary conditions
- investigate reaction recovery
- investigate discretization

Do not simply loosen the tolerance to make the test pass.

## Reporting

When completing verification, summarize:

- cases tested
- analytical values
- numerical values
- relative error
- equilibrium status
- any unresolved discrepancies

Engineering results should not be declared validated while material discrepancies remain unexplained.