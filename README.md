# 📈 STM32 Bare-Metal Digital Storage Oscilloscope (DSO)

![C](https://img.shields.io/badge/Language-C-blue.svg)
![MCU](https://img.shields.io/badge/MCU-STM32F103C8T6-red.svg)
![Architecture](https://img.shields.io/badge/Architecture-ARM%20Cortex--M3-orange.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)

A high-performance, real-time **Digital Storage Oscilloscope (DSO)** firmware written in bare-metal C for the **ch32F103** (ARM Cortex-M3) microcontroller. This project demonstrates zero-CPU-overhead data acquisition using hardware Timers, ADC, and DMA, rendering real-time waveforms and electrical metrics on a 320x240 ILI9341 TFT LCD.

---

## 🌟 Key Features

- **🚀 High-Speed Hardware Data Acquisition:**
  - Uses **TIM3 (TRGO)** to trigger **ADC1** hardware conversions at precise sample rates.
  - **DMA1 Channel 1** handles memory transfers directly to RAM (`1600 samples buffer`), eliminating CPU overhead during acquisition.
- **⏱️ Flexible Timebase Engine:**
  - 12 selectable timebase ranges from **$20\mu s/\text{div}$ up to $50ms/\text{div}$**.
  - Dynamic sample rate adjustment via Timer prescalers and ADC sample cycles.
- **⚡ Advanced Triggering System:**
  - **Modes:** `AUTO` and `NORMAL`.
  - **Slopes:** `Rising Edge` and `Falling Edge`.
  - **Controls:** Adjustable Trigger Level (via TIM4 PWM output) and Horizontal Trigger Position ($X\text{-Pos}$).
- **🤖 Smart Auto-Set Algorithm:**
  - Measures peak-to-peak voltage and signal period in under **500ms**.
  - Automatically calculates optimal Timebase, $Y\text{-Offset}$, and Trigger Level for unknown incoming signals.
- **📊 Real-Time Signal Processing & OSD Metrics:**
  - On-Screen Display inside the scope grid for: $V_{max}$, $V_{min}$, $V_{pp}$, $V_{dc}$, and $V_{rms}$.
  - Real-time frequency counter with automatic scaling ($\text{Hz}$, $\text{kHz}$, $\text{MHz}$).
  - Built-in **IIR Moving Average Filter** to smooth out display metrics and eliminate digit flickering.
- **🛠️ Hardware Switch Integration:**
  - Full support for physical DSO138 switches: Coupling (`AC` / `DC` / `GND`) and Attenuation (`X1` / `X2` / `X5`).
- **📟 Test Signal Generator:**
  - Built-in $1\text{kHz}$ square wave output generated via **TIM2** on pin `PA7` for probe calibration.

---

## 🏗️ Hardware Architecture & Peripherals

| Peripheral | Function / Assignment | Description |
| :--- | :--- | :--- |
| **ADC1 (CH0)** | Analog Signal Input (`PA0`) | Reads conditioned analog signal |
| **DMA1 (CH1)** | Direct Memory Access | Transfers ADC samples to `adc_buffer[1600]` |
| **TIM3** | ADC Trigger (`TRGO`) | Master sample clock generator |
| **TIM2** | Test Signal Generator | Outputs $1\text{kHz}$ Square Wave on `PA7` |
| **TIM4 (CH3/4)**| PWM Trigger Voltage | Generates DC reference for Op-Amp Trigger Hardware |
| **ILI9341 LCD**| 8-Bit Parallel Interface | 320x240 RGB565 Display rendering Scope UI |
| **GPIO Inputs**| Physical Buttons (`PB12-PB15`)| Debounced push buttons for menu control |

---

## 🕹️ Control & Menu System

The UI features a top-bar header, custom vector icons, bottom status bar, and a $300 \times 200$ grid area.

### Push-Button Mapping
- **SW1 (`PB12`) — MENU SELECT:** Cycles through adjustable parameters ($Y\text{-Offset} \rightarrow \text{Timebase} \rightarrow \text{Trigger Slope} \rightarrow \text{Trigger X-Pos} \rightarrow \text{Trigger Mode} \rightarrow \text{Trigger Level}$).
- **SW2 (`PB14`) — UP / INCREMENT (+):** Increases the selected parameter value.
- **SW3 (`PB13`) — DOWN / DECREMENT (-):** Decreases the selected parameter value.
- **SW4 (`PB15`) — MULTI-FUNCTION:**
  - **Short Tap:** Toggles **HOLD / RUN** state (Freezes waveform).
  - **Long Press ($>500\text{ms}$):** Triggers **AUTO-SET** mode.

---

## 📐 Signal Processing Details

### 1. RMS Voltage ($V_{rms}$) Calculation
To prevent integer overflow during 12-bit ADC squaring:
$$\text{SumSquares} = \sum_{i=0}^{N-1} (V_{\text{adc}}[i] - V_{\text{GND}})^2$$
$$V_{rms} = \left( \frac{\sqrt{\frac{\text{SumSquares}}{N}}}{98.0} \right) \times V_{\text{div\_scale}}$$

### 2. Display Smoothing (IIR Filter)
$$y[n] = 0.8 \cdot y[n-1] + 0.2 \cdot x[n]$$
Applied to all voltage metrics ($V_{max}, V_{min}, V_{pp}, V_{dc}, V_{rms}$) and frequency measurements to ensure stable display readouts without lag.

---

## 🛠️ How  Flash

   1. Flash to ch32F103 target using ST-Link .

---

## 📸 Screenshots / Demo

*(Add photos or GIFs of your working scope display here)*
