# Ultra-Low-Power Edge AI Sensor Interface for Drone Crop Monitoring

## Overview

This project delivers a mixed‑signal system‑on‑chip (SoC) that serves as the intelligent sensor payload for agricultural drones.  
The chip interfaces with external environmental sensors (temperature, humidity, gas, ambient light) through a low‑power analog front‑end (AFE) and performs **on‑chip crop‑stress inference** using a TinyML accelerator.  
By combining event‑driven sensing with local edge AI, the chip eliminates continuous wireless raw‑data streaming and enables autonomous, long‑endurance drone missions.

**The design targets the Chipathon’s open‑source GF180 nm process and fits within a Block B (1/8) pad frame (0.625 mm², ≤16 pins).**

## System Architecture & Value of a Custom Chip

Our chip is **not** the drone flight controller — it’s a dedicated payload co‑processor that handles the entire *sensor‑to‑decision* pipeline.  
In a typical discrete implementation, this pipeline requires 8‑12 separate ICs (op‑amps, ADC, reference, MCU, memory, power management) and draws 3‑10 mW continuously. By integrating all these functions into a single GF180 die, we achieve:

| Metric | Discrete solution | Our chip | Improvement |
|--------|-------------------|-----------|-------------|
| IC count | 8‑12 | 1 | Only 1 IC required|
| Active power (sensing + inference) | 3‑10 mW | <1 mW | Less power requirements, longer battery life |
| Sleep current | 1‑10 µA | <100 nA | ~10‑100× lower sleep current |
| PCB area | 4‑8 cm² | ~1 cm² (chip + few passives) | ~4‑8× less area requirements|
| Weight | 2‑5 g | <0.5 g | ~4‑10× lighter|

**These savings cascade!** 
A lighter sensor board allows a smaller frame, smaller motors, and a smaller battery — extending flight time and reducing overall system cost.

The chip communicates with the drone’s main flight controller over **SPI**. The flight controller already handles inertial measurement (IMU/gyro) and connects to the LoRa/WiFi radio; our chip simply pushes crop‑stress alerts when an event is detected, keeping the radio off otherwise.

<img width="860" height="300" alt="image" src="https://github.com/user-attachments/assets/63375c43-2d8b-4418-ab90-a73171c635a5" />


*No radio or gyroscope is included on‑chip — the chip is purely the sensor‑AI edge node.*

## Key Objectives

- **Sensor Interfacing:** Configurable AFE with on‑chip multiplexer that reads resistive, capacitive, and voltage‑output sensors.
- **Ultra‑Low Power:** <1 mW active, <100 nA deep‑sleep (event‑driven wake‑up).
- **Edge Intelligence:** SERV RISC‑V core + custom TinyML accelerator (systolic array) running a quantised 1D‑CNN for crop stress classification.
- **Event‑Driven Operation:** On‑chip wake‑up comparator triggers inference only when a sensor channel crosses a programmed threshold; otherwise the digital core is power‑gated.
- **Chipathon Compliance:** Fully open‑source toolchain, fits in Block B (1/8) with 14–16 pins.

## Target Specifications

| Parameter                | Target value                                      |
|--------------------------|---------------------------------------------------|
| **Technology**           | GlobalFoundries 180 nm (GF180MCU)                  |
| **Block size**           | Block B (1/8) – pad area ≤ 0.625 mm², core ~0.58 mm² |
| **Supply voltage**       | 1.8 V (analog & digital)                           |
| **Analog inputs**        | 2 differential channels (multiplexed to 4 sensors) |
| **AFE programmable gain**| 1‑100                                             |
| **ADC**                  | 12‑bit SAR, 10 kSps, integrated anti‑alias filter  |
| **On‑chip references**   | Bandgap voltage reference, PTAT temperature sensor |
| **Digital core**         | SERV RISC‑V (RV32I) + TinyML accelerator (8 MAC systolic array) |
| **On‑chip SRAM**         | 2 KB (activation buffer & scratchpad)              |
| **CNN weights storage**  | Off‑chip SPI flash (loaded into accelerator cache at boot / on‑demand) |
| **Inference latency**    | <10 ms per inference (weight streaming overhead included) |
| **Active power**         | <1 mW (full cycle: sensor read + inference)        |
| **Deep‑sleep current**   | <100 nA (analog wake‑up comparator active)         |
| **Communication**        | SPI slave to flight controller; dedicated interrupt pin for alerts |
| **Operating temperature**| -20 °C to +85 °C                                   |


