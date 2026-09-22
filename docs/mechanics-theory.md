# Mechanics Theory & Governing Equations

This document provides the theoretical foundations, assumptions, governing differential equations, and analytical expressions for linear elastic Euler-Bernoulli beam bending.

---

## 1. Kinematic Assumptions

Euler-Bernoulli beam theory (classical beam theory) describes the bending of slender beams under the following foundational hypotheses:
1. **Normality & Straightness**: Cross-sections normal to the undeformed neutral axis remain plane and perpendicular to the deformed neutral axis after bending.
2. **Negligible Shear Deformation**: The transverse shear strain $\gamma_{xy} \approx 0$. The cross-section rotation is identical to the slope of the deflection curve:
   $$\theta(x) = \frac{dv}{dx}$$
3. **Small Deflections**: The slope $|\theta(x)| \ll 1$ and deflections $|v(x)| \ll L$, allowing the curvature $\kappa(x)$ to be approximated linearly:
   $$\kappa(x) = \frac{v''(x)}{\left(1 + [v'(x)]^2\right)^{3/2}} \approx \frac{d^2 v}{dx^2}$$
4. **Prismatic & Homogeneous**: The beam has a constant cross-section (area $A$, second moment of area $I$, outer-fiber distance $c$) and constant Young's modulus $E$ throughout its length $L$.
5. **Uncoupled In-Plane Bending**: Only transverse displacements $v(x)$ in the $y$-direction and bending rotations $\theta(x)$ about the $z$-axis are considered. Axial strain $\epsilon_x^0 = 0$, out-of-plane displacements $w_z = 0$, and torsional rotations $\phi = 0$.

---

## 2. Governing Differential Equations

For a beam segment subjected to an upward transverse distributed load $w(x)$ (units: $\text{N/m}$):

### 1. Differential Equilibrium
From static equilibrium of an infinitesimal beam slice of length $dx$:
- Vertical equilibrium:
  $$\frac{dV}{dx} = -w(x)$$
- Moment equilibrium:
  $$\frac{dM}{dx} = V(x)$$

Combining these yields:
$$\frac{d^2 M}{dx^2} = -w(x)$$

### 2. Constitutive Moment-Curvature Relation
Under linear elasticity and plane strain bending:
$$M(x) = E I \frac{d^2 v}{dx^2}$$

### 3. Fourth-Order Beam Equation
Substituting the moment-curvature relation into differential equilibrium yields the governing fourth-order ordinary differential equation:
$$\frac{d^2}{dx^2} \left( E I \frac{d^2 v}{dx^2} \right) = w(x)$$
For a prismatic beam ($EI = \text{const}$):
$$E I \frac{d^4 v}{dx^4} = w(x)$$

---

## 3. Finite Element Weak Form & Hermite Interpolation

The weak (variational) form of the governing beam equation is derived via the Principle of Virtual Work:
$$\int_0^L E I \frac{d^2 \delta v}{dx^2} \frac{d^2 v}{dx^2} dx = \int_0^L \delta v \cdot w(x) dx + \sum F_i \delta v(x_i) + \sum M_i \delta \theta(x_i)$$

Because the integrand involves second derivatives $\frac{d^2 v}{dx^2}$, the displacement field $v(x)$ requires $C^1$ continuity (continuity of both displacement and rotation across elements).

### Hermite Cubic Shape Functions
For a 2-node element of length $L_e = x_2 - x_1$, define the local normalized coordinate:
$$\xi = \frac{x - x_1}{L_e}, \quad \xi \in [0, 1]$$

The transverse displacement within the element is interpolated as:
$$v(\xi) = \mathbf{N}(\xi) \mathbf{d}_e = \begin{bmatrix} N_1(\xi) & N_2(\xi) & N_3(\xi) & N_4(\xi) \end{bmatrix} \begin{bmatrix} v_1 \\ \theta_1 \\ v_2 \\ \theta_2 \end{bmatrix}$$

where the shape functions are:
$$N_1(\xi) = 1 - 3\xi^2 + 2\xi^3$$
$$N_2(\xi) = L_e (\xi - 2\xi^2 + \xi^3)$$
$$N_3(\xi) = 3\xi^2 - 2\xi^3$$
$$N_4(\xi) = L_e (-\xi^2 + \xi^3)$$

The rotation within the element is:
$$\theta(\xi) = \frac{dv}{dx} = \frac{1}{L_e} \frac{dv}{d\xi} = \frac{d\mathbf{N}}{dx} \mathbf{d}_e$$

---

## 4. Element Stiffness Matrix

The element stiffness matrix is computed analytically from the virtual work integral:
$$\mathbf{k}_e = \int_0^{L_e} E I \left( \frac{d^2 \mathbf{N}}{dx^2} \right)^T \left( \frac{d^2 \mathbf{N}}{dx^2} \right) dx$$

Carrying out the integration yields the exact $4 \times 4$ element stiffness matrix:
$$\mathbf{k}_e = \frac{E I}{L_e^3} \begin{bmatrix}
12 & 6 L_e & -12 & 6 L_e \\
6 L_e & 4 L_e^2 & -6 L_e & 2 L_e^2 \\
-12 & -6 L_e & 12 & -6 L_e \\
6 L_e & 2 L_e^2 & -6 L_e & 4 L_e^2
\end{bmatrix}$$

### Properties of $\mathbf{k}_e$:
- **Symmetric**: $\mathbf{k}_e = \mathbf{k}_e^T$.
- **Positive semi-definite**: Rank is 2 (contains 2 rigid-body modes: vertical translation $[1, 0, 1, 0]^T$ and rigid-body rotation $[0, 1, L_e, 1]^T$).
- Satisfies $\mathbf{k}_e \mathbf{d}_{\text{rigid}} = \mathbf{0}$.

---

## 5. Consistent Equivalent Nodal Load Vectors

For a uniformly distributed load of intensity $w$ (upward) applied across an element of length $L_e$:
$$\mathbf{f}_e = \int_0^{L_e} \mathbf{N}(x)^T w dx = w L_e \begin{bmatrix} 1/2 \\ L_e / 12 \\ 1/2 \\ -L_e / 12 \end{bmatrix}$$

- $f_{e,1} = \frac{w L_e}{2}$ (vertical force at node 1)
- $f_{e,2} = \frac{w L_e^2}{12}$ (counterclockwise moment at node 1)
- $f_{e,3} = \frac{w L_e}{2}$ (vertical force at node 2)
- $f_{e,4} = -\frac{w L_e^2}{12}$ (clockwise moment at node 2)

Point loads $F_y$ and concentrated moments $M_z$ applied at nodes are added directly to the corresponding nodal DOFs in the global load vector $\mathbf{F}$.

---

## 6. Boundary Conditions & Reaction Recovery

Boundary conditions are enforced by partitioning the global system into free ($f$) and constrained ($c$) degrees of freedom:
$$\begin{bmatrix} \mathbf{K}_{ff} & \mathbf{K}_{fc} \\ \mathbf{K}_{cf} & \mathbf{K}_{cc} \end{bmatrix} \begin{bmatrix} \mathbf{d}_f \\ \mathbf{d}_c \end{bmatrix} = \begin{bmatrix} \mathbf{F}_f \\ \mathbf{F}_c + \mathbf{R}_c \end{bmatrix}$$

For homogeneous zero boundary conditions ($\mathbf{d}_c = \mathbf{0}$):
$$\mathbf{d}_f = \mathbf{K}_{ff}^{-1} \mathbf{F}_f$$
$$\mathbf{R}_c = \mathbf{K}_{cf} \mathbf{d}_f - \mathbf{F}_c$$

Here $\mathbf{R}_c$ represents the true physical reactions at constrained degrees of freedom, exactly accounting for any applied or consistent nodal loads $\mathbf{F}_c$ at support nodes.

---

## 7. Elastic Bending Stress Distribution

According to the flexure formula, the normal longitudinal stress $\sigma_x$ at a distance $y_f$ from the centroidal neutral axis (with $+y_f$ upward toward top fibers) is:
$$\sigma_x(x, y_f) = -\frac{M(x) y_f}{I}$$

- Top fiber ($y_f = +c$):
  $$\sigma_{\text{top}}(x) = -\frac{M(x) c}{I}$$
- Bottom fiber ($y_f = -c$):
  $$\sigma_{\text{bot}}(x) = +\frac{M(x) c}{I}$$
- Peak bending stress magnitude:
  $$\sigma_{\max} = \frac{|M_{\max}| c}{I}$$

When $M(x) > 0$ (positive bending moment), the top fiber is in compression ($\sigma_{\text{top}} < 0$) and the bottom fiber is in tension ($\sigma_{\text{bot}} > 0$).
