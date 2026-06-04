# sscs-chipathon-2026-Track-B-Team-AgriSense
# Ultra-Low-Power Edge Sensor Interface for Drone-Assisted Crop Health Monitoring

**Chipathon 2026 – Track B**  
*Open-source mixed-signal ASIC for smart agriculture*

## Overview

This project designs a mixed-signal system-on-chip (SoC) for agricultural drones. The chip interfaces with external environmental sensors (temperature, humidity, gas, and ambient light) through a low-power analog front-end (AFE) and performs on‑chip inference to detect crop stress and environmental anomalies. By combining event‑driven sensing with a lightweight TinyML accelerator, the chip dramatically reduces wireless data transmission and total power consumption, enabling autonomous, long‑endurance drone missions.

The design is implemented in the **GlobalFoundries 180 nm (GF180)** mixed-signal CMOS process using an open-source EDA toolchain: **Xschem** for schematic capture, **ngspice** for circuit simulation, **Magic** and **Klayout** for layout, **Icarus Verilog** for digital simulation, and **OpenLane** for digital synthesis and place-and-route.

## Objectives

- **Sensor Interfacing:** Develop a configurable analog front-end capable of reading multiple sensor types (resistive, capacitive, voltage-output).
- **Ultra-Low Power:** Achieve sub‑mW active power and nanoampere sleep currents to maximise drone battery life.
- **Edge Intelligence:** Implement a TinyML inference engine that classifies crop health from raw sensor data without off‑chip processing.
- **Event-Driven Operation:** Wake up the digital core only when significant environmental changes are detected, minimising energy and radio usage.
- **Open-Source Tapeout:** Fully design, verify, and layout the chip with the Chipathon’s open-source tools, ready for fabrication.

## Target Specifications

| Parameter                   | Target Value                                      |
|-----------------------------|---------------------------------------------------|
| Technology                  | GlobalFoundries 180 nm (GF180)                    |
| Supply Voltage              | 1.8 V (core & analog), 3.3 V (I/O) optional       |
| Sensor Input Channels       | 4 differential / single-ended, multiplexed        |
| AFE Programmable Gain       | 1 – 100                                           |
| ADC Resolution & Rate       | 12‑bit SAR, up to 10 kSps                         |
| On‑Chip References          | Bandgap voltage reference, PTAT temperature sensor|
| Digital Processing Core     | RISC‑V (SERV) + custom TinyML accelerator         |
| SRAM                       | 16 KB for model weights and activations            |
| TinyML Model                | Quantised 1D‑CNN (approx. 5 KB parameters)        |
| Inference Latency           | < 10 ms                                           |
| Active Power (sensing+inference)| < 1 mW                                         |
| Deep‑Sleep Current          | < 100 nA                                          |
| Communication               | SPI slave, event‑triggered interrupt               |
| Chip Area                   | ≤ 2 mm² (est.)                                    |
| Operating Temperature       | -20 to 85 °C                                      |

## Block Diagram Description

1. **Analog Front‑End (AFE):**  
   - Low‑noise programmable gain instrumentation amplifier  
   - Multiplexer for sensor channel selection  
   - Anti‑alias filter and 12‑bit SAR ADC  
   - On‑chip bandgap reference and temperature sensor  

2. **Digital Core:**  
   - SERV RISC‑V CPU for control and light processing  
   - Custom TinyML accelerator: a systolic array of 8 multiply‑accumulate units for fast convolution  
   - 16 KB single‑port SRAM for model storage and intermediate activations  

3. **Power Management Unit:**  
   - Linear regulators for analog and digital supplies  
   - Event‑driven wake‑up comparator that monitors a selected sensor channel  
   - Power gating of digital domain during deep sleep  

4. **I/O & Communication:**  
   - SPI slave interface to drone main controller  
   - Dedicated interrupt pin to signal crop stress detection  
   - GPIO for sensor excitation control  

## EDA Tools & Workflow

- **Schematic & Analog Simulation:** Xschem, ngspice  
- **Analog Layout:** Magic, Klayout  
- **Digital RTL & Verification:** Icarus Verilog  
- **Digital Synthesis & P&R:** OpenLane (with Yosys, ABC, OpenROAD)  
- **Mixed‑Signal Integration:** Custom flow combining Magic/OpenLane GDS  
- **PDK:** GF180MCU open-source PDK  

All design files, testbenches, and documentation are version‑controlled in this repository.

## Repository Structure

