# 🪖 SMART HELMET FOR TWO WHEELERS

> An IoT-enabled wearable safety system for two-wheeler riders integrating accident detection, drunk driving prevention, helmet-wear enforcement, and real-time emergency dispatch using ESP32, MPU6050, GPS NEO-6M, and GSM SIM800L.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [System Architecture](#system-architecture)
- [Hardware Components](#hardware-components)
- [Software Tools](#software-tools)
- [Circuit & Pin Connections](#circuit--pin-connections)
- [How It Works](#how-it-works)
- [Multi Sensor Fusion](#multi-sensor-fusion)
- [Communication Protocols](#communication-protocols)
- [Results](#results)
- [Future Scope](#future-scope)

---

## Overview

Road accidents involving two-wheeler riders remain one of the leading causes of fatalities worldwide, often worsened by delayed emergency response, drunk driving, and failure to wear helmets. Traditional helmets provide only passive protection — they cannot detect crashes, monitor rider conditions, or alert emergency services.

This project addresses that gap with a **Comprehensive Smart Helmet** that integrates embedded systems, multi sensor fusion, and wireless communication into a single wearable device. It prevents unsafe riding before the vehicle starts and automatically dispatches emergency alerts with GPS location when an accident is detected — **without any manual intervention**.

---

## Features

- ✅ Alcohol detection — disables vehicle ignition if alcohol exceeds safe limit
- ✅ Helmet-wear verification via IR sensor before allowing ignition
- ✅ Voice interaction module to confirm rider condition post-accident
- ✅ Real-time GPS location tracking via NEO-6M
- ✅ SMS + voice call alerts via GSM SIM800L
- ✅ Google Maps link embedded in the SMS alert
- ✅ Solar panel + Li-ion battery for self-sustained power
- ✅ Works without internet connectivity (GSM-based)
- ✅ Distributed two-module architecture (helmet unit + bike unit)
- ✅ Scalable for IoT and smart city integration

---

## System Architecture

The system is split into two hardware nodes communicating wirelessly over Wi-Fi:

```
┌────────────────────────────────────────────────────────────────┐
│  HELMET UNIT          ESP32-C3 SuperMini (Central Processing)  │
│                                                                │
│  SENSING LAYER        MPU6050 (Gyroscope + Accelerometer)      │
│                       MQ-135 Alcohol Sensor                    │
│                       IR Sensor (Helmet-Wear Detection)        │
│                                                                │
│  POSITIONING LAYER    GPS NEO-6M (UART @ 9600 bps)             │
│                       NMEA data parsing via TinyGPS++          │
│                                                                │
│  COMMUNICATION LAYER  GSM SIM800L (AT Commands via UART)       │
│                       SMS + Call to emergency contacts         │
│                                                                │
│  ALERT LAYER          ISD1820 Voice Module                     │
│                                                                │
│  POWER LAYER          6V Solar Panel → 1S BMS → Li-Ion Battery │
└────────────────────────────────────────────────────────────────┘
                              │ Wi-Fi
┌────────────────────────────────────────────────────────────────┐
│  BIKE UNIT            ESP8266 V01 (Receiver + Relay Control)   │
│                       5V Relay Module (Ignition Control)       │
└────────────────────────────────────────────────────────────────┘
```

### Block Diagram

```
IR Sensor ──────────────┐
MPU6050 Sensor ─────────► ESP32-C3 SuperMini ──► GSM SIM800L ──► Alert:
MQ-9 Alcohol Sensor ────┘        │                               1. Ambulance
                                 │                               2. Patrol / Family
                         GPS NEO-6M
                                 │
                         ESP8266 V01 ──► 5V Relay ──► Ignition Control
                    (Self-Powered via Solar + BMS + Battery)
```

---

## Hardware Components

| Component | Model | Interface | Purpose |
|---|---|---|---|
| Microcontroller (Helmet) | ESP32-C3 SuperMini | — | Central processing unit |
| Microcontroller (Bike) | ESP8266 V01 | — | Relay control via Wi-Fi |
| IMU Sensor | MPU6050 | I2C | Gyroscope + Accelerometer |
| Alcohol Sensor | MQ-135 | ADC (Analog) | Drunk driving prevention |
| Helmet-Wear Sensor | IR Sensor | GPIO | Detects if helmet is worn |
| GPS Module | NEO-6M | UART | Real-time location tracking |
| GSM Module | SIM800L | UART | SMS and voice call alerts |
| Voice Module | ISD1820 | GPIO | Rider condition confirmation |
| Relay Module | 5V Relay | GPIO | Vehicle ignition control |
| Solar Panel | 6V Solar Panel | — | Renewable power source |
| Battery Management | 1S BMS | — | Battery protection & charging |
| Battery | Li-Ion Cell | — | Main energy storage |

---

## Software Tools

| Tool | Purpose |
|---|---|
| **Arduino IDE** | Firmware development, compilation, and upload |
| **PuTTY Serial Monitor** | UART debugging, AT command testing, GPS data verification |

### Libraries Used

| Library | Purpose |
|---|---|
| `TinyGPS++` | Parse NMEA sentences from GPS NEO-6M |
| `Wire` | I2C communication with MPU6050 |
| `MPU6050` | Read angular velocity and acceleration |
| `SoftwareSerial` | UART communication with GSM SIM800L |

---

## Circuit & Pin Connections

### MPU6050 → ESP32-C3

| MPU6050 Pin | ESP32 Pin | Description |
|---|---|---|
| VCC | 3.3V | Power |
| GND | GND | Ground |
| SCL | I2C SCL | I2C Clock |
| SDA | I2C SDA | I2C Data |
| AD0 | GND | I2C Address = 0x68 |

### MQ-135 Alcohol Sensor → ESP32-C3

| Sensor Pin | ESP32 Pin | Description |
|---|---|---|
| VCC | 5V | Power |
| GND | GND | Ground |
| AO | ADC Pin | Analog alcohol concentration |
| DO | GPIO | Digital threshold output |

### IR Sensor → ESP32-C3

| IR Pin | ESP32 Pin | Description |
|---|---|---|
| VCC | 3.3V – 5V | Power |
| GND | GND | Ground |
| OUT | GPIO | Helmet-wear detection signal |

### GPS NEO-6M → ESP32-C3 (UART)

| GPS Pin | ESP32 Pin | Description |
|---|---|---|
| VCC | 3.3V – 5V | Power |
| GND | GND | Ground |
| TX | UART RX | GPS data to ESP32 |
| RX | UART TX | Commands to GPS |

### GSM SIM800L → ESP32-C3 (UART)

| SIM800L Pin | ESP32 Pin | Description |
|---|---|---|
| VCC | Li-ion 3.7V – 4.2V | Power |
| GND | GND | Ground |
| TXD | UART RX | GSM data to ESP32 |
| RXD | UART TX | AT commands to GSM |

### ISD1820 Voice Module → ESP32-C3

| ISD1820 Pin | ESP32 Pin | Description |
|---|---|---|
| VCC | 3V – 5V | Power |
| GND | GND | Ground |
| PLAYE | GPIO | Edge-triggered playback |

### 5V Relay → ESP8266 V01 (Bike Unit)

| Relay Pin | ESP8266 Pin | Description |
|---|---|---|
| VCC | 5V | Power |
| GND | GND | Ground |
| IN | GPIO | Ignition control signal |

### Solar Panel → BMS → Battery

| Connection | Description |
|---|---|
| Solar Panel (+) | BMS Charging Input |
| BMS Output | Li-Ion Battery |
| Battery Output | Voltage Regulator → ESP32 + Modules |

---

## How It Works

1. **Boot** — ESP32-C3 initializes all peripherals (I2C, UART, ADC, GPIO).
2. **Pre-Ride Safety Check** —
   - IR sensor verifies helmet is properly worn.
   - Alcohol sensor reads breath alcohol concentration via ADC.
   - If either check fails → relay disables vehicle ignition via ESP8266.
3. **Continuous Monitoring** — MPU6050 streams angular velocity and vibration data continuously during the ride.
4. **Accident Detection** — sensor fusion algorithm checks:
   - Angular velocity exceeds threshold (abnormal tilt/rotation)
   - sensor must agree to avoid false positives
5. **Rider Confirmation** — ISD1820 voice module plays: *"Accident detected. Are you safe? Please respond."*
   - If rider responds within timeout → alert cancelled.
   - If no response → emergency sequence triggered.
6. **Location Fetch** — GPS NEO-6M provides current latitude/longitude.
7. **Alert Dispatch** — GSM SIM800L sends to all emergency contacts:
   - Voice call to each contact
   - SMS: `ACCIDENT DETECTED! Helmet wearer may need help. Location: https://maps.google.com/?q=<lat>,<lng>`
8. **Power** — Solar panel continuously charges the Li-ion battery through the BMS, ensuring operation even if the vehicle's power fails.

---

## Multi Sensor Fusion

The system uses **Multi Sensor Fusion** instead of single-sensor thresholding, significantly reducing false alerts from road bumps or sudden braking.

```
Initialize Sensors
        │
        ▼
Continuous Monitoring Loop
        │
        ├─► Read MPU6050 (Angular Velocity)
        │
        ▼
sensor exceed thresholds?
        │
       YES
        │
        ▼
Activate ISD1820 Voice Module
"Accident detected. Are you safe?"
        │
Wait for rider response (timeout)
        │
   No Response?
        │
       YES
        │
        ▼
Fetch GPS Coordinates ──► Send SMS + Call via GSM SIM800L
```

---

## Communication Protocols

### UART (GPS & GSM)
- Asynchronous serial communication at **9600 bps** (GPS) and **115200 bps** (GSM debug)
- GPS sends NMEA sentences parsed via `TinyGPS++`
- GSM controlled via AT commands

**Example AT commands:**
```
AT+CMGF=1                             // Set SMS text mode
AT+CMGS="+91XXXXXXXXXX"               // Set recipient
> ACCIDENT DETECTED! Location: ...    // Message body + Ctrl+Z to send
ATD+91XXXXXXXXXX;                     // Make voice call
ATH                                   // Hang up
```

### I2C (MPU6050)
- Two-wire synchronous protocol (SCL + SDA)
- MPU6050 address: `0x68`
- ESP32-C3 acts as I2C master

### BLE (Helmet ↔ Bike)
- ESP32-C3 SuperMini transmits safety status wirelessly to ESP8266 V01
- ESP8266 controls relay based on received helmet/alcohol status

---

## Results

The system was successfully tested on hardware under real-time conditions. Key outcomes:

- Alcohol detection correctly prevented ignition when breath alcohol exceeded the safe threshold.
- IR sensor accurately detected helmet placement before allowing vehicle start.
- AI sensor fusion correctly identified simulated accident events while rejecting false triggers from road bumps and sudden braking.
- Voice module confirmation reduced false emergency dispatches.
- GPS coordinates were acquired within 1–2 seconds under outdoor conditions.
- GSM SIM800L successfully dispatched SMS alerts with a Google Maps location link and made voice calls to emergency contacts.
- Solar-powered BMS provided continuous operation independent of the vehicle's power supply.

---

## Future Scope

- **ML-based detection** — Train models on real crash datasets for superior accuracy over threshold logic
- **Cloud / IoT integration** — Push data to ThingSpeak, Firebase, or AWS IoT for centralized fleet monitoring
- **Mobile app** — Real-time dashboard for emergency services and family contacts
- **Health monitoring** — Integrate pulse oximeter or heart rate sensor for rider vitals
- **Camera module** — Add image capture for post-accident evidence
- **V2V / V2I communication** — Alert nearby vehicles and traffic infrastructure
- **LoRa backup** — Long-range GSM fallback in zero-coverage zones
- **Compact PCB design** — OEM-ready ruggedized version for vehicle integration

---

> *Built to save lives on India's roads through intelligent embedded systems.*  
