# 🌞 Single-Phase Grid-Connected PV Inverter

This repository contains the **firmware, algorithms, and design resources** for a **single-stage grid-connected photovoltaic (PV) inverter**.  
The system is built on the **TI C2000 TMS320F28379D** microcontroller and integrates **advanced digital control techniques** for real-time operation.

---

## 🔎 Project Overview

The goal of this project is to design and implement a **single-phase, single-stage inverter** that:  
- Converts PV DC power into synchronized AC power.  
- Injects sinusoidal current into the grid with low harmonic distortion.  
- Maximizes PV efficiency using **MPPT**.  
- Synchronizes to the utility grid using **SOGI-PLL**.  
- Uses advanced control strategies (**PI and PR controllers**) to achieve stability and compliance.  

This work is part of an **academic research project**, and the complete source code will be released after related papers are published.

---

## ⚙️ Features

- 🔋 **MPPT (P&O & Incremental Conductance)** for maximizing PV power.  
- 🎛 **PI Controller** for DC bus voltage regulation.  
- 🎚 **PR Controller** for sinusoidal current regulation with low THD.  
- 🔗 **SOGI-PLL** for robust grid synchronization.  
- 📐 **Unipolar SPWM** generation with duty clamping and dead-time.  
- 🛡 Built-in safety: anti-windup, current limits, grid protection.  
- 📊 DAC outputs for debugging PLL angle, sine references, and internal variables.  

---

## 📚 Algorithm Explanations

### 1️⃣ Maximum Power Point Tracking (MPPT)
The MPPT algorithm ensures that the PV system always operates at the voltage/current point where **power output is maximized**.  

- **Perturb & Observe (P&O):**  
  - Slightly perturb PV voltage and observe the power change.  
  - If power increases, keep the same direction; otherwise, reverse.  
  - Simple and effective, but oscillates around MPP.  

- **Incremental Conductance (IncCond):**  
  - Uses derivative condition: `dP/dV = 0` at MPP.  
  - Calculates slope of PV curve in real-time.  
  - Better under rapidly changing irradiance compared to P&O.  

---

### 2️⃣ PI Controller
The PI controller regulates **slow loops**, such as the DC bus voltage.  

- **Proportional term (Kp):** Responds to error magnitude.  
- **Integral term (Ki):** Eliminates steady-state error.  
- **Anti-windup & saturation:** Prevents instability when limits are reached.  

---

### 3️⃣ SOGI-PLL (Single-Phase Grid Synchronization)
The SOGI-PLL estimates the **grid angle (θ) and frequency**, essential for synchronization.  

- **SOGI (Second-Order Generalized Integrator):** Creates orthogonal signals (α, β) from single-phase grid input.  
- **PLL Loop:** A PI controller processes the phase error between estimated signals and the grid to lock on frequency and phase.  
- **Output:** Provides real-time `θ_est` for generating sinusoidal references.  

---

### 4️⃣ PR Controller (Proportional-Resonant)
The PR controller is used for **AC current regulation**.  

- Provides **infinite gain at the fundamental frequency** (50/60 Hz).  
- Ensures **zero steady-state error** for sinusoidal reference tracking.  
- Can be extended with additional resonant terms to suppress harmonics (3rd, 5th, etc.).  

---

### 5️⃣ SPWM (Sinusoidal Pulse Width Modulation)
The inverter uses **unipolar SPWM** to synthesize the AC waveform.  

- **Reference signal:** Sinusoidal wave generated from PLL angle.  
- **Carrier signal:** High-frequency triangular waveform.  
- **Comparison:** Generates duty cycles for ePWM.  
- **Advantages of unipolar SPWM:** Lower harmonic distortion and reduced switching losses compared to bipolar SPWM.  

---

## 🛠 Step-by-Step Implementation

1. **PWM Bring-Up**  
   - Configure ePWM modules for unipolar SPWM.  
   - Verify duty cycle outputs on oscilloscope.  

2. **Grid Synchronization**  
   - Implement SOGI filter and PLL loop.  
   - Check that PLL angle tracks grid voltage (use DAC to observe).  

3. **Current Control with PR**  
   - Generate sinusoidal reference `i_ref = Imax·sin(θ)`.  
   - Use PR controller to regulate inverter current.  

4. **MPPT Integration**  
   - Run MPPT algorithm periodically (every 50–200 ms).  
   - Adjust reference power/voltage to maximize PV output.  

5. **System Integration & Safety**  
   - Enable grid injection only after PLL lock.  
   - Implement protections: OV/UV, OF/UF, anti-islanding.  

---

## 📂 Repository Structure

