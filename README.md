## 🏊 ESPHome Smart Pool Controller

This project provides a comprehensive ESPHome configuration for managing a smart pool system using a **Waveshare ESP32-S3 6-Channel Relay board**. It features RS485/UART communication for high-end pool pumps, motorized valve control, and automated lighting sequences.

### ✨ Key Features

* **Advanced Pump Control**: Communicates via UART to trigger specific pump programs (Eco, Boost, Clean, Max).
* **Intelligent Operation Modes**: Includes "Swim Mode," "Storm Defense," and "Super Cooling" with automated valve adjustments.
* **Automated Pool Lighting**: Custom scripts to "cycle" pool lights through internal programs using precise power-toggle timing.
* **Safety Watchdog**: An interval-based watchdog ensures the pump is actually running when commanded, refiring commands if RPM is detected at zero.
* **Swim Timer**: A configurable countdown timer that automatically turns off the system after a swim session.

### 🛠 Hardware Supported

* **Controller**: Waveshare ESP32-S3 Relay 6CH.
* **Pump**: RS485-capable pool pump (e.g., Pentair or similar clones using the 0xA5 0x00 protocol).
* **Valves**: Standard 24V AC motorized pool valves (connected via relays).

### ⚙️ Logic Overview

#### 1. Operation Modes

The system uses a `select` entity to choose between various pool states. Changing this mode triggers `refire_pump_logic`, which orchestrates the pump speed and valve positions:

| Mode | Pump Program | Suction Valve | Aerator Valve |
| --- | --- | --- | --- |
| **Off** | Stopped | Closed | Closed |
| **Swim Mode** | 45 GPM | Closed | Toggleable |
| **Cleaning Vac** | 38 GPM | **Open** | Closed |
| **Storm Defense** | 55 GPM | Closed | Closed |
| **Super Cooling** | 55 GPM | Closed | **Open** |

#### 2. The Lighting "Cycle" Script

Most pool lights change colors based on rapid power toggles. This config automates that tedious process:

1. It performs a **30-second reset** (power off) to ensure the light is at a known starting state.
2. It executes a **triple-toggle** to set the light to "Program 1."
3. It then toggles  additional times (based on your input) to reach the desired color mode.

#### 3. UART Telemetry

The configuration decodes the 26-byte hex response from the pump to provide real-time data in Home Assistant:

* **RPM & Flow (GPM)**
* **Power Consumption (Watts)**
* **Drive Temperature**
* **Error Codes** (mapped to human-readable text)

### 🚀 Getting Started

1. Copy `poolcontrol.yaml` to your ESPHome directory.
2. Create a `secrets.yaml` and include your `wifi_ssid`, `wifi_password`, and `api_encryption_key`.
3. Connect your RS485-to-TTL adapter to **GPIO17 (TX)** and **GPIO18 (RX)**.
4. Flash the ESP32-S3.
