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
 
## Integration into PeleLMeX

- The pretrained DeepONet model was coupled with the PeleLMeX solver through a custom C–Python interface implemented using Python.h and the NumPy C API.  
- During the reaction step of the operator-splitting loop, each cell's thermochemical state is flattened and passed to the DeepONet for inference.  
- The DeepONet predicts the updated representative scalars for the specified time increment, replacing the conventional stiff ODE integration.  
- The predicted outputs are mapped back to the full thermochemical state using the reconstruction network (RecNet).  
- The updated species and temperature fields are then returned to the solver and used to advance transport and subsequent chemistry evaluations.  

<p align="center">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/deeponet_integration_workflow.png" width="75%">
</p> 

<p align="center">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/Operator_splitting_schematic.png" width="75%">
</p> 

## Model Training

- The training data was generated using a 2D backward-facing step methane-air flame simulated with the DRM19 mechanism, providing diverse reacting-flow conditions.
- A coarse-grid simulation was used to collect temporal snapshots of temperature and species fields, from which representative species were selected based on a cutoff mass-fraction threshold.
- PCA was applied to the sampled snapshots to select diverse thermochemical states for training while reducing redundancy in the dataset.  
- Each sampled state was evolved through 0D constant-pressure reactors in Cantera to obtain time-series data for temperature and species evolution.  
- The DeepONet and RecNet models were built in JAX and trained on an NVIDIA RTX A6000 GPU using a two-stage process: pre-training followed by refined auto-regressive training.  
- Pre-training minimized short-step prediction error for physical consistency, while refined training increased rollout length by feeding the model’s own predictions back as inputs.

<p align="center">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/training_workflow.png" width="75%">
</p> 

## Results

- The DeepONet-based surrogate was integrated into PeleLMeX and tested on the 2D backward-facing step flame up to 3 ms using a refined AMR grid.  
- Predicted temperature and key species fields closely matched the solver’s native chemistry integration, with low absolute error across all time snapshots.  
- Error contours confirmed that the surrogate preserved major flame structures while significantly reducing the overall cost of the reaction step.  

<figure style="text-align: center;">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/t050.png" width="75%">
  <figcaption style="font-size: 16px; margin-top: 6px; text-align: center;">
    Figure: Temperature and CH4 mass fraction—CVODE, React-DeepONet, and absolute error at t = 0.50 ms.
  </figcaption>
</figure>

<figure style="text-align: center;">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/t100.png" width="75%">
  <figcaption style="font-size: 16px; margin-top: 6px; text-align: center;">
    Figure: Temperature and CH4 mass fraction—CVODE, React-DeepONet, and absolute error at t = 1.00 ms.
  </figcaption>
</figure>

<figure style="text-align: center;">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/t150.png" width="75%">
  <figcaption style="font-size: 16px; margin-top: 6px; text-align: center;">
    Figure: Temperature and CH4 mass fraction—CVODE, React-DeepONet, and absolute error at t = 1.50 ms.
  </figcaption>
</figure>

<figure style="text-align: center;">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/t175.png" width="75%">
  <figcaption style="font-size: 16px; margin-top: 6px; text-align: center;">
    Figure: Temperature and CH4 mass fraction—CVODE, React-DeepONet, and absolute error at t = 1.75 ms.
  </figcaption>
</figure>

<figure style="text-align: center;">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/t200.png" width="75%">
  <figcaption style="font-size: 16px; margin-top: 6px; text-align: center;">
    Figure: Temperature and CH4 mass fraction—CVODE, React-DeepONet, and absolute error at t = 2.00 ms.
  </figcaption>
</figure>

<figure style="text-align: center;">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/t225.png" width="75%">
  <figcaption style="font-size: 16px; margin-top: 6px; text-align: center;">
    Figure: Temperature and CH4 mass fraction—CVODE, React-DeepONet, and absolute error at t = 2.25 ms.
  </figcaption>
</figure>

<figure style="text-align: center;">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/t250.png" width="75%">
  <figcaption style="font-size: 16px; margin-top: 6px; text-align: center;">
    Figure: Temperature and CH4 mass fraction—CVODE, React-DeepONet, and absolute error at t = 2.50 ms.
  </figcaption>
</figure>

<figure style="text-align: center;">
  <img src="https://sachdevakunal.github.io/images/ms_thesis/t275.png" width="75%">
  <figcaption style="font-size: 16px; margin-top: 6px; text-align: center;">
    Figure: Temperature and CH4 mass fraction—CVODE, React-DeepONet, and absolute error at t = 2.75 ms.
  </figcaption>
</figure>
