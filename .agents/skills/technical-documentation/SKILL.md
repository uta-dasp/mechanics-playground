---
name: technical-documentation
description: Creates and maintains technical documentation for the mechanics playground, including architecture, structural mechanics theory, numerical methods, validation, testing, API usage, deployment, design decisions, roadmap, and setup instructions. Use whenever behavior, architecture, equations, interfaces, deployment, or project scope changes.
---

# Technical Documentation

Documentation is part of the implementation.

Keep documentation synchronized with the actual codebase.

Never document planned behavior as though it already exists.

## Required documentation

Maintain:

README.md

docs/architecture.md
docs/mechanics-theory.md
docs/numerical-method.md
docs/sign-convention.md
docs/validation.md
docs/testing.md
docs/api.md
docs/deployment.md
docs/roadmap.md
docs/ai-playground-integration.md

Create Architecture Decision Records when a significant architectural decision needs a durable rationale.

## README

The README should allow a new developer to understand and run the project.

Keep it current with:

- project purpose
- current capabilities
- limitations
- architecture summary
- technology stack
- prerequisites
- installation
- backend startup
- frontend startup
- tests
- repository structure
- documentation links
- current project maturity

Add screenshots when the UI becomes sufficiently stable.

## Mechanics documentation

Document equations and assumptions sufficiently for an engineer to audit the implementation.

Include:

- theory assumptions
- degrees of freedom
- element formulation
- stiffness matrix
- load vectors
- boundary conditions
- reaction recovery
- diagram conventions
- stress calculation
- limitations

Use mathematical notation clearly.

## Sign convention

Maintain one dedicated sign-convention document.

Include diagrams or clear descriptions of:

- positive vertical load
- positive displacement
- positive rotation
- positive shear
- positive bending moment
- positive applied moment

When conventions change, update solver logic, frontend graphics, plots, tests, and documentation together.

## Validation

For each analytical validation problem record:

- problem definition
- analytical equation
- analytical value
- numerical value
- absolute error
- relative error
- discretization used
- tolerance
- pass/fail status

Do not hide failed validation cases.

## Architecture

Document major components and their responsibilities.

Explain boundaries among:

- frontend
- API
- mechanics domain code
- numerical solver
- unit conversion
- persistence if later introduced
- AI explanation layer if later introduced

The deterministic mechanics solver must remain independent from any future language-model feature.

## API documentation

Document request and response structures and units.

Keep examples synchronized with actual schemas.

## Deployment

Document:

- environment variables
- frontend API configuration
- backend settings
- CORS configuration
- development startup
- production build
- deployment assumptions

Never place credentials in documentation.

## Roadmap

Separate:

- implemented
- planned
- experimental
- deferred

The future personal website integration belongs in the roadmap until explicitly started.

## Documentation review

After significant implementation work:

1. determine which documents became outdated
2. update them
3. verify commands and file paths
4. verify equations
5. verify examples
6. confirm planned features are not presented as completed features