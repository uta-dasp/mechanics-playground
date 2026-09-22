# Numerical Method & Finite Element Implementation

This document describes the numerical algorithms, discretization strategy, matrix equations, and post-processing methods implemented in the mechanics solver.

---

## 1. Discretization & Mesh Generation

To eliminate discretization errors for point loads and boundary conditions while providing high-resolution curves for distributed loads, the solver employs an **adaptive 1D keypoint discretization** algorithm.

### Keypoint Identification
Let the beam span be $[0, L]$. A set of critical keypoints $X_{\text{key}}$ is assembled:
1. Beam boundaries: $x = 0$ and $x = L$.
2. Support locations: $\{x_s\}$.
3. Point load locations: $\{x_p\}$.
4. Applied moment locations: $\{x_m\}$.
5. Distributed load start and end points: $\{x_{w,\text{start}}, x_{w,\text{end}}\}$.

All keypoints are sorted and deduplicated within a numerical spatial tolerance $\epsilon_x = 10^{-7} \cdot L$.

### Sub-segment Subdivision
Between adjacent keypoints $x_k$ and $x_{k+1}$, the interval length is $\Delta x_k = x_{k+1} - x_k$. If $\Delta x_k > h_{\max}$ (where $h_{\max} = L / 50$), the interval is subdivided into $n_k = \lceil \Delta x_k / h_{\max} \rceil$ equal elements.

This guarantees:
- Every support, point load, and moment is located exactly at a node.
- No distributed load starts or stops within an element; $w(x)$ is piecewise constant across every element.
- The mesh contains at least 50 elements across the span, providing dense sampling points for plotting.

---

## 2. Element Matrix Assembly

For each element $e$ connecting node $i$ (at $x_i$) and node $j$ (at $x_j$) with length $L_e = x_j - x_i$:
- Degrees of freedom:
  $$\mathbf{d}_e = [v_i, \theta_i, v_j, \theta_j]^T$$
  where global DOF indices are:
  $$\text{dof}(i) = [2i, 2i+1], \quad \text{dof}(j) = [2j, 2j+1]$$

### Local Stiffness Matrix $\mathbf{k}_e$:
$$\mathbf{k}_e = \frac{E I}{L_e^3} \begin{bmatrix}
12 & 6 L_e & -12 & 6 L_e \\
6 L_e & 4 L_e^2 & -6 L_e & 2 L_e^2 \\
-12 & -6 L_e & 12 & -6 L_e \\
6 L_e & 2 L_e^2 & -6 L_e & 4 L_e^2
\end{bmatrix}$$

The local stiffness matrix is assembled into the global stiffness matrix $\mathbf{K} \in \mathbb{R}^{2N \times 2N}$ using standard direct stiffness summation:
$$K_{P(r), P(s)} \leftarrow K_{P(r), P(s)} + k_{e, rs}$$
where $P = [2i, 2i+1, 2j, 2j+1]$.

---

## 3. Global Load Vector Construction

The global force vector $\mathbf{F} \in \mathbb{R}^{2N}$ consists of:
1. **Direct Nodal Point Loads**:
   - For a point load $F_y$ at node $k$: $F_{2k} \leftarrow F_{2k} + F_y$.
   - For an applied moment $M_z$ at node $k$: $F_{2k+1} \leftarrow F_{2k+1} + M_z$.
2. **Consistent Distributed Loads**:
   - For an element $e$ subject to uniform upward load $w$:
     $$\mathbf{f}_e = w L_e \begin{bmatrix} 1/2 \\ L_e / 12 \\ 1/2 \\ -L_e / 12 \end{bmatrix}$$
     Added directly into global indices $[2i, 2i+1, 2j, 2j+1]$.

---

## 4. Boundary Conditions & Partitioned Solution

Supports impose homogeneous Dirichlet constraints on selected DOFs:
- **Pin / Roller Support at Node $k$**:
  - Constrains $v_k = 0 \implies \text{DOF } 2k \in \mathcal{C}$.
- **Fixed Support at Node $k$**:
  - Constrains $v_k = 0$ and $\theta_k = 0 \implies \text{DOFs } 2k, 2k+1 \in \mathcal{C}$.

Let $\mathcal{C}$ be the set of constrained DOF indices, and $\mathcal{F}$ be the set of free DOF indices ($\mathcal{C} \cup \mathcal{F} = \{0, 1, \dots, 2N-1\}$, $\mathcal{C} \cap \mathcal{F} = \emptyset$).

