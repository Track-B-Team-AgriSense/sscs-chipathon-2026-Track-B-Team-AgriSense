# sscs-chipathon-2026-Track-B-Team-AgriSense
# Ultra-Low-Power Edge Sensor Interface for Drone-Assisted Crop Health Monitoring

**Chipathon 2026 – Track B**  
*Open-source mixed-signal ASIC for smart agriculture*

![Chipathon 2026](https://img.shields.io/badge/Chipathon-2026-blue) ![GF180](https://img.shields.io/badge/Process-GF180MCU-green) ![License](https://img.shields.io/badge/License-Apache--2.0-yellow)

## Overview

This project develops an **ultra-low-power mixed-signal edge sensing chip** tailored for agricultural drones used in crop health and environmental monitoring. The chip directly interfaces with multiple sensors:

- 🌡️ **Temperature** (e.g., thermistor, RTD)
- 💧 **Humidity** (capacitive or resistive)
- 🌫️ **Gas sensors** (e.g., CO₂, ethylene, NH₃ for stress detection)
- ☀️ **Optical sensors** (NDVI, reflectance, ambient light)

An **analog front-end (AFE)** performs signal conditioning and on-chip feature extraction (e.g., peak detection, slope calculation, FFT bins). A lightweight **digital processing block** implements event-driven sensing and **TinyML-inspired inference** to detect crop stress (e.g., water deficiency, pest infestation, nutrient imbalance) and environmental anomalies.

By processing data locally at the edge, the chip drastically reduces wireless transmission overhead and power consumption, enabling fully autonomous, long-endurance drone swarms for scalable smart farming.

## Objectives

- Design low-power sensor interfaces in **GF180MCU** 
- Develop mixed-signal feature extraction circuits 
- Implement event-driven edge processing with tunable activity thresholds
- Explore lightweight TinyML-compatible inference logic 
- Create reusable open-source building blocks for future sensor systems 




## Repository Structure
