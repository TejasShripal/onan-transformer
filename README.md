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
- **Time Step**: 0.02–0.05 s (CFL < 1), 30 iterations/step  
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
- **Height of cavity** \(H \approx 0.04\ \text{m}\) (40 mm)  
- **Temperatures**: \(T_h \approx 100^\circ\text{C}\) (373 K), \(T_c \approx 25^\circ\text{C}\) (298 K) → \(\Delta T \approx 75\ \text{K}\)  
- **Oil properties at film \(T_f \approx 335\ \text{K}\):**  
  \(\rho_0 \approx 850\ \text{kg/m}^3,\; k \approx 0.12\ \text{W/m·K},\; c_p \approx 2000\ \text{J/kg·K},\; \mu \approx 0.012\ \text{Pa·s},\; \beta \approx 8\times10^{-4}\ \text{K}^{-1}\)  
  → \(\nu=\mu/\rho_0\approx 1.4\times10^{-5}\,\text{m}^2/\text{s},\;\; \alpha=k/(\rho_0 c_p)\approx 7.1\times10^{-8}\,\text{m}^2/\text{s}\)

**Derived targets:**
- **Prandtl**: \(Pr \approx \mathbf{200}\)  
- **Grashof**: \(Gr_H \approx \mathbf{1.9\times10^5}\)  
- **Rayleigh**: \(Ra_H \approx \mathbf{3.8\times10^7}\)  
- **Characteristic velocity**: \(U \approx \mathbf{0.01\ \text{m/s}}\) (≈1 cm/s)  
- **Reynolds**: \(Re \approx \mathbf{30}\) (laminar)  
- **Nusselt (avg, vertical wall)**: \(\overline{Nu} \approx \mathbf{30–70}\)  
- **Effective convection coefficient**: \(\overline{h} \approx \mathbf{90–210\ \text{W/m}^2\!\cdot\!\text{K}}\)

**Interpretation:**  
High \(Pr\) and \(Ra\sim10^{7}\) with \(Re\sim30\) indicate a **laminar natural-convection roll**, as expected for ONAN cooling. The oil-side \(h\) in the order of \(10^2\ \text{W/m}^2\!\cdot\!\text{K}\) is realistic for transformer operation.

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
   - Transient, 0.02–0.05 s Δt, 30 iterations/step  
   - Residual targets: 1e−8 (Energy), 1e−5 (others)  

5. **Monitoring**
   - Heat transfer rate at Core & Radiator (Q_core, Q_rad)  
   - Bulk oil temperature (mass-weighted)  
   - Convergence: |Q_core| ≈ |Q_rad|, T_bulk flat, residuals stable  

6. **Post-processing**
   - Temperature contours  
   - Velocity streamlines  
   - Local/average wall heat flux  
   - Nusselt number distribution  
   - Effective convection coefficient h  

---

## 🔹 Results

### Temperature Contour
![Temperature Contour](temperature_contour.gif)

### Velocity Vector Lines
![Velocity Vector lines](vectorvel.gif)

### Velocity Contour
![Velocity Contour](vel.gif)

---

## 🔹 Key Observations

- **Natural convection roll** forms with upward plume near Core and downward return near Radiator.  
- Bulk oil temperature rises and reaches quasi-steady state after ~3000 time steps.  
- Energy balance check: |Q_core| ≈ |Q_rad| (within <1%).  
- Average convection coefficient **h ≈ XX W/m²·K** (to be filled with CFD result).  
- Corresponding Nusselt number **Nu ≈ YY** (dimensionless validation).

---

## 🔹 Next Steps

- Extend to **Conjugate Heat Transfer (CHT)** with solid fin and external air.  
- Include **radiation** for more realistic ONAN performance.  
- Compare CFD-derived h and Nu with **Churchill–Chu correlations** for validation.

---

📌 *This document is prepared as a technical workflow summary for internal review and presentation.*
