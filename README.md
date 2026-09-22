# Interactive Mechanics Playground

A production-quality browser-based engineering application for linear elastic Euler-Bernoulli beam bending analysis, interactive visualization, and rigorous numerical validation.

[![CI](https://github.com/placeholder/mechanics-playground/actions/workflows/ci.yml/badge.svg)](https://github.com/placeholder/mechanics-playground/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![React 19](https://img.shields.io/badge/React-19-61dafb.svg)](https://react.dev/)

---

## 1. Project Purpose

The **Interactive Mechanics Playground** is an open-source, deterministic engineering tool designed for students, educators, and practicing structural and mechanical engineers. It allows users to visually configure beams, specify cross-section geometries, define support conditions and loadings, and immediately inspect:
- Support reactions ($R_y, M_R$) and global equilibrium verification
- Shear force diagrams $V(x)$
- Bending moment diagrams $M(x)$
- Elastic deflection curves $v(x)$
- Extreme bending stress $\sigma_{\max} = \frac{|M| c}{I}$ and surface distribution
- Exact numerical extrema and their beam locations

Calculations are strictly driven by deterministic numerical finite element code. Language models are never used to compute structural mechanics responses.

---

## 2. Capabilities & Limitations (Version 1.0)

### Current Capabilities (v1.0.0)
- **Governing Theory**: 1D linear elastic Euler-Bernoulli beam theory with small deflections and rotations.
- **Cross-Section Definitions**:
  - Solid rectangular section (inputs: width $b$, height $h \implies I = \frac{bh^3}{12}$, $c = \frac{h}{2}$)
  - Solid circular section (input: diameter $d \implies I = \frac{\pi d^4}{64}$, $c = \frac{d}{2}$)
  - Custom section (direct input of $I$ and outer-fiber distance $c$)
- **Support Types**:
  - Fixed support (constrains $v=0, \theta=0$)
  - Pin support (constrains $v=0$)
  - Roller support (constrains $v=0$)
- **Load Types**:
  - Concentrated vertical point loads ($F_y$)
  - Uniformly distributed loads ($w$) over arbitrary intervals $[x_1, x_2]$
  - Concentrated applied moments ($M_z$)
- **Adaptive Discretization**: Mesh automatically refines around discontinuities, support boundaries, and load application points.
- **Analytical Benchmark Suite**: Automated regression validation against classical closed-form benchmarks (cantilevers, simply supported beams with point and uniform loads).
- **Unit System**: Internal computations use canonical SI units, with convenient unit selection in the UI (metric and US customary units).

### Limitations
- Prismatic beam sections only (cross-section properties $E, I, A, c$ constant along the span).
- Transverse shear deformation is neglected (Euler-Bernoulli, not Timoshenko beam theory).
- Linear elastic material behavior only (no plasticity, yielding, or fracture).
- Small deflections only (no $P-\Delta$ or geometric non-linearities).
- Transverse plane bending only (no axial loads, torsional warping, or lateral-torsional buckling).

---

## 3. Architecture Summary

```
mechanics-playground/
├── backend/                   # FastAPI backend service
│   ├── app/
│   │   ├── api/               # API routes and Pydantic schemas
│   │   ├── mechanics/         # Pure mechanics package (no FastAPI dependencies)
│   │   │   ├── beam.py        # Beam model and configuration
│   │   │   ├── sections.py    # Cross-section property calculators
│   │   │   ├── units.py       # Unit conversion layer
│   │   │   ├── mesh.py        # Adaptive 1D discretization
│   │   │   ├── element.py     # Euler-Bernoulli 2-node cubic Hermite element
│   │   │   ├── solver.py      # Assembly, boundary conditions, partition solve
│   │   │   ├── postprocessor.py# Continuous curves, extrema, bending stress
│   │   │   └── validation_cases.py # Closed-form reference solutions
│   │   └── services/          # Orchestration service layer
│   └── tests/                 # Unit, analytical regression, and API tests
├── frontend/                  # React + TypeScript + Vite frontend
│   └── src/
│       ├── components/        # Property panels, SVG beam schematic, engineering charts
│       ├── services/          # API client
│       └── utils/             # Unit conversion utilities
└── docs/                      # Comprehensive technical documentation
```

---

## 4. Technology Stack

- **Backend**: Python 3.11+, FastAPI, Pydantic v2, NumPy, pytest
- **Frontend**: React 19, TypeScript (strict mode), Vite, Chart.js / react-chartjs-2, TailwindCSS / modern CSS
- **CI/CD**: GitHub Actions

---

## 5. Getting Started

### Prerequisites
- Python 3.11 or later
- Node.js 18+ (Node 24 LTS recommended) and npm

### Backend Setup
```bash
cd backend
python -m venv .venv
# Windows:
.venv\Scripts\activate
# Linux/macOS:
source .venv/bin/activate

pip install -r requirements.txt
pytest
uvicorn app.main:app --reload --port 8000
```
The backend API will be available at `http://localhost:8000`. Interactive API documentation is available at `http://localhost:8000/docs`.

### Frontend Setup
```bash
cd frontend
npm install
npm run dev
```
Open `http://localhost:5173` in your browser.

---

## 6. Running Tests

### Backend Tests
```bash
cd backend
pytest -v
```

### Frontend Type Checking and Build
```bash
cd frontend
npm run typecheck
npm run build
```

---

## 7. Documentation Links

- [Architecture Overview](docs/architecture.md)
- [Mechanics Theory & Governing Equations](docs/mechanics-theory.md)
- [Numerical Method & Finite Element Formulation](docs/numerical-method.md)
- [Sign Convention](docs/sign-convention.md)
- [Analytical Validation Suite & Error Analysis](docs/validation.md)
- [Testing Strategy & Quality Assurance](docs/testing.md)
- [API Specification](docs/api.md)
- [Deployment Guide](docs/deployment.md)
- [Project Roadmap](docs/roadmap.md)
- [Future AI Playground Integration](docs/ai-playground-integration.md)

---

## 8. License

MIT License. See [LICENSE](LICENSE) for details.
