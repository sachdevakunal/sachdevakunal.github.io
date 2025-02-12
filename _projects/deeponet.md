---
permalink: /projects/deeponet/
title: "Accelerating CFD Simulations with DeepONet"
author_profile: true
---

# Accelerating CFD Simulations with DeepONet

## Overview
This project focuses on integrating **Deep Operator Networks (DeepONets)** into **PeleLMeX**, a high-fidelity reactive CFD solver, to accelerate the reaction integration step while maintaining accuracy.

By replacing computationally expensive **ODE solvers** with a trained DeepONet model, we achieve a significant reduction in computational cost while maintaining high accuracy.

---

## Problem Statement
Reactive CFD simulations involve **solving stiff ODEs** for chemical kinetics at every grid point. This process:
- Is computationally expensive.
- Becomes the main bottleneck in large-scale simulations.
- Limits real-time CFD applications.

**Solution:**  
We leverage a **DeepONet-based surrogate model** to replace the ODE solver, drastically improving computational efficiency.

---

## Technologies Used
- **Machine Learning**: DeepONet, TensorFlow, PyTorch
- **CFD Solver**: PeleLMeX (AMReX-based low Mach number solver)
- **Programming Languages**: Python, C, C++
- **HPC**: MPI-based parallel computing

---

## Methodology

### 🔹 **1. Data Collection & Preprocessing**
- Generated **zero-dimensional reactor data** using **Cantera**.
- Clustered data into **four reaction groups** based on thermochemical conditions.
- Each cluster was trained separately with a different **DeepONet model**.

### 🔹 **2. Training DeepONet**
- Used **high-fidelity reaction data** as input.
- Trained **four DeepONets** (one per cluster) to predict **species evolution & temperature**.
- Optimized architecture using **MSE loss & Adam optimizer**.

### 🔹 **3. Integrating DeepONet into PeleLMeX**
- Converted the trained DeepONet models into a **C++ callable function**.
- Integrated this function inside **PeleLMeX’s reaction module**.
- Ensured parallel computing compatibility for **HPC performance**.

### 🔹 **4. Performance Evaluation**
- **Compared DeepONet predictions vs. traditional ODE solvers**.
- **Measured speedup & accuracy trade-offs**.
- Analyzed computational savings in **full 3D CFD simulations**.

---

## Results & Performance Gains
✅ **Achieved 4-5x speedup** over traditional stiff ODE solvers.  
✅ **Maintained high accuracy** in predicting chemical species evolution.  
✅ **Successfully integrated into PeleLMeX**, enabling **faster CFD simulations**.

| Metric              | Traditional ODE Solver | DeepONet Model |
|---------------------|----------------------|----------------|
| Computational Time  | **100% (Baseline)**   | **20-25% of ODE time** |
| Accuracy (MSE)      | **Baseline (0.0)**    | **<2% error** |
| HPC Scalability     | Limited               | **Highly Parallel** |

---

## 📷 Images & Videos

### 🔹 **DeepONet Prediction vs. ODE Solver**
![DeepONet vs ODE Solver](../assets/images/deeponet_comparison.png)

### 🔹 **Speedup in Chemical Kinetics Computation**
![Speedup Graph](../assets/images/deeponet_speedup.png)

### 🎥 **Demo Video**
<iframe width="560" height="315" src="https://www.youtube.com/embed/YOUR_VIDEO_ID" frameborder="0" allowfullscreen></iframe>

---

## 🔗 Related Links
- 📂 **[GitHub Repository](https://github.com/yourrepo)**
- 📄 **[Research Paper (if applicable)](https://example.com)**
- 🔗 **[Back to Projects](../projects/)**

---

## Conclusion
This work demonstrates that **DeepONets can significantly accelerate chemical reaction integration in CFD simulations**, making large-scale combustion simulations more feasible for **high-performance computing (HPC) applications**.

Further work includes:
- Extending DeepONet to handle **multi-species reactions with turbulence**.
- Investigating alternative ML architectures for **adaptive chemistry models**.

---

