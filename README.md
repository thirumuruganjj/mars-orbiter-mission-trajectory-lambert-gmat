# Recreation of Mars Orbiter Mission (MOM) Trajectory

[![MATLAB](https://img.shields.io/badge/MATLAB-R2023b%2B-orange.svg)](https://www.mathworks.com/products/matlab.html)
[![GMAT](https://img.shields.io/badge/NASA-GMAT%20R2022a-blue.svg)](https://gmat.gsfc.nasa.gov/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Mission: ISRO MOM](https://img.shields.io/badge/Mission-ISRO%20Mangalyaan-red.svg)]()

> **Astrodynamics Analysis, Universal Variable Lambert Solver, $\Delta V$ Budgeting, and Numerical Trajectory Validation in NASA GMAT**

---

## 1. Overview

The **Mars Orbiter Mission (MOM)**, launched by ISRO on 5 November 2013, marked India's first interplanetary mission. This project reconstructs MOM's full three-phase orbital trajectory:
1. **Earth Orbit-Raising Phase** (26 days, multi-apogee burns).
2. **Heliocentric Interplanetary Transfer Arc** (297 days from 01 Dec 2013 to 24 Sep 2014).
3. **Mars Orbit Insertion (MOI)** into a highly elliptical $421 \times 76,993\text{ km}$ capture orbit.

The heliocentric transfer velocity vectors are computed in MATLAB via a **Universal Variable Lambert Solver** and validated by numerical integration in **NASA GSFC's General Mission Analysis Tool (GMAT)**.

---

## 2. Mission Timeline & $\Delta V$ Budget

| Phase | Epoch / Dates | Duration | Description | $\Delta V$ (m/s) |
| :--- | :---: | :---: | :--- | :---: |
| **Phase 1: Orbit Raising** | 05 Nov – 01 Dec 2013 | 26 days | Apogee-raising burns ($248 \times 23,550\text{ km} \rightarrow 600 \times 71,000\text{ km}$) | $311.48$ |
| **Phase 2: Trans-Mars Injection** | 01 Dec 2013 | — | Hyperbolic Earth escape ($v_{\infty,\text{dep}} = 2.809\text{ km/s}$) | $647.96$ |
| **Phase 3: Heliocentric Transfer** | 01 Dec 2013 – 24 Sep 2014 | 297 days | Keplerian interplanetary Lambert arc | — |
| **Phase 4: Mars Orbit Insertion** | 24 Sep 2014 | — | Retrograde capture burn into $e = 0.8997$ orbit | $852.77$ |
| **Total Mission** | **05 Nov 2013 – 24 Sep 2014** | **323 days** | **Cumulative $\Delta V$ Budget** | **$1812.21\text{ m/s}$** |

---

## 3. Astrodynamics Methodology

### 1. Vis-Viva Formulation for Earth Orbit-Raising
$$\Delta v_{\text{raise}} = v_{\text{pre-TMI},p} - v_{\text{park},p} = \sqrt{\mu_E \left(\frac{2}{r_p} - \frac{1}{a_{\text{pre-TMI}}}\right)} - \sqrt{\mu_E \left(\frac{2}{r_p} - \frac{1}{a_{\text{park}}}\right)} = 311.48\text{ m/s}$$

### 2. Universal Variable Lambert Solver
Given boundary vectors $\mathbf{r}_1$ (Earth, 01 Dec 2013) and $\mathbf{r}_2$ (Mars, 24 Sep 2014) with $\text{TOF} = 297\text{ days}$, the transfer orbit is solved using Stumpff functions $C(z)$ and $S(z)$:
$$C(z) = \frac{1 - \cos\sqrt{z}}{z}, \quad S(z) = \frac{\sqrt{z} - \sin\sqrt{z}}{(\sqrt{z})^3}$$
The iterative Newton-Raphson solver converges to:
* Earth departure velocity: $\mathbf{v}_1 \implies |\mathbf{v}_1| = 32.650\text{ km/s}, \quad v_{\infty,\text{dep}} = 2.809\text{ km/s}$
* Mars arrival velocity: $\mathbf{v}_2 \implies |\mathbf{v}_2| = 22.642\text{ km/s}, \quad v_{\infty,\text{arr}} = 3.130\text{ km/s}$

### 3. Tsiolkovsky Rocket Equation & Propellant Budget
Sequential mass calculation for MOM's $440\text{ N}$ Liquid Apogee Motor ($I_{\text{sp}} = 308\text{ s}$, initial mass $m_0 = 1337\text{ kg}$):
$$m_f = m_0 \exp\left(-\frac{\Delta v}{I_{\text{sp}} g_0}\right)$$
* **Post-Orbit Raising Mass ($m_1$):** $1206.0\text{ kg}$ (Propellant: $131.0\text{ kg}$)
* **Post-TMI Mass ($m_2$):** $973.1\text{ kg}$ (Propellant: $232.8\text{ kg}$)
* **Post-MOI Mass ($m_3$):** $733.8\text{ kg}$ (Propellant: $239.4\text{ kg}$)
* **Total Propellant Consumed:** $\mathbf{603.2\text{ kg}}$ ($45.1\%$ of launch mass)
* **Margin over 500 kg Dry Mass:** $\mathbf{+233.8\text{ kg}}$

---

## 4. NASA GMAT Numerical Validation

The MATLAB state vector was fed into GMAT under the `SunMJ2000Eq` heliocentric frame:
$$\mathbf{r}_0 = \begin{bmatrix} 53,551,431.598 & 137,455,945.927 & 0.0 \end{bmatrix}^T\text{ km}$$
$$\mathbf{v}_0 = \begin{bmatrix} -31.009245 & 10.220033 & 0.0 \end{bmatrix}^T\text{ km/s}$$
Propagating via **Runge-Kutta 89 (RK89)** over 297 days terminated on **24 September 2014**, validating the analytical Lambert solution against high-precision multi-body numerical integration.

---

## 5. Repository Structure
