# Sign Convention

This document defines the single, authoritative sign convention used throughout the **Interactive Mechanics Playground**. This convention is strictly maintained across:
- The finite element solver formulation
- Unit tests and analytical validation suites
- API request/response models
- SVG schematic graphics and arrow directions
- Shear, moment, and deflection plots
- Extrema reporting and documentation

---

## 1. Global Coordinate System

The Cartesian coordinate axes are defined as:
- **$x$-axis**: Longitudinal axis along the beam centroidal axis, running from the left boundary ($x = 0$) to the right boundary ($x = L$).
- **$y$-axis**: Transverse vertical axis, pointing **upward** ($+y = \text{up}$).
- **$z$-axis**: Out-of-plane axis, pointing out of the page toward the viewer, forming a right-handed system ($x \times y = z$).

```
        y (up, +)
        ^
        |
        |
        +------> x (right, +)
       /
      v z (out-of-plane, counterclockwise +)
```

---

## 2. Kinematic Degrees of Freedom

For any node $i$ located at $x_i$:
- **Transverse Deflection $v(x)$**:
  - Positive ($v > 0$): Beam moves **upward** in $+y$.
  - Negative ($v < 0$): Beam sags **downward** in $-y$.
- **Slope / Rotation $\theta(x) = \frac{dv}{dx}$**:
  - Positive ($\theta > 0$): **Counterclockwise** (CCW) rotation about $+z$.
  - Negative ($\theta < 0$): **Clockwise** (CW) rotation.

---

## 3. External Actions & Reactions

### Applied Forces & Moments
- **Concentrated Force $F_y$**:
  - Positive ($F_y > 0$): Directed **upward** ($+y$).
  - Negative ($F_y < 0$): Directed **downward** (gravity load, $-y$).
  - *UI convention*: The user may enter positive magnitudes and specify "downward" orientation; the conversion layer sets $F_y = -|F|$.
- **Uniformly Distributed Load $w(x)$**:
  - Units: $\text{N/m}$.
  - Positive ($w > 0$): Directed **upward** ($+y$).
  - Negative ($w < 0$): Directed **downward** (downward pressure/weight, $-y$).
- **Concentrated Moment $M_z$**:
  - Units: $\text{N}\cdot\text{m}$.
  - Positive ($M_z > 0$): **Counterclockwise** (CCW).
  - Negative ($M_z < 0$): **Clockwise** (CW).

### Support Reactions
- **Vertical Reaction Force $R_y$**:
  - Positive ($R_y > 0$): Exerts an **upward** force on the beam ($+y$).
  - Negative ($R_y < 0$): Exerts a **downward** force on the beam (hold-down reaction, $-y$).
- **Reaction Moment $M_R$**:
  - Positive ($M_R > 0$): Exerts a **counterclockwise** moment on the beam (CCW, $+z$).
  - Negative ($M_R < 0$): Exerts a **clockwise** moment on the beam (CW).

---

## 4. Internal Shear Force $V(x)$

The internal shear force is defined according to the standard civil/mechanical engineering convention (positive shear causes clockwise rotation of an isolated beam element).

### Cut Face Definitions
- **Right face of a cut** (outward normal pointing in $+x$):
  - Positive shear force $V(x)$ points **downward** ($-y$).
- **Left face of a cut** (outward normal pointing in $-x$):
  - Positive shear force $V(x)$ points **upward** ($+y$).

```
     Left face (outward -x)        Right face (outward +x)
             ^                              |
             | V > 0                        |
        +----+----+                    +----+----+
        |         |                    |         |
        | Segment |                    | Segment |
        |         |                    |         |
        +---------+                    +----+----+
                                            |
                                            v V > 0
```

### Governing Relations
$$V(x) = \frac{dM}{dx}$$
$$\frac{dV}{dx} = -w(x)$$

---

## 5. Internal Bending Moment $M(x)$

The internal bending moment is defined by the standard "tension on bottom" convention (positive bending moment creates a "smiling" deflected shape).

### Cut Face Definitions
- **Right face of a cut** (outward normal in $+x$):
  - Positive moment $M(x)$ acts **counterclockwise** (CCW).
- **Left face of a cut** (outward normal in $-x$):
  - Positive moment $M(x)$ acts **clockwise** (CW).

```
         Clockwise M > 0            Counterclockwise M > 0
               )                                (
               )                                (
         +-----+-----+                    +-----+-----+
         |           |                    |           |
         |  Segment  |                    |  Segment  |
         |           |                    |           |
         +-----+-----+                    +-----+-----+
               )                                (
               )                                (
        (Left face, -x)                  (Right face, +x)
```

### Curvature Relation
$$M(x) = E I \frac{d^2 v}{dx^2}$$
- When $M(x) > 0$: $\frac{d^2 v}{dx^2} > 0 \implies$ Curvature is concave upward (sagging). Bottom fibers are in tension, top fibers are in compression.
- When $M(x) < 0$: $\frac{d^2 v}{dx^2} < 0 \implies$ Curvature is concave downward (hogging). Top fibers are in tension, bottom fibers are in compression.

---

## 6. Longitudinal Bending Stress $\sigma_x(x, y_f)$

The normal stress at an arbitrary fiber elevation $y_f$ measured from the neutral axis (with $+y_f$ upward toward top fibers) is:
$$\sigma_x(x, y_f) = -\frac{M(x) \cdot y_f}{I}$$

- **Top fiber ($y_f = +c$)**:
  $$\sigma_{\text{top}}(x) = -\frac{M(x) \cdot c}{I}$$
  For positive $M > 0$, $\sigma_{\text{top}} < 0$ (compression).
- **Bottom fiber ($y_f = -c$)**:
  $$\sigma_{\text{bot}}(x) = +\frac{M(x) \cdot c}{I}$$
  For positive $M > 0$, $\sigma_{\text{bot}} > 0$ (tension).
- **Extreme magnitude**:
  $$\sigma_{\max}(x) = \frac{|M(x)| \cdot c}{I}$$