The global system is partitioned as:
$$\begin{bmatrix} \mathbf{K}_{ff} & \mathbf{K}_{fc} \\ \mathbf{K}_{cf} & \mathbf{K}_{cc} \end{bmatrix} \begin{bmatrix} \mathbf{d}_f \\ \mathbf{d}_c \end{bmatrix} = \begin{bmatrix} \mathbf{F}_f \\ \mathbf{F}_c + \mathbf{R}_c \end{bmatrix}$$

### Structural Stability Check
Before solving, the stability of $\mathbf{K}_{ff}$ is verified:
1. If $|\mathcal{C}| < 2$ for pin/roller systems (or if $|\mathcal{C}| < 1$ with fixed support), rigid-body motion is physically unconstrained.
2. The condition number or matrix rank of $\mathbf{K}_{ff}$ is checked. If $\mathbf{K}_{ff}$ is singular ($\det(\mathbf{K}_{ff}) \approx 0$ or condition number $> 10^{15}$), a `StructuralInstabilityError` is raised with engineering diagnostic messages indicating insufficient support constraints.

### Linear Equation Solving
Because $\mathbf{d}_c = \mathbf{0}$, the free displacements are determined by solving:
$$\mathbf{K}_{ff} \mathbf{d}_f = \mathbf{F}_f$$
using `numpy.linalg.solve`, which employs LAPACK LU decomposition with partial pivoting.

---

## 5. Reaction Recovery & Equilibrium Verification

Reactions at constrained DOFs are recovered from the second row of the partitioned system:
$$\mathbf{R}_c = \mathbf{K}_{cf} \mathbf{d}_f - \mathbf{F}_c$$

For each support at node $k$:
- If DOF $2k \in \mathcal{C}$: Vertical reaction force $R_y = R_{c}(2k)$.
- If DOF $2k+1 \in \mathcal{C}$: Reaction moment $M_R = R_{c}(2k+1)$.

### Equilibrium Residual Verification
The global static equilibrium of the recovered reactions and external applied loads is explicitly validated:
1. **Vertical Force Equilibrium**:
   $$\epsilon_{F} = \left| \sum R_y + \sum F_y^{\text{applied}} + \int_0^L w(x) dx \right|$$
2. **Moment Equilibrium about $x = 0$**:
   $$\epsilon_{M} = \left| \sum M_R + \sum R_y x_s + \sum M_z^{\text{applied}} + \sum F_y^{\text{applied}} x_p + \int_0^L w(x) x dx \right|$$

If either $\epsilon_F > 10^{-6}\,\text{N}$ or $\epsilon_M > 10^{-6}\,\text{N}\cdot\text{m}$, an equilibrium discrepancy warning is recorded.

---

## 6. Continuous Field Evaluation & Internal Forces

To generate continuous plotting curves for $v(x)$, $\theta(x)$, $V(x)$, $M(x)$, and $\sigma(x)$:
1. Within each element $e \in [x_i, x_j]$, the exact cubic Hermite displacement field is sampled at dense interior points:
   $$v(x) = \mathbf{N}(\xi) \mathbf{d}_e, \quad \theta(x) = \frac{1}{L_e} \frac{d\mathbf{N}}{d\xi} \mathbf{d}_e$$
2. For internal shear $V(x)$ and bending moment $M(x)$:
   - Because all load discontinuities occur at element boundaries, $w(x) = w_e = \text{const}$ within element $e$.
   - $V(x)$ varies linearly within each element:
     $$V(x) = V(x_i) - w_e (x - x_i)$$
   - $M(x)$ varies quadratically within each element:
     $$M(x) = M(x_i) + V(x_i)(x - x_i) - \frac{1}{2} w_e (x - x_i)^2$$
   - The starting values $V(0)$ and $M(0)$ are determined by boundary equilibrium, and values across nodes jump by the exact point loads $F_y$, support reactions $R_y$, and point moments $M_z$.
3. Extreme values (max/min shear, max sagging/hogging moment, max deflection, max bending stress) are located by evaluating the exact analytical critical points ($\frac{dV}{dx} = 0$, $V(x) = 0 \implies \frac{dM}{dx} = 0$, $\theta(x) = 0 \implies \frac{dv}{dx} = 0$) plus all nodal boundary values.
