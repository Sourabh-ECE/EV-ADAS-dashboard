<div align="center">

# 🚗⚡ EV-ADAS Dashboard System

**Real-World Electric Vehicle & Advanced Driver Assistance System**
built on STM32 ARM Cortex-M microcontroller with live Python dashboard

[![STM32](https://img.shields.io/badge/STM32-F103C8-blue?logo=stmicroelectronics&logoColor=white)](https://www.st.com)
[![Language](https://img.shields.io/badge/Language-C%20%7C%20Python-green)](https://gcc.gnu.org)
[![IDE](https://img.shields.io/badge/IDE-STM32CubeIDE-blue)](https://www.st.com/en/development-tools/stm32cubeide.html)
[![Simulator](https://img.shields.io/badge/Simulator-PicSimLab-orange)](https://lcgamboa.github.io/picsimlab_docs/)
[![Internship](https://img.shields.io/badge/Internship-Emertxe%20IT-purple)](https://emertxe.com)
[![License](https://img.shields.io/badge/License-MIT-yellow)](LICENSE)

</div>

---

## 📌 Overview

This project is a **Hardware-in-the-Loop (HIL) emulation** of a real-world Electric Vehicle cockpit combined with an ADAS safety system. Developed during my Embedded Systems Internship at **Emertxe Information Technologies**, it runs on an STM32F103C8Tx (Blue Pill) microcontroller and streams live telemetry to a Python Matplotlib dashboard over UART.

> The project emulates how a real EV ECU (Electronic Control Unit) processes sensor data, computes vehicle dynamics, and triggers ADAS safety alerts — all on bare-metal embedded C with no operating system.

---

## 🎥 Demo

> 📺 *Watch the full project walkthrough on YouTube → [link here]*

---

## ✨ Features

### ⚙️ EV Dynamics Engine
- Real-time **speed calculation** from ADC potentiometer input (0–200 km/h)
- **Torque computation** (0–250 Nm) with Eco / Normal / Sport drive modes
- **State of Charge (SOC)** tracking with regenerative braking simulation
- Motor temperature monitoring with overheat fault detection

### 🛡️ ADAS Safety System
- **Forward Collision Warning (FCW)** — alerts at < 50 cm, critical at < 20 cm
- **Blind Spot Detection (BSD)** — activates over 20 km/h when side proximity < 30 cm
- **Time-to-Collision (TTC)** = Distance / Relative Speed
- 3-sample hysteresis filter to eliminate sensor noise and false alarms
- **PWM piezo buzzer** — variable tone based on threat level (TIM4, pin PB9)

### 📡 Communication & Fault System
- **USART serial @ 115200 bps** — ASCII telemetry data framing
- **8-bit fault register** using bitwise logic:
  - `0x01` → Over-temperature
  - `0x02` → Low SOC
  - `0x04` → Impact proximity
- Failsafe isolation — torque drops to 0 on critical fault

### 📊 Python Live Dashboard
- Animated **speedometer dial** (color shifts green → red with speed)
- **Battery SOC bar** with range estimate
- **EV metrics panel** — Torque, Accel, Brake, Temp, Alarm, Fault, Signal
- **ADAS bird's-eye view** — top-down vehicle with front sensor cone and TTC readout
- **Speed history graph** — scrolling 60-second chart
- Renders at **10 FPS** over serial COM port

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|-----------|
| Microcontroller | STM32F103C8Tx — ARM Cortex-M3 @ 72 MHz |
| IDE | STM32CubeIDE 1.x + STM32CubeMX |
| Firmware language | C (bare-metal, HAL library) |
| Simulator | PicSimLab (HIL emulation) |
| Communication | USART1 @ 115200 bps |
| Dashboard | Python 3 + Matplotlib + PySerial |
| ADC | 12-bit SAR (4096 levels, 0.806 mV/step) |
| Timers | TIM1 (10ms EV loop) · TIM2 (PWM capture) · TIM3 (100ms sensors) · TIM4 (buzzer) |

---

## 📐 ADC Conversion Formula

```
Digital Value = (Vin / Vref) × (2ⁿ − 1)
```

| Parameter | Value |
|-----------|-------|
| Vref | 3.3 V |
| Resolution (n) | 12 bits |
| Max count | 4095 |
| Step size | 0.806 mV/step |

**Example:** Vin = 1.65V → `(1.65 / 3.3) × 4095 = 2047`

---

## 📁 Project Structure

```
EV-ADAS-Dashboard/
│
├── Core/
│   ├── Src/
│   │   ├── main.c              # Main firmware — EV + ADAS logic
│   │   ├── stm32f1xx_it.c      # Interrupt handlers (TIM1, TIM2, TIM3, USART)
│   │   └── adc.c               # ADC read & voltage conversion
│   └── Inc/
│       └── main.h
│
├── Drivers/
│   └── STM32F1xx_HAL_Driver/   # ST HAL library (auto-generated)
│
├── python_dashboard/
│   ├── dashboard.py            # Main Matplotlib live dashboard
│   ├── serial_parser.py        # UART telemetry parser
│   └── requirements.txt        # pip dependencies
│
├── docs/
│   ├── pinout_config.png       # STM32CubeMX pinout screenshot
│   └── system_architecture.png
│
├── .gitignore
├── EV_ADAS.ioc                 # STM32CubeMX configuration file
└── README.md
```

---

## 🔌 Pin Configuration (STM32F103C8Tx)

| Pin | Function | Peripheral |
|-----|----------|-----------|
| PA0 | Throttle (Accel ADC) | ADC1_IN0 |
| PA1 | Brake ADC | ADC1_IN1 |
| PA2 | Front proximity ADC | ADC1_IN2 |
| PA3 | Left side proximity ADC | ADC1_IN3 |
| PC13 | Status LED | GPIO Output |
| PA9 | USART1 TX → PC | USART1_TX |
| PA10 | USART1 RX ← PC | USART1_RX |
| PA8 | TIM1 CH1 — EV loop (10ms) | TIM1_CH1 |
| PB9 | Buzzer PWM | TIM4_CH4 |

---

## 🚀 Getting Started

### Prerequisites
- STM32CubeIDE (download from st.com)
- PicSimLab simulator
- Python 3.8+ with pip
- STM32F103C8Tx Blue Pill board (or PicSimLab simulation)

### 1. Clone the repository
```bash
git clone https://github.com/YourUsername/EV-ADAS-Dashboard.git
cd EV-ADAS-Dashboard
```

### 2. Flash the firmware
```
1. Open STM32CubeIDE
2. File → Import → Existing Projects into Workspace
3. Select this folder
4. Build Project (Ctrl + B)
5. Run → Debug / Flash to board
```

### 3. Run the Python dashboard
```bash
cd python_dashboard
pip install -r requirements.txt
python dashboard.py --port COM3    # Windows
python dashboard.py --port /dev/ttyUSB0   # Linux
```

### 4. Simulate with PicSimLab (no hardware needed)
```
1. Open PicSimLab → select STM32F103 board
2. Load the .hex file from Debug/ folder
3. Connect virtual serial port to dashboard.py
```

---

## 📊 System Architecture

```
Potentiometers (Sensors)
        │
        ▼
   STM32 ADC (12-bit)
  Vin → Digital (0–4095)
        │
   ┌────▼─────────────────┐
   │   STM32 Firmware (C)  │
   │  ┌─────────────────┐  │
   │  │  EV Physics      │  │  ← Speed, Torque, SOC, Regen
   │  │  Engine          │  │
   │  └────────┬────────┘  │
   │  ┌────────▼────────┐  │
   │  │  ADAS Safety    │  │  ← FCW, BSD, TTC, Fault register
   │  │  Engine         │  │
   │  └────────┬────────┘  │
   │  ┌────────▼────────┐  │
   │  │  USART Telemetry│  │  ← ASCII data @ 115200 bps
   │  └─────────────────┘  │
   └──────────┬────────────┘
              │ Serial (USB-UART)
              ▼
   Python Matplotlib Dashboard
   (Speedometer · SOC · ADAS · Graph)
```

---

## 📈 What I Learned

| Topic | Details |
|-------|---------|
| ADC peripheral | 12-bit SAR, multichannel, HAL_ADC_Start / PollForConversion |
| Timer interrupts | TIM1 @ 10ms for deterministic EV loop |
| UART framing | ASCII telemetry packets, PySerial parsing |
| ADAS algorithms | TTC calculation, hysteresis filtering, zonal alerts |
| Fault management | Bitwise 8-bit fault register, failsafe isolation |
| HIL emulation | Potentiometers as physical sensor emulators |
| Python GUI | Real-time Matplotlib animation at 10 FPS |

---

## 🏢 Internship Details

| Field | Info |
|-------|------|
| Company | Emertxe Information Technologies |
| Domain | Embedded Systems — Real World EV & ADAS |
| Duration | Ongoing |
| Track | STM32 ARM Cortex-M · Automotive Embedded · IoT |

---

## 📜 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 🙋 Author

**Your Name**
- LinkedIn: https://www.linkedin.com/in/sourabh-kumar-pandey-603267330/
- Email: sourabhkumarp07@gmail.com

---

<div align="center">

⭐ **If this project helped you, please give it a star!** ⭐

*Built with ❤️ during Emertxe Embedded Systems Internship*

</div>
