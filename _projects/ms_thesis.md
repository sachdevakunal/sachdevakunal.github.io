---
title: "Accelerating Reactive Flow Simulations with Deep Operator Networks (MS Thesis)"
order: 1
tools: "Python (JAX, NumPy), MATLAB, Cantera, PeleLMeX, High-Performance Computing (HPC), ParaView"
link: /projects/ms-thesis/
---

## Objective of This Work

- **Reactive flow simulations** are limited by the high computational cost of chemistry integration.  
- Developed a **machine learning–based surrogate framework** to accelerate this step in **PeleLMeX**, an open-source, high-fidelity CFD solver for reactive flows.  
- Implemented a **Deep Operator Network (DeepONet)** to learn the temporal evolution of reduced thermochemical states, bypassing conventional numerical integration.  
- Established a **C–Python interface** enabling seamless integration of the trained model with PeleLMeX.  
- Validated the framework on a **2D reacting flow case**, demonstrating strong accuracy and significant computational savings.  

## Background: Reaction Integration in PeleLMeX

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

## Deep Operator Network (DeepONets) Framework

- Deep Operator Networks (DeepONets) are neural architectures that learn mappings between functions, making them suitable for modeling complex dynamical systems.  
- Instead of predicting fixed scalar outputs, DeepONets learn the temporal evolution of entire thermochemical states over time.  
- The architecture consists of two subnetworks: the Branch Net, which processes the thermochemical input state, and the Trunk Net, which takes the time increment as input.  
- The outputs of both networks are combined through a dot product to predict the future thermochemical state.  
- This approach allows DeepONets to generalize across a wide range of initial conditions and reaction timescales.  
- In this work, DeepONets were trained to predict representative quantities such as temperature and key species mass fractions, effectively replacing traditional numerical integration of stiff ODEs.  

<p align="center">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/deeponet_architecture.png" width="75%">
</p> 

## Data Preparation and Reduction

- Detailed mechanisms contain many species, so a reduced set of representative scalars (key species and temperature) was selected to lower computational cost.  
- Representative species were chosen based on their influence on thermal and chemical dynamics, retaining only those that contribute most to system variability.  
- This reduction enables faster network training and inference while still capturing the essential evolution of temperature and major species.  
- The DeepONet predicts only the representative scalars, keeping the model size and complexity manageable.  
- A separate neural network called reconstruction network (RecNet) maps the representative scalars back to the remaining species, enabling full thermochemical state recovery without increasing DeepONet complexity.  
 

