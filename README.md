# ⚡ Edge-Sync-Room-Ecosystem
### *Latency-Optimized Distributed Automation*

![Status](https://img.shields.io/badge/Status-Active_v2.1-success.svg)
![Language](https://img.shields.io/badge/Language-C%2B%2B-blue.svg)
![Platform](https://img.shields.io/badge/Platform-ESP32_%7C_ESP8266-green.svg)
![Protocol](https://img.shields.io/badge/Protocol-38kHz_Active_IR-orange.svg)
![License](https://img.shields.io/badge/License-MIT-lightgrey.svg)

![System Architecture](assets/system_architecture.jpg)
*(Note: Upload your architecture diagram or a photo of the finished setup here)*

## 🚀 Project Overview
**Distributed-Room-Ecosystem** is a high-performance IoT network designed to automate physical access, ambient lighting, and legacy hardware with **sub-50ms latency**.

It orchestrates two independent microcontrollers (ESP32 & ESP8266) to create a **synchronized environment**, managing a custom Active IR Tripwire for presence detection while simultaneously modernizing non-smart RGB strips into a cohesive smart node.

> **Key Differentiator:** Unlike standard smart plugs that rely on slow cloud triggers, this system runs on a **non-blocking, hardware-interrupted architecture**, ensuring instant physical response times even during heavy Wi-Fi activity.

---

## ⚙️ Key Features

| Feature | Description |
| :--- | :--- |
| **⚡ Dual-Node Sync** | Distributed logic between **Door Node** (Receiver) and **Desk Node** (Emitter) enables instant reaction to human presence. |
| **🔌 Smart Extension Hub** | Retrofitted a mains extension cord to act as a "Smart Hub," allowing modular control of any plugged-in device (Lamps, Chargers, Drivers). |
| **🛡️ Active IR "Heartbeat"** | Uses a modulated 38kHz carrier wave (instead of simple DC) to reject sunlight and ambient noise interference. |
| **🔄 State Recovery** | Automated logic overrides the chaotic flashing of cheap RGB strips on boot, restoring user presets (Purple/Solid) without a physical remote. |
| **🧠 Smart Latch Logic** | Anti-jitter algorithm distinguishes between a lingering person and a genuine entry/exit event. |

---

## 🔧 The Engineering Journey

### 1. The Sensor Pivot: PIR vs. Active IR
* **The Attempt:** Initially used Passive Infrared (PIR) sensors.
* **The Failure:** High latency (delay) and broad detection zones made it impossible to create a precise "Tripwire" for the doorframe.
* **The Solution:** Pivoted to **VS1838B TSOP Receivers** with internal gain control and band-pass filters for laser-focused detection.

### 2. The Cloud Latency Lesson (Cloud-to-Edge)
Before the current hardware-based design, I attempted a "No-Code" cloud solution. It was a critical lesson in IoT bottlenecks.

* **❌ Old Architecture:** `ESP32 -> Webhooks -> Make.com -> SmartThings -> Cloud -> Bulb`
    * **Result:** **3-5 second delay**. Unacceptable for room entry.
* **✅ New Architecture:** `ESP32 -> GPIO Relay (Hardwired)`
    * **Result:** **<50ms delay**. A 99.9% improvement in speed and reliability.



[Image of Cloud vs Edge Computing diagram for IoT]


### 3. The "Resource War" (Tripwire vs. RGB)
* **The Conflict:** The ESP8266 Desk Node had to generate a continuous 38kHz tripwire signal (`analogWrite`) AND send precise NEC IR codes to the LED strip simultaneously. The interrupts clashed, causing flickering lights.
* **The Fix:** Implemented a **Mutex (Mutual Exclusion)** strategy. The system briefly pauses the 38kHz carrier wave (~50ms) to transmit LED color commands cleanly, then immediately resumes the tripwire.

---

## 🛠️ Hardware Architecture

### 🚪 Door Node ( The "Brain" & Receiver )
* **Core:** ESP32 DevKit V1
* **Role:** Detects beam breaks, controls the main relay, and syncs state to the cloud.
* **Sensors:** VS1838B IR Receiver (3.3V Logic).
* **Actuators:** 5V Relay Module (Transistor Buffered).

### 🖥️ Desk Node ( The "Emitter" & Lighting )
* **Core:** NodeMCU (ESP8266)
* **Role:** Generates the 38kHz "Heartbeat" signal and controls legacy RGB strips.
* **Hardware:** 940nm High-Power IR LED driven by a **2N2222 Transistor** on the 5V rail.

---

## 🔌 Schematics & Wiring

**Safety First:** The system uses a transistor-buffered relay circuit to protect the ESP32 GPIO pins from inductive flyback voltage.

![Circuit Diagram](schematics/wiring_diagram.png)
*(Note: Upload your wiring diagram to the schematics folder)*

---

## 📱 IoT Integration (Blynk)
The system connects to a mobile dashboard for manual overrides and telemetry:

* **V0:** Main Room Light (Synced with physical beam break)
* **V1:** "Party Mode" Lock (Disables automatic sensors)
* **V2:** Desk RGB Color Selector (Injects NEC Codes)

---

## 📂 Repository Structure
* **`/src`**: Production-ready firmware for ESP32 and ESP8266.
* **`/legacy_prototypes`**: Chronological archive of development versions (v0.1 to v2.1).
* **`/assets`**: Project diagrams and images.

## 📜 License
Distributed under the MIT License. See `LICENSE` for more information.

*Built by **Aoun Raza**.*
