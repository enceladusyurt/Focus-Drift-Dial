# Focus Drift Dial

*A Physical Gauge for Externalizing Attention Drift*

## 1. Project Overview

**Focus Drift Dial** is a two-device system that externalizes attention drift through a calm, physical display.

Instead of interrupting users with alerts or notifications, a slowly drifting gauge needle reflects changes in focus level derived from physiological signals, making attention shifts perceptible but non-intrusive.

**Physical form:**

- A **wearable sensing device** worn on the wrist, resembling a minimal smart band.
- A **desktop display device** with a semi-circular gauge, a stepper-motor-driven needle, a status LED, and a button for calibration.

![image.png](Focus%20Drift%20Dial/image.png)

## 2. Sensor Device (Wearable)

### Function

The sensing device continuously captures heart-related signals and extracts features related to attention and cognitive load.

### Sensors & Components (with part numbers)

- **PPG sensor:** MAX30102
    - Measures heart rate and pulse waveform
    - Used to compute HR and HRV-related features
- **Microcontroller:** Seeed XIAO ESP32-C3
    - BLE-capable
    - Low power consumption
- **Battery:** 120–200 mAh LiPo battery
- **Power management:** MCP73831 LiPo charging IC (or equivalent)

### How it works

The PPG sensor collects pulse waveform data.

On-device signal processing extracts:

- ΔHR (heart rate change)
- ΔHRV (short-term heart rate variability proxy)

A lightweight normalization and weighting function is applied:

```
A = clip(0.55·σ(ΔHR) +0.45·σ(ΔHRV),0,1)
```

The resulting attention index **A** is transmitted periodically to the display device via BLE.

## 3. Display Device (Desktop Gauge)

### Function

The display device transforms the abstract attention index into a slow, physical movement that users can perceive at a glance.

### Components (with part numbers)

- **Microcontroller:** Seeed XIAO ESP32-C3
- **Stepper motor:** 28BYJ-48 + ULN2003 driver
- **LED:** WS2812B (single pixel for state indication)
- **Button:** Tactile push button (for calibration / reset)
- **Battery:** 500–800 mAh LiPo battery

### How it works

The display device receives the attention index **A** via BLE and maps it to a needle angle:

- Calm: A < 0.33
- Engaged: 0.33 ≤ A < 0.66
- Stressed / distracted: A ≥ 0.66

The stepper motor moves the needle **slowly and continuously**, avoiding sudden jumps.

The LED provides subtle feedback (e.g., breathing-like glow when engaged).

## 4. System Communication & Data Flow Diagrams

### Figure 1: System-level communication diagram

- PPG sensor → MCU (sensor device)
- Feature extraction → BLE transmission
- BLE reception → MCU (display device)
- Stepper motor + LED actuation

![figure1.jpg](Focus%20Drift%20Dial/figure1.jpg)

### Figure 2: Data processing pipeline

1. PPG signal acquisition
2. Feature extraction (ΔHR, ΔHRV proxy)
3. Normalization and weighted fusion
4. Attention state mapping
5. Physical needle movement

This split-processing architecture balances **responsiveness, interpretability, and power efficiency**.

![figure2.jpg](Focus%20Drift%20Dial/figure2.jpg)