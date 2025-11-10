# EPS GUARDIAN — Hardware Implementation
## Autonomous Energy Supervision System for CubeSat

### IEEE AESS & IES Challenge 2025

## Overview

This repository contains the hardware implementation of the EPS GUARDIAN system — an intelligent onboard Electrical Power System (EPS) designed for CubeSat platforms. It integrates deterministic logic and embedded artificial intelligence to perform real-time fault detection, anomaly prediction, and autonomous power management using an ESP32 microcontroller.

This module represents the embedded counterpart of the software developed in [EPS-Guardian-Software](https://github.com/Yasmine638/EPS-Guardian-Software).

## Architecture Overview

The system follows a hybrid two-layer architecture combining rule-based physics supervision with deep learning inference.

| Layer | Platform | Function | Model | Description |
|-------|----------|----------|-------|-------------|
| MCU (Edge) | ESP32 | Real-time supervision | Autoencoder (.tflite) | Detects anomalies in voltage, current, temperature |
| OBC (Core) | Host/PC or ESP32 secondary | Temporal validation | LSTM Autoencoder (.h5 / .tflite) | Confirms/rejects alerts based on time sequences |

## Embedded AI Logic

### Deterministic Rules (R1–R7):
- Monitor EPS physical parameters (battery voltage/current, temperature, bus voltage).

### AI Inference (TensorFlow Lite Micro):
- Executes an optimized Autoencoder directly on the MCU.

### Fusion Layer:
- Combines AI reconstruction error with deterministic rules to classify the EPS state as:
  - 🟢 Normal
  - 🟡 Warning
  - 🔴 Critical

### OBC Layer (LSTM):
- Performs advanced validation of anomalies using temporal data sequences.
- Communicates with the MCU via UART serial link.

## Repository Structure
```
EPS-Guardian-Hardware/
│
├── firmware/
│   ├── eps_guardian_ai_model.h              # Quantized AI model for MCU (C++ header)
│   ├── eps_guardian_esp32.ino               # Deterministic rules + sensor logic
│   ├── eps_guardian_inference.ino           # TensorFlow Lite Micro inference engine
│   ├── eps_guardian_esp32_fusion.ino        # Hybrid MCU + OBC communication
│
├── models/
│   ├── ai_autoencoder.tflite                # MCU AI model
│   ├── ai_autoencoder.h5                    # Original training model (reference)
│   ├── ai_model_lstm_autoencoder.h5         # OBC temporal model (LSTM)
│   ├── ai_model_lstm_autoencoder_quant.tflite # Quantized OBC version
│
└── docs/
    ├── deployment_report.md                 # Model optimization & deployment details (optional)
    └── hardware_diagram.png                 # Hardware diagram (optional)
```

## Deployment on ESP32

### Requirements
- Arduino IDE (version ≥ 2.2.0)
- TensorFlow Lite Micro library
- ESP32 Board Package (Tools → Board → ESP32 Dev Module)

### Upload Process
1. Open `firmware/eps_guardian_esp32_fusion.ino` in Arduino IDE.
2. Verify connections and select your ESP32 port.
3. Click Upload (Ctrl + U).
4. Monitor output via Serial Monitor (115200 baud).

The system will automatically execute both rule-based and AI-based supervision, and display:
```
[EPS-GUARDIAN] MCU active — Monitoring EPS
AI inference: Normal / Warning / Critical
```

### Hardware Readiness
This firmware is optimized for direct integration with:
- INA219 (voltage/current sensor)
- DS18B20 (temperature sensor)
- MOSFET/Relay-controlled subsystems
- LED indicators for Power / Warning / Critical

During deployment, the system can operate in simulation mode (no sensors connected) or real telemetry mode with external hardware.

## License and Acknowledgment
This work was developed as part of the IEEE Aerospace and Electronic Systems (AESS) and Industrial Electronics Society (IES) Challenge 2025.