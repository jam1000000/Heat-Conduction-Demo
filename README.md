# 1D Heat Conduction Demo

A minimal finite element simulation of 1D heat conduction using [FEniCSx (DOLFINx)](https://fenicsproject.org/), solved with an implicit Euler time-stepping scheme.

## Overview

This notebook simulates how an initial temperature distribution decays over time in a rod with fixed (zero) temperatures at both ends. An introduction to mesh creation, weak form derivation, boundary conditions, and time-stepping with a parabolic PDE problem.

**Governing equation (Heat Equation):**

$$\frac{\partial u}{\partial t} - \nabla^2 u = F \quad \text{on } [0,1], \quad t > 0$$

with homogeneous Dirichlet boundary conditions $u(0, t) = u(1, t) = 0$ and initial condition $u(x, 0) = \sin(\pi x)$.

## Features

- **1D mesh** over $[0, 1]$ with 64 uniform elements
- **P1 Lagrange elements** (piecewise linear basis functions)
- **Implicit Euler** time integration (unconditionally stable)
- **PETSc** linear solver backend via DOLFINx's `LinearProblem`
- Time evolution plotted at each step with `matplotlib`

## Requirements

| Package | Role |
|---|---|
| `dolfinx` | FEM mesh, function spaces, BCs, and assembly |
| `ufl` | Symbolic weak form construction |
| `mpi4py` | MPI communicator (required by DOLFINx) |
| `petsc4py` | PETSc scalar types and linear algebra backend |
| `numpy` | Array operations |
| `matplotlib` | Visualization |

The recommended way to install FEniCSx and its dependencies is via **conda** or **Docker**. See the [FEniCSx installation guide](https://github.com/FEniCS/dolfinx#installation) for details.

```bash
# Using conda (recommended)
conda create -n fenicsx-env
conda activate fenicsx-env
conda install -c conda-forge fenics-dolfinx mpich pyvista
```

## Usage

Launch Jupyter and open the notebook:

```bash
jupyter notebook "Heat_Conduction (simple demo).ipynb"
```

Run both cells. The notebook will output a plot showing the temperature profile $u(x, t)$ at 12 time steps ($\Delta t = 0.12$), illustrating our sinusoidal initial condition's smooth decay.

## Numerical Method

The weak form is derived by multiplying the PDE by a test function $v$ and integrating by parts. The implicit Euler discretization yields the linear system solved at each time step:

$$\int u^n v \, dx + \Delta t \int \nabla u^n \cdot \nabla v \, dx = \int u^{n-1} v \, dx + \Delta t \int F v \, dx$$

Because the left-hand side (stiffness matrix) is constant across all time steps, the `LinearProblem` is assembled once outside the time loop for efficiency. Only the right-hand side is re-evaluated at each step as `uOld` is updated in place.

## References

- [FEniCSx Project](https://fenicsproject.org/)
- [DOLFINx GitHub](https://github.com/FEniCS/dolfinx)
- [The FEniCSx Tutorial](https://jsdokken.com/dolfinx-tutorial/) — especially the [Heat Equation chapter](https://jsdokken.com/dolfinx-tutorial/chapter2/heat_equation.html)
