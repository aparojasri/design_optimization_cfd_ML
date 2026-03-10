

# Multi-Stage Optimization of a Wells Turbine for North Atlantic Sea States

### A Hybrid CFD & Physics-Informed Neural Network (PINN) Approach for OWC Systems

---

## 🌊 Project Overview

The **Wells Turbine** is the standard Power Take-Off (PTO) mechanism for Oscillating Water Column (OWC) wave energy converters. However, standard symmetric blade geometries (e.g., NACA 0015) suffer from **early aerodynamic stall** under the high-velocity, stochastic flow conditions characteristic of energetic ocean sites.

This project develops a **Sequential Machine-Learning-Assisted Optimization Framework**. Because evaluating the 5-dimensional design space $(AoA, V_{in}, RPM, D, N)$ using full 3D transient CFD is computationally prohibitive, we decoupled the physics. We integrated **16,759 hours of real spectral ocean data** to set boundary conditions, generated discrete CFD matrices, and built a **Physics-Informed Neural Network (PINN)** to interpolate the continuous efficiency surfaces between numerical steps.

---

## 🎯 Problem Statement

* **The Environment:** Real ocean waves are stochastic. NOAA Station 41001 data reveals air velocities inside the OWC chamber frequently exceed 40 m/s during storm regimes, pushing standard turbines deep into stall.
* **The Computational Bottleneck:** Traditional brute-force CFD sweeps across all aerodynamic and kinematic variables simultaneously require thousands of hours on HPC clusters.
* **The Solution:** A staggered dimensionality-reduction pipeline. We isolate 2D aerodynamics, transition to 3D rotational kinematics, and finalize with turbine assembly. The PINN acts as an intelligent interpolator, bridging the gaps between discrete $4 \times 4$ CFD matrices to pinpoint the absolute global maximum efficiency.

---

## 📂 Dataset & Physics Inputs

### 1. Oceanographic Data (The Environmental Bounds)

Archival data from **NOAA Station 41001 (East Hatteras)** establishes the real-world operational boundaries.

* **`41001h2023.txt`:** Significant Wave Height ($H_s$) and Dominant Period ($T_p$) used to derive the intake Air Velocity Probability Density Function ($V_{in}$).
* **`41001d2023.txt` & `41001w2023.txt`:** Mean Wave Direction and Spectral Density to validate energy frequency bands.

### 2. CFD Training Matrices (The "Ground Truth")

* **Solver:** Ansys Fluent, Pressure-Based, Steady-State.
* **Turbulence Model:** $k-\omega$ Shear Stress Transport (SST) to accurately capture boundary layer separation.
* **Profiles Evaluated:** NACA 0015 (Current), NACA 0018, NACA 0024.

---

## ⚙️ Methodology: The Sequential PINN-CFD Algorithm

The project executes optimization across four staggered phases to minimize computational load while maximizing design fidelity.

### Phase 1: 2D Aerodynamic Parametric Sweep

Isolating the pure aerodynamic profile (infinite span assumption) to evaluate lift and drag before and after stall.

* **Variables:** Angle of Attack ($AoA \in \{4^\circ, 8^\circ, 12^\circ, 17^\circ\}$), Inlet Velocity ($V_{in} \in \{6, 11, 15, 19\}$ m/s).
* **Execution:** 16 discrete 2D CFD simulations.
* **ML Output:** The PINN interpolates the $4 \times 4$ matrix to find the continuous optimal tuple $(AoA_{opt}, V_{in,opt})$.

### Phase 2: 3D Kinematic Optimization

Transitioning the optimal aerodynamic coordinates into a 3D rotating reference frame to capture tip leakage vortices and radial flow effects.

* **Variables:** Blade Speed ($RPM \in \{1000, 1300, 1700, 1900\}$), Hub Diameter ($D \in \{220, 240, 260, 300\}$ mm).
* **Execution:** 16 discrete 3D CFD simulations.
* **ML Output:** The PINN updates its gradient map to find the 4-variable optimal state $(AoA, V_{in}, RPM, D)_{opt}$.

### Phase 3: Assembly & Solidity Optimization

The central hub is introduced. The final geometric variable is the number of blades ($N$). The PINN evaluates the trade-off between increased torque generation and aerodynamic cascade blockage to finalize the rotor solidity ($\sigma$).

### Phase 4: Final Validation

A single, high-fidelity full-scale 3D CFD simulation is run using the PINN's final predicted parameters to validate the predicted efficiency gain against the standard baseline turbine.

---

## 💻 Tech Stack

* **Language:** Python 3.10+
* **Machine Learning:** TensorFlow / Keras (Physics-Informed Surrogate Modeling)
* **Optimization:** SciPy (`minimize_scalar`), Gradient Ascent
* **CFD Automation:** Ansys PyFluent (`ansys-fluent-core`)
* **Data Processing:** Pandas, NumPy
* **External Validation:** Ansys Workbench

---

## 🚀 Repository Structure & Execution

1. **Clone the Repo:**

```bash
git clone https://github.com/your-username/wells-turbine-pinn-optimization.git

```

2. **Install Dependencies:**

```bash
pip install numpy pandas matplotlib tensorflow scipy ansys-fluent-core

```

3. **Execution Flow:**

* `/data/`: Contains NOAA raw text files.
* `/cfd_automation/`: Python scripts to auto-generate the 16-run Ansys Fluent matrices.
* `/ml_models/`: Contains the PINN architectures for Phase 1 and Phase 2 interpolations.
* `main_pipeline.py`: The master script executing the optimization algorithm.

---

## 🔮 Current Progress & Future Work

1. **Environmental Modeling:** Complete. Oceanographic bounds have been successfully translated into CFD inlet velocity regimes.
2. **Current Phase:** The Phase 1 ($4 \times 4$) 2D aerodynamic sweep for the baseline NACA 0015 is currently computing.
3. **Future Scope:** Upon completion of the NACA 0015 full assembly, the identical automated pipeline will be deployed for thicker profiles (NACA 0018 and NACA 0024) to execute a comprehensive cross-profile comparative analysis.

---

### Authors

* **Aditya** * **Vishnunand** * **Karthikeya Siripuram** *This research is conducted as part of the Major Project for the National Institute of Technology Warangal (NITW).*
