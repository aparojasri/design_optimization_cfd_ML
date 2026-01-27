

# Stochastic Optimization of a Wells Turbine for North Atlantic Sea States

### A Physics-Informed Surrogate Model Approach to OWC Wave Energy Conversion

---

## 🌊 Project Overview

The **Wells Turbine** is the standard Power Take-Off (PTO) mechanism for Oscillating Water Column (OWC) wave energy converters. However, standard blade geometries (e.g., NACA 0015) suffer from **early aerodynamic stall** under the high-velocity, stochastic flow conditions characteristic of energetic ocean sites. This results in significant power capping and structural vibration during storms—precisely when energy potential is highest.

This project develops a **Site-Specific Geometric Optimization Framework**. Instead of relying on generic "textbook" blade shapes, we built a **Physics-Informed Neural Network (PINN)** surrogate model coupled with **16,759 hours of real spectral ocean data** to design a blade specifically tuned for the North Atlantic wave climate.

---

## 🎯 Problem Statement

* **The Limitation:** Standard symmetrical airfoils (NACA 0015) stall at relatively low Angles of Attack (~12-14°).
* **The Environment:** Real ocean waves are stochastic. NOAA Station 41001 data reveals air velocities in the OWC chamber frequently exceed 40 m/s, pushing turbines deep into the stall regime.
* **The Solution:** We utilize Generative AI to explore the non-linear design space of airfoil thickness (), balancing low-speed viscous efficiency against high-speed stall delay to maximize **Annual Energy Production (AEP)**.

---

## 📂 Dataset & Physics Inputs

### 1. Oceanographic Data (The "Fuel")

We utilized archival data from **NOAA Station 41001 (East Hatteras)** as a high-energy reference site.

* **`41001h2023.txt`:** Significant Wave Height () and Dominant Period (). Used to derive the intake Air Velocity Probability Density Function (PDF).
* **`41001d2023.txt`:** Mean Wave Direction (). Used to determine optimal chamber orientation via Wave Rose analysis.
* **`41001w2023.txt`:** Spectral Density (). Validates the energy frequency bands.

### 2. Aerodynamic Data (The "Mechanism")

* **Ground Truth:** 2D RANS CFD simulations (Ansys Fluent, k-omega SST model).
* **Training Data:** Synthetic physics generation based on NACA 4-series equations for initial training, calibrated via Transfer Learning with lab-generated Polars.
* **Geometries:** NACA 0010, 0015, 0018, 0024 digitized coordinates.

---

## ⚙️ Methodology: The "Digital Twin" Pipeline

The project follows a 4-stage scientific pipeline:

### Phase 1: Environmental Characterization

We parsed raw meteorological files to calculate the **Pneumatic Power Available**.



We derived the **Air Velocity Distribution** inside the chamber using the Piston-Flow assumption with an area ratio of 75:1.

### Phase 2: Surrogate Modeling (The AI)

Training a Deep Neural Network (DNN) to act as a real-time aerodynamic solver.

* **Inputs:** Reynolds Number (), Angle of Attack (), Thickness Ratio ().
* **Outputs:** Lift (), Drag (), Moment ().
* **Advantage:** The AI predicts performance in milliseconds, whereas CFD takes hours, allowing us to test thousands of blade shapes.

### Phase 3: Stochastic Optimization

We utilized a bounded minimization algorithm (`scipy.optimize`) to maximize AEP.

* **Objective Function:** 
* **Variable:** Blade Thickness ().
* **Constraint:** Structural solidity limits (12% to 30%).

### Phase 4: Validation (The "Victory Lap")

The AI-optimized design is validated against the industry standard (NACA 0015) by plotting efficiency curves across the full operational range (0–50 m/s).

---

## 📊 Key Results

* **Optimal Geometry:** The algorithm converged on a non-standard thickness of **~24.1%** (approx. NACA 0024).
* **Performance Gain:** The optimized blade delays stall onset by approximately **4 degrees** compared to the NACA 0015.
* **Energy Capture:** Significant efficiency gains were observed in the "Storm Regime" ( m/s), where the standard turbine typically chokes.

---

## 🧪 Nomenclature & Parameters

| Symbol | Parameter | Role |
| --- | --- | --- |
| **** | Significant Wave Height | Primary energy input metric. |
| **** | Dominant Wave Period | Determines flow frequency. |
| **** | Angle of Attack | Key aerodynamic variable; determines stall. |
| **** | Reynolds Number | Ratio of inertial to viscous forces (). |
| **** | Thickness-to-Chord Ratio | **The Optimization Variable.** |
| **** | Lift & Drag Coefficients | Performance metrics from CFD/AI. |
| **AEP** | Annual Energy Production | The final success metric. |

---

## 💻 Tech Stack

* **Language:** Python 3.10+
* **Machine Learning:** TensorFlow / Keras (Surrogate Modeling)
* **Optimization:** SciPy (`minimize_scalar`)
* **Data Processing:** Pandas, NumPy
* **Visualization:** Matplotlib, Seaborn
* **External Validation:** Ansys Fluent (CFD)

---

## 🚀 How to Run

1. **Clone the Repo:**
```bash
git clone https://github.com/your-username/wells-turbine-optimization.git

```


2. **Install Dependencies:**
```bash
pip install numpy pandas matplotlib tensorflow scipy scikit-learn

```


3. **Add Data:**
Place the NOAA text files (`41001h2023.txt`, etc.) in the `/data` directory.
4. **Run the Genesis Script:**
```bash
python main_optimization.py

```



---

## 🔮 Future Work

1. **Lab Calibration:** Current "Transfer Learning" protocols will be updated with fresh experimental data from the university wind tunnel to correct AI bias.
2. **3D Effects:** Analytical corrections for Aspect Ratio () and Tip Loss are currently applied; full 3D CFD validation is scheduled for the final optimized geometry.
3. **Guide Vane Integration:** Future optimization will include the pitch angle of Inlet Guide Vanes (IGVs).

---

### Authors

* **[Aditya, Vishnunand & Karthikeya Siripuram]** 


*This project was conducted as part of the Major Project for [NITW].*