1. **Analog Front‑End (AFE):**  
   - 4‑channel multiplexer (selecting from temperature, humidity, gas, optical sensors).  
   - Low‑noise programmable‑gain instrumentation amplifier (gain 1‑100).  
   - Anti‑alias filter and 12‑bit SAR ADC.  
   - Integrated bandgap reference and PTAT temperature sensor for calibration.

2. **Power Management:**  
   - Separate linear regulators for analog and digital supplies (1.8 V).  
   - Ultra‑low‑power wake‑up comparator continuously monitors one selected sensor channel; when the signal crosses a configurable threshold, it re‑enables the digital core.  
   - Power gating on the digital domain during deep sleep.

3. **Digital Core:**  
   - **SERV RISC‑V CPU:** Manages sensor acquisition, accelerator control, and SPI communication.  
   - **TinyML Accelerator:** 8‑element systolic MAC array for 1D convolution operations required by the crop‑stress CNN. Weights are fetched from an external SPI flash using a simple streaming protocol, minimising on‑chip memory.  
   - **2 KB SRAM:** Holds input sensor vectors, intermediate activations, and the final classification result.  
   - **SPI slave:** Allows the flight controller to read inference results and to configure thresholds/algorithms.

4. **I/O & Communication:**  
   - **SPI (4 pins):** Slave interface to drone flight controller (SCLK, MOSI, MISO, CS).  
   - **Interrupt (1 pin):** Asserted when crop stress is detected.  
   - **Reset (1 pin):** Active‑low hardware reset.  
   - **Analog inputs (2 pins):** Differential inputs from the sensor MUX (multiplexer control can be performed via GPIOs or an external pin‑strap, but to save pins the MUX is configured over SPI).  
   - **Power/Ground (4 pins):** VDD_A, VDD_D, GND_A, GND_D – separate supplies to isolate analog and digital noise.  
   - **Spare (2 pins):** Reserved for future I/O expansion (e.g., sensor excitation control).

## Pinout (Block B – 14 of 16 pads used)

| Pad # | Name    | Type      | Description                          |
|-------|---------|-----------|--------------------------------------|
| 1     | VDD_A   | Power     | Analog supply (1.8 V)                |
| 2     | AIN_P   | Analog In | Sensor positive input                |
| 3     | AIN_N   | Analog In | Sensor negative input                |
| 4     | VDD_D   | Power     | Digital supply (1.8 V)               |
| 5     | SCLK    | Digital In| SPI clock                            |
| 6     | MOSI    | Digital In| SPI data (flight controller → chip)  |
| 7     | MISO    | Digital Out| SPI data (chip → flight controller) |
| 8     | CS      | Digital In| SPI chip select                      |
| 9     | INT     | Digital Out| Interrupt (crop stress alert)       |
| 10    | RST     | Digital In| Active‑low reset                     |
| 11    | GND_D   | Ground    | Digital ground                       |
| 12    | GND_A   | Ground    | Analog ground                        |
| 13    | SPARE1  | I/O       | Reserved                             |
| 14    | SPARE2  | I/O       | Reserved                             |

## EDA Tools & Design Flow

All tools are open‑source and form part of the Chipathon’s approved flow:

| Task                       | Tool                                 |
|----------------------------|--------------------------------------|
| Schematic capture (analog) | Xschem                               |
| Circuit simulation         | ngspice                              |
| Analog layout              | Magic, Klayout                       |
| Digital RTL design         | Icarus Verilog (simulation), Yosys (synthesis) |
| Digital place‑and‑route    | LibreLane (OpenROAD, ABC, Yosys)      |
| Mixed‑signal integration   | Custom scripts merging Magic & LibreLane GDS |
| DRC/LVS/ERC                | Magic, Netgen                        |
| PDK                        | GF180MCU open‑source PDK             |

**Block integration:**  
We will target the Chipathon’s **Block B (1/8, 16‑pad ring)**. The design will incorporate the supplied padframe template and will include secondary ESD protection on all analog I/Os (as recommended for the Chipathon analog group project).

## Repository Structure



