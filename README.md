# Heat_Conduction_Solver - 1D Steady-State Heat Conduction Solver (FVM)

![Language](https://img.shields.io/badge/Language-Python%20%7C%20MATLAB-blue)
![Method](https://img.shields.io/badge/Method-Finite%20Volume%20Method-green)
![Status](https://img.shields.io/badge/Status-Validated-success)

## 📌 Project Overview
This project implements a numerical solution for 1D steady-state heat conduction with volumetric heat generation using the **Finite Volume Method (FVM)**. The solver determines the temperature distribution across a plane wall subject to asymmetric boundary conditions: convection on one side and a constant heat flux on the other.

This repository demonstrates:
1.  **Numerical Analysis:** Discretization of differential equations using FVM.
2.  **Validation:** Comparison of numerical results against an exact analytical solution.
3.  **Mesh Sensitivity:** Analysis of solution convergence as node count ($N$) increases.
4.  **Advanced Post-Processing:** Implementation of quadratic interpolation to resolve sub-grid thermal peaks.

## ⚙️ Problem Description
The physical system consists of a plane wall with the following parameters based on the project specifications:

* **Geometry:** Thickness, Height, & Width: $L = 0.75~m, H = 1.5~m, W = 1~m$.
* **Material:** Constant thermal conductivity $k = 40~W/m\cdot K$.
* **Heat Source:** Uniform volumetric heat generation $\dot{g}''' = 5 \times 10^4~W/m^3$.

### Boundary Conditions
1.  **Left Face ($x=0$):** Convection with $h_f = 200~W/m^2\cdot K$ and fluid temperature $T_{f} = 20^\circ C$.
2.  **Right Face ($x=L$):** Uniform heat flux input $\dot{q}'' = 100~W/m^2$ (flowing into the wall).

## 🧮 Mathematical Formulation
The governing differential equation for 1D steady-state conduction is derived from the energy balance:

$$\frac{d^2T}{dx^2} + \frac{\dot{g}}{k} = 0$$

The domain is discretized into $N$ control volumes. Applying the conservation of energy to each node results in a system of linear algebraic equations in the form $[A]\{T\} = \{b\}$, which is solved using matrix inversion.

## 📊 Results & Analysis

### 1. Validation (Numerical vs. Analytical)
The numerical solution (FVM) was tested against the analytical solution derived from integrating the heat equation.
* **Observation:** At $N=101$, the Maximum Absolute Error is negligible (< 0.1°C).

![Temperature Distribution](results/temp_distribution.png)
*(Fig 1: Overlay of Analytical solution (red) and Numerical approximation (blue).)*

### 2. Mesh Independence Study
The system was solved iteratively for node counts ranging from $N=3$ to $N=101$. The maximum temperature stabilizes rapidly, confirming the consistency of the numerical scheme.

![Convergence Plot](results/max_temp_convergence.png)

### 3. Handling Discretization Artifacts (Grid Locking)
When plotting the **location** of the maximum temperature versus node count, a "stair-step" artifact was observed. This occurs because standard peak detection (`max()`) is limited to discrete node locations.

**Solution:** I implemented **Quadratic Interpolation**. By fitting a parabola to the peak node and its nearest neighbors, the solver estimates the *true* continuous physical location of the thermal peak.

![Interpolation Fix](results/max_temp_location.png)
*(Fig 3: The red dashed line shows the discrete "staircase" error. The solid blue line shows the corrected sub-grid location using quadratic interpolation.)*
