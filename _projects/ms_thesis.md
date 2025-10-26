---
title: "Accelerating Reactive Flow Simulations with Deep Operator Networks (MS Thesis)"
order: 1
tools: "Python (JAX, NumPy), MATLAB, Cantera, PeleLMeX, High-Performance Computing (HPC), ParaView"
link: /projects/ms-thesis/
---

### Objective of This Work

- **Reactive flow simulations** are limited by the high computational cost of chemistry integration.  
- Developed a **machine learning–based surrogate framework** to accelerate this step in **PeleLMeX**, an open-source, high-fidelity CFD solver for reactive flows.  
- Implemented a **Deep Operator Network (DeepONet)** to learn the temporal evolution of reduced thermochemical states, bypassing conventional numerical integration.  
- Established a **C–Python interface** enabling seamless integration of the trained model with PeleLMeX.  
- Validated the framework on a **2D reacting flow case**, demonstrating strong accuracy and significant computational savings.  

### Background: Reaction Integration in PeleLMeX

- PeleLMeX is an open-source, high-fidelity CFD solver for low-Mach reactive flow simulations, built on the AMReX framework.  
- The solver employs an operator-splitting approach, separating transport (advection + diffusion) from reaction during each time advance.  
- In the reaction step, every grid cell solves a system of stiff, nonlinear ODEs representing detailed chemical kinetics.  
- These ODEs are integrated using SUNDIALS CVODE, an implicit solver designed for stiff reaction systems.  
- Although robust, this step dominates computational cost—typically over 70% of total runtime, especially for detailed fuel mechanisms.  
- Each cell evolves independently, enabling parallel execution but resulting in high cumulative computational overhead.  
- This motivates the development of a faster surrogate capable of predicting the temporal evolution of thermochemical states directly, without iterative ODE integration.  

<p align="center">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/op_split.png" width="75%">
</p>  

