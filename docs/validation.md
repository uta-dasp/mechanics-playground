# Analytical Validation Suite & Numerical Benchmarks

This document records the analytical verification cases, closed-form equations, numerical benchmarks, error metrics, and validation results for the mechanics solver.

---

## 1. Validation Methodology

In compliance with the project's engineering rules and the `mechanics-verification` skill:
1. Every solver capability is evaluated against closed-form analytical solutions derived from classical mechanics theory.
2. A successful solver run is not considered proof of correctness; numerical values are compared directly with analytical expressions.
3. For every quantity, the absolute error $|y_{\text{num}} - y_{\text{analytical}}|$ and relative error $\frac{|y_{\text{num}} - y_{\text{analytical}}|}{|y_{\text{analytical}}|}$ are calculated.
4. If numerical results disagree with analytical solutions beyond justified tolerances ($\le 10^{-5}$), the formulation, units, sign conventions, and discretization are investigated; tolerances are never loosened arbitrarily.

---

## 2. Benchmark Case Definitions

### Case A: Cantilever with Tip Point Load $P$
- **Geometry**: Length $L = 4.0\,\text{m}$, Rectangular cross-section ($b = 0.2\,\text{m}, h = 0.3\,\text{m}$).
- **Material**: Structural Steel, $E = 200.0 \times 10^9\,\text{Pa}$ ($200\,\text{GPa}$).
- **Support**: Fixed support at $x = 0$.
- **Loading**: Downward point load $P = 10,000\,\text{N}$ at $x = L$ ($F_y = -10,000\,\text{N}$).
- **Analytical Formulas**:
  - Vertical Reaction at $x=0$: $R_y = P = 10,000\,\text{N}$
  - Reaction Moment at $x=0$: $M_R = P L = 40,000\,\text{N}\cdot\text{m}$ (CCW)
  - Tip Deflection: $\delta = -\frac{P L^3}{3 E I}$
  - Maximum Bending Moment Magnitude: $M_{\max} = P L = 40,000\,\text{N}\cdot\text{m}$
  - Peak Bending Stress: $\sigma_{\max} = \frac{P L c}{I}$

### Case B: Simply Supported Beam with Central Point Load $P$
- **Geometry**: Length $L = 6.0\,\text{m}$, Rectangular cross-section ($b = 0.25\,\text{m}, h = 0.4\,\text{m}$).
- **Material**: Structural Steel, $E = 200.0 \times 10^9\,\text{Pa}$.
- **Supports**: Pin support at $x = 0$, Roller support at $x = L$.
- **Loading**: Downward point load $P = 24,000\,\text{N}$ at midspan $x = L/2 = 3.0\,\text{m}$ ($F_y = -24,000\,\text{N}$).
- **Analytical Formulas**:
  - Left Reaction: $R_1 = P / 2 = 12,000\,\text{N}$
  - Right Reaction: $R_2 = P / 2 = 12,000\,\text{N}$
  - Midspan Bending Moment: $M(L/2) = \frac{P L}{4} = 36,000\,\text{N}\cdot\text{m}$
  - Midspan Deflection: $\delta = -\frac{P L^3}{48 E I}$
  - Peak Bending Stress: $\sigma_{\max} = \frac{M_{\max} c}{I}$

### Case C: Simply Supported Beam with Full-Span Uniform Load $w$
- **Geometry**: Length $L = 8.0\,\text{m}$, Circular solid section ($d = 0.3\,\text{m}$).
- **Material**: Aluminium, $E = 70.0 \times 10^9\,\text{Pa}$ ($70\,\text{GPa}$).
- **Supports**: Pin support at $x = 0$, Roller support at $x = L$.
- **Loading**: Uniform downward load $w = 5,000\,\text{N/m}$ over $[0, L]$ ($w_y = -5,000\,\text{N/m}$).
- **Analytical Formulas**:
  - Reactions: $R_1 = R_2 = \frac{w L}{2} = 20,000\,\text{N}$
  - Maximum Bending Moment: $M_{\max} = \frac{w L^2}{8} = 40,000\,\text{N}\cdot\text{m}$
  - Midspan Deflection: $\delta = -\frac{5 w L^4}{384 E I}$
  - Peak Bending Stress: $\sigma_{\max} = \frac{M_{\max} c}{I}$

### Case D: Cantilever with Full-Span Uniform Load $w$
- **Geometry**: Length $L = 5.0\,\text{m}$, Rectangular cross-section ($b = 0.2\,\text{m}, h = 0.35\,\text{m}$).
- **Material**: Structural Steel, $E = 200.0 \times 10^9\,\text{Pa}$.
- **Support**: Fixed support at $x = 0$.
- **Loading**: Uniform downward load $w = 8,000\,\text{N/m}$ over $[0, L]$ ($w_y = -8,000\,\text{N/m}$).
- **Analytical Formulas**:
  - Vertical Reaction at $x=0$: $R_y = w L = 40,000\,\text{N}$
  - Reaction Moment at $x=0$: $M_R = \frac{w L^2}{2} = 100,000\,\text{N}\cdot\text{m}$ (CCW)
  - Maximum Bending Moment Magnitude: $M_{\max} = \frac{w L^2}{2} = 100,000\,\text{N}\cdot\text{m}$
  - Tip Deflection: $\delta = -\frac{w L^4}{8 E I}$
  - Peak Bending Stress: $\sigma_{\max} = \frac{M_{\max} c}{I}$

---

## 3. Automated Regression Verification Suite

Automated verification tests run against these cases in `backend/tests/test_analytical_validation.py`. Every test asserts:
- Relative error $\le 10^{-5}$ ($0.001\%$)
- Equilibrium residual $|\sum F_y| < 10^{-9}\,\text{N}$ and $|\sum M| < 10^{-9}\,\text{N}\cdot\text{m}$.
