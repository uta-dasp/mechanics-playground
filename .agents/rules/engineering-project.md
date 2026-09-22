# Engineering Project Rules

This repository contains engineering analysis software.

Engineering correctness has priority over UI convenience and development speed.

## Solver authority

All structural calculations must come from deterministic numerical or analytical code.

Do not use language models to calculate:

- reactions
- shear
- bending moment
- displacement
- rotation
- stress
- section properties

Language models may later explain solver results but may not replace the solver.

## Units

Use SI units internally.

All external unit choices must be converted through a dedicated unit-conversion layer.

Do not insert unexplained conversion constants into mechanics equations.

## Verification

Every important solver capability must have analytical or otherwise independently established validation.

A successful run is not proof of correctness.

If numerical results disagree with analytical validation:

1. investigate the formulation
2. inspect units
3. inspect boundary conditions
4. inspect loading
5. inspect sign conventions
6. inspect discretization
7. inspect result recovery

Do not simply loosen test tolerances.

## Architecture

Keep structural mechanics logic independent from:

- FastAPI routing
- frontend code
- plotting
- deployment infrastructure
- future AI functionality

The core mechanics package should be directly importable and testable.

## Sign conventions

Maintain one documented sign convention.

Do not silently change it.

Changes affecting conventions must be reflected consistently in:

- solver
- tests
- API
- frontend
- diagrams
- labels
- documentation

## Code quality

Prefer modular, typed, testable code.

Avoid monolithic files.

Use descriptive engineering variable names.

Do not leave unexplained magic numbers.

## Documentation

Update relevant documentation when:

- equations change
- assumptions change
- interfaces change
- architecture changes
- deployment changes
- validation changes

Do not document planned functionality as completed functionality.

## Git

Use incremental, meaningful commits.

Do not commit secrets.

Do not rewrite remote history without explicit permission.

## Repository isolation

This repository is the standalone Interactive Mechanics Playground.

A separate personal website repository exists.

Do not open, clone, modify, commit to, or otherwise change the personal website repository unless explicitly instructed to begin website integration.

The future "AI Playground" webpage is deferred scope.