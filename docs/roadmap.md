# Project Roadmap

This document outlines completed capabilities, planned enhancements, and deferred long-term goals for the Interactive Mechanics Playground.

---

## 1. Version 1.0 (Current Release Scope)

- [x] Deterministic 1D Euler-Bernoulli beam bending solver.
- [x] Adaptive 1D Hermite cubic finite element discretization.
- [x] Exact reaction recovery from partitioned equilibrium equations.
- [x] Global equilibrium verification ($\sum F_y = 0$, $\sum M = 0$).
- [x] Rectangular, solid circular, and custom section moment of inertia calculators.
- [x] Fixed, pin, and roller supports.
- [x] Point forces, uniform distributed loads, and concentrated moments.
- [x] Automated analytical validation against classical benchmarks (Cases A, B, C, D) with relative error $< 10^{-5}$.
- [x] FastAPI REST service with Pydantic request/response schemas.
- [x] Interactive React/TypeScript frontend with SVG beam schematic and engineering charts ($V, M, v$).
- [x] Built-in analytical benchmark examples with 1-click loading.
- [x] Comprehensive technical documentation and GitHub Actions CI.

---

## 2. Version 1.1 (Planned Enhancements)

- [ ] Linearly varying / triangular distributed loads ($w_1 \to w_2$).
- [ ] Internal hinges (moment release degrees of freedom).
- [ ] Intermediate settlement / prescribed non-zero support displacements.
- [ ] Additional standard cross-sections (I-beam / wide flange, T-section, hollow rectangular tube, pipe).
- [ ] Shear stress distribution across section depth $\tau(y) = \frac{V Q}{I b}$.
- [ ] Drag-and-drop support and load positioning on the SVG schematic.
- [ ] PDF / PNG report export of calculation results and diagrams.

---

## 3. Version 2.0 (Structural Extensions - Long Term)

- [ ] **Timoshenko Beam Theory**: Incorporate transverse shear deformation for deep beams and sandwich structures.
- [ ] **Non-Prismatic Beams**: Stepped cross-sections and linearly tapered beams.
- [ ] **Axial-Flexural Coupling (Beam-Columns)**: Axial loads, buckling analysis (Euler critical load $P_{cr}$), and second-order $P-\Delta$ effects.
- [ ] **2D Plane Frame Analysis**: 2D frames with inclined members and joint rigidity.

---

## 4. Explicitly Deferred Scope

- **AI Explanation Engine**: Future conversational explanations of deterministic solver outputs (e.g., "Why is maximum moment located at $x=3\,\text{m}$?"). Strictly deferred until deterministic solver features and UI are stable. Language models will never compute structural mechanics responses.
- **Personal Website Integration**: Embedding the playground into the personal website's "AI Playground" section is deferred and maintained in an isolated repository.
