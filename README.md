# Natural Convection in Transformer Oil – ONAN Cooling Simulation

![Temperature Contour](temp.gif)

This repository documents a **2D CFD study of natural convection in transformer oil** between a hot "core" wall and a cold "radiator" wall, representative of an ONAN (Oil Natural Air Natural) cooling mode in power transformers.

The objective is to analyze transient natural convection in an ONAN power transformer.
---

## 🔹 Simulation Setup

- **Solver**: ANSYS Fluent 2024 R1  
- **Solver Type**: Pressure-based, Transient, Laminar  
- **Gravity**: Enabled, **Y = −9.81 m/s²**  
- **Energy Equation**: Enabled  
- **Time Step**: 0.5s (CFL < 1), 2 iterations/step  
- **Transient Formulation**: Second-Order Implicit  
- **Spatial Discretization**: Second-order upwind for momentum & energy  
- **Initialization**: Hybrid (fluid initialized at ambient temperature)

---

## 🔹 Geometry and Named Selections

- **Core (Hot Wall)**: isothermal boundary condition at target winding temperature (e.g. 373 K / 100 °C).  
- **Radiator (Cold Wall)**: isothermal at ambient (e.g. 298 K / 25 °C).  
- **Walls**: adiabatic, no-slip boundaries.  

The fluid region (transformer oil) is bounded between the **Core** and **Radiator** with surrounding cavity walls.

---

## 🔹 Material Properties (Transformer Oil)

Properties defined at a **film temperature** (midpoint between hot and cold walls):

- **Density**: Boussinesq model  
  - ρ₀ = 850 kg/m³  
  - β = 8 × 10⁻⁴ K⁻¹  
  - T₀ = 333 K  
- **Cp**: 2000 J/kg·K  
- **Thermal Conductivity**: 0.12 W/m·K  
- **Viscosity μ(T)**: temperature-dependent  
  - 293 K → 0.035 Pa·s  
  - 313 K → 0.020 Pa·s  
  - 333 K → 0.012 Pa·s  
  - 353 K → 0.008 Pa·s  

---

## 🔹 Mesh Statistics

Generated in ANSYS Mesher with inflation layers at heated/cooled walls.

- **Nodes**: 9780  
- **Elements**: 9453  
- **Inflation Layers**: 10 layers, growth rate 1.2, smooth transition  

**Quality Metrics:**
- Aspect Ratio: Min 1.0, Max 22.1, Avg 7.22  
- Skewness: Min ~0, Max 0.73, Avg 0.18 (well below 0.9)  
- Orthogonal Quality: Min 0.42, Avg 0.93  

✅ Mesh is suitable for laminar natural convection.  
✅ Inflation resolves wall boundary layers.  
✅ Y⁺ values are **very low** (≪1) due to high viscosity of oil and fine near-wall spacing → good for resolving conduction-dominant near-wall regions without wall functions.

---

## 🔹 Target Dimensionless Numbers

**Assumptions (consistent with case setup):**
- **Height of cavity**: H ≈ 0.04 m (40 mm)  
- **Temperatures**: Th ≈ 100 °C (373 K), Tc ≈ 25 °C (298 K) → ΔT ≈ 75 K  
- **Oil properties at film Tf ≈ 335 K:**  
  ρ₀ ≈ 850 kg/m³, k ≈ 0.12 W/m·K, cp ≈ 2000 J/kg·K, μ ≈ 0.012 Pa·s, β ≈ 8×10⁻⁴ K⁻¹  
  → ν = μ/ρ₀ ≈ 1.4×10⁻⁵ m²/s,   α = k/(ρ₀·cp) ≈ 7.1×10⁻⁸ m²/s

**Derived targets:**
- **Prandtl**: Pr ≈ **200**  
- **Grashof**: Gr_H ≈ **1.9×10⁵**  
- **Rayleigh**: Ra_H ≈ **3.8×10⁷**  
- **Characteristic velocity**: U ≈ **0.01 m/s** (≈1 cm/s)  
- **Reynolds**: Re ≈ **30** (laminar)  
- **Nusselt (avg, vertical wall)**: Nu ≈ **30–70**  
- **Effective convection coefficient**: h ≈ **90–210 W/m²·K**
- 
---

## 🔹 Workflow Summary

1. **Pre-processing**
   - Geometry import  
   - Named selections: Core, Radiator, Walls  
   - Meshing with inflation layers  

2. **Physics Setup**
   - Pressure-based, transient laminar solver  
   - Energy ON, Boussinesq density, μ(T) defined  
   - Gravity enabled (Y = −9.81 m/s²)  

3. **Boundary Conditions**
   - Core: isothermal hot wall  
   - Radiator: isothermal cold wall  
   - Remaining walls: adiabatic, no-slip  

4. **Solution Controls**
   - URFs: p=0.3, m=0.5, e=1.0  
   - Transient, 0.5s Δt, 2 iterations/step  
   - Residual targets: 1e−8 (Energy), 1e−5 (others)  

5. **Monitoring**
   - Heat transfer rate at Core & Radiator (Q_core, Q_rad)  
   - Bulk oil temperature (mass-weighted)  
   - Convergence: |Q_core| ≈ |Q_rad|, T_bulk flat, residuals stable  

6. **Post-processing**
   - Temperature contours  
   - Velocity streamlines  
   - Local/average wall heat flux

---

## 🔹 Results

### Temperature Contour
![Temperature Contour](temp.gif)

### Velocity Vector Lines
![Velocity Vector lines](vectorvel.gif)

### Velocity Contour
![Velocity Contour](vel.gif)

---

## 🔹 Key Observations

- **Natural convection roll** forms with upward plume near Core and downward return near Radiator.  
- Bulk oil temperature rises and reaches quasi-steady state after ~700 time steps.  
- Convection circulation cell is observed between the core and the radiator section.

---

📌 *This document is prepared as a technical workflow summary for internal review and presentation.*
