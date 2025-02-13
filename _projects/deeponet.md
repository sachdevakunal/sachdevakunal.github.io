---
title: "Development and Application of an Incompressible Navier-Stokes Solver"
date: 2024-02-12
excerpt: "Building a solver to simulate flow over a square geometry using the Incompressible Navier-Stokes equations."
description: "Implemented a CFD solver for incompressible flows and validated results for flow over a square obstacle."
image: /assets/images/incompressible-ns.png
link: /projects/incompressible-ns/
---

# Development and Application of an Incompressible Navier-Stokes Solver

## **Objectives**
1. **Develop an incompressible Navier-Stokes solver** using a fractional-step method and finite difference discretization to simulate viscous fluid flows.  
2. **Validate the solver** by simulating lid-driven cavity flow and comparing velocity profiles with benchmark data to assess accuracy.  
3. **Apply the solver to simulate flow over a square obstacle** at different Reynolds numbers to analyze wake formation, vortex shedding, and flow separation.  
4. **Evaluate the solver’s performance and numerical stability** by testing different boundary conditions, time-stepping methods, and solver configurations.  

---

## **Governing Equations**  
The incompressible Navier-Stokes (iNS) equations governing viscous, incompressible fluid flow are given as:  

$$
\frac{\partial \mathbf{u}}{\partial t} + (\mathbf{u} \cdot \nabla) \mathbf{u} = -\frac{1}{\rho} \nabla p + \nu \nabla^2 \mathbf{u} + \mathbf{f}, \quad \nabla \cdot \mathbf{u} = 0
$$

where \( \mathbf{u} \) is the velocity field, \( p \) is pressure, \( \nu \) is kinematic viscosity, and \( \mathbf{f} \) represents external forces. To solve these equations, a **fractional-step method** is used with the **Adams-Bashforth (AB2) scheme for advection** and **Crank-Nicolson (CN) scheme for diffusion**.

The final discretized iNS equations, formulated by **Perot**, ensure second-order accuracy in time and are expressed as:

$$
R u^F = S u^n + \Delta t \left( \frac{3a^n - a^{n-1}}{2} \right) + \frac{\Delta t \nu}{2} \left( bc_L^{n+1} + bc_L^n \right)
$$

$$
D R^{-1} G P^{n+1} = \frac{D u^F}{\Delta t} + \frac{bc_D^{n+1}}{\Delta t}
$$

$$
u^{n+1} = u^F - \Delta t R^{-1} G P^{n+1}
$$

where \( u^F \) is the fractional-step velocity, \( a^n \) is the advection term, and \( bc_L, bc_D \) are boundary condition terms. The operators are defined as:

$$
R = I - \frac{\Delta t}{2} \nu L, \quad S = I + \frac{\Delta t}{2} \nu L, \quad R^{-1} \approx I + \frac{\Delta t}{2} \nu L
$$

where \( D, G, L \) represent the divergence, gradient, and Laplacian operators, respectively.

---

## **Grid and Domain Setup**  
A **staggered grid** is used to discretize the domain, where velocity components \( u, v \) are stored at the **cell faces**, while **pressure is stored at the cell centers**. This approach helps prevent **checkerboard instability**, which occurs when using a regular grid for pressure and velocity storage.

The computational domain is a **\( 1 \times 1 \) square** discretized into a **\( 129 \times 129 \) grid**. The boundary conditions are as follows:
- **Top boundary:** \( u = 1 \), \( v = 0 \) (to simulate a moving lid).  
- **Bottom, left, and right boundaries:** \( u = 0 \), \( v = 0 \) (stationary walls).  
- **Initial conditions:** The velocity and pressure fields are initialized to zero.

A schematic representation of the staggered grid is shown below:

![Grid Discretization](../assets/images/grid_placeholder.png)  
