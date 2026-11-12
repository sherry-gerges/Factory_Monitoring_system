
# 🛡️ MachGuard
### Industrial IoT Machine Condition Monitoring & Predictive Maintenance System

<p align="center">
  <strong>Monitor smarter. Detect earlier. Protect better.</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Project-IoT%20Graduation%20Project-2563EB?style=for-the-badge" alt="IoT Graduation Project">
  <img src="https://img.shields.io/badge/Program-Samsung%20Innovation%20Campus%20SIC%208-1428A0?style=for-the-badge" alt="Samsung Innovation Campus SIC 8">
  <img src="https://img.shields.io/badge/Prototype-Low--Voltage%20DC%20Motor-orange?style=for-the-badge" alt="Low voltage prototype">
</p>

---

## 📌 Overview

**MachGuard** is an Industrial Internet of Things (IIoT) prototype designed to monitor machine operating conditions, detect abnormal behavior, and help protect equipment through automated shutdowns and operator notifications.

The system brings together sensors, edge processing, MQTT communication, dashboards, and historical data storage to demonstrate a move from reactive maintenance toward **condition-based monitoring and predictive-maintenance practices**.

> [!IMPORTANT]
> **Project scope:** The documented implementation is a single low-voltage prototype represented by a DC motor. It demonstrates monitoring, alerting, data logging, and protective actions. It has **not** been validated for use on real industrial machinery.

## 📚 Table of Contents

- [🎯 Problem Statement](#-problem-statement)
- [✨ Key Features](#-key-features)
- [🏗️ System Architecture](#️-system-architecture)
- [🔩 Hardware Components](#-hardware-components)
- [💻 Software & Technologies](#-software--technologies)
- [⚙️ How It Works](#️-how-it-works)
- [📊 Monitoring & Data Flow](#-monitoring--data-flow)
- [🧠 Predictive-Maintenance Approach](#-predictive-maintenance-approach)
- [🧪 Testing & Validation](#-testing--validation)
- [⚠️ Limitations](#️-limitations)
- [🚀 Future Enhancements](#-future-enhancements)
- [👥 Team](#-team)
- [🙏 Acknowledgments](#-acknowledgments)
- [📁 Repository & Setup Notes](#-repository--setup-notes)

## 🎯 Problem Statement

Traditional reactive maintenance often means that equipment is inspected or repaired only after a fault occurs. Abnormal temperature, vibration, electrical load, or gas conditions may go unnoticed, potentially leading to unplanned downtime, equipment damage, and increased maintenance costs.

**MachGuard addresses this challenge by:**

- Continuously monitoring selected operating parameters.
- Detecting readings that move beyond configured limits.
- Recording historical telemetry to help review trends and recurring abnormalities.
- Triggering protective actions for configured critical conditions.
- Notifying responsible personnel when an alert occurs.

## ✨ Key Features

| Feature | Description |
|---|---|
| 🔐 **NFC-Based Access Control** | Checks the operator's NFC tag against the authorized list before allowing machine operation. |
| 🌡️ **Temperature Monitoring** | Monitors ambient/enclosure temperature and raises a warning when the configured limit is exceeded. |
| 📳 **Vibration Monitoring** | Uses the MPU-6050 readings to monitor vibration-related behavior and identify abnormal readings. |
| ⚡ **Current & Electrical Monitoring** | Monitors electrical measurements and detects configured overcurrent conditions. |
| 🫧 **Gas Detection** | Uses the MQ-5 module's digital output to indicate gas presence relative to its configured threshold. |
| 🛑 **Automatic Protection** | Uses a relay to stop the low-voltage prototype motor when configured critical conditions are detected. |
| 💤 **Idle / No-Load Shutdown** | Turns the prototype off after a configured period of idle/no-load operation. |
| 🔔 **Smart Notifications** | Sends configured alerts through Telegram and Gmail notification flows. |
| 📈 **Dashboard & Historical Logging** | Visualizes machine telemetry in ThingsBoard and stores time-series data in InfluxDB through Telegraf. |

## 🏗️ System Architecture

The system is organized into connected layers, from physical sensing to monitoring and notifications.

| Layer | Components / Technologies | Responsibility |
|---|---|---|
| 🏭 Machine Model | Low-voltage DC motor | Represents the machine in the prototype. |
| 📡 Sensing & Identification | DHT22, MPU-6050, INA219/INA226 as configured, MQ-5, PN532 | Collects environmental, vibration, electrical, gas-indication, and operator-identity data. |
| 🧩 Edge Controller | Raspberry Pi 4, Python | Reads sensors, evaluates local conditions, and manages the relay and machine state. |
| 🔌 Actuation | Relay module | Switches the prototype motor off during configured protective shutdowns. |
| ☁️ Communication | MQTT, HiveMQ Cloud | Transports telemetry and messages between system components. |
| 🔄 Flow Processing | Node-RED | Processes messages and supports dashboard and notification workflows. |
| 📊 Visualization | ThingsBoard | Displays machine telemetry, status, and alerts. |
| 🗄️ Time-Series Storage | Telegraf, InfluxDB | Collects and stores timestamped telemetry for historical review. |
| 📬 Notifications | Telegram, Gmail | Delivers configured alerts to responsible users. |

### Architecture at a Glance

```text
 Sensors + NFC
      │
      ▼
 Raspberry Pi 4 ───────► Relay ───────► DC Motor
      │
      │ MQTT
      ▼
  HiveMQ Cloud
      │
      ├────────► Node-RED ─────────► Telegram / Gmail
      │                │
      │                └───────────► ThingsBoard
      │
      └────────► Telegraf ────────► InfluxDB
```

The Raspberry Pi performs the documented local monitoring and protective decision logic. MQTT and cloud-connected services support telemetry exchange, visualization, and notifications.

## 🔩 Hardware Components

| Component | Role in MachGuard | Notes |
|---|---|---|
| 🧠 **Raspberry Pi 4 Model B** | Main controller and IoT gateway | Runs the Python application and local logic. |
| 🌡️ **DHT22** | Temperature and humidity sensing | Measures ambient/enclosure conditions. |
| 📳 **MPU-6050** | Motion and vibration-related sensing | Provides accelerometer and gyroscope readings for signal analysis. |
| ⚡ **INA219 / INA226** | Electrical monitoring | Measures current, voltage, and power; confirm the exact module used in the final wiring. |
| 🫧 **MQ-5** | Gas indication | Digital output indicates whether the module's threshold has been crossed. |
| 🪪 **PN532 NFC Reader** | Operator authorization | Reads the NFC tag presented by the operator. |
| 🔌 **Relay Module** | Motor switching | Controls power to the low-voltage prototype motor. |
| ⚙️ **DC Motor** | Machine representation | Used as the prototype load. |
| 🖥️ **16×2 I2C LCD** | Local status display | Displays machine status and fault messages. |

> [!NOTE]
> The DHT22 measures ambient/enclosure temperature, not the internal temperature of the motor body. The MQ-5 is used through its digital output; therefore, the prototype does not identify the gas type or provide a calibrated concentration in ppm.

## 💻 Software & Technologies

| Technology | Purpose |
|---|---|
| 🐍 **Python** | Raspberry Pi application, sensor reading, and control logic. |
| 📡 **MQTT** | Lightweight publish/subscribe messaging between system components. |
| ☁️ **HiveMQ Cloud** | MQTT broker for telemetry and messaging. |
| 🔄 **Node-RED** | Message processing, integrations, and notification flows. |
| 📊 **ThingsBoard** | Dashboard and machine-status visualization. |
| 🗄️ **Telegraf** | Subscribes to telemetry and forwards measurements to InfluxDB. |
| 📈 **InfluxDB** | Time-series database for timestamped machine data. |
| 🧮 **RMS & FFT Analysis** | Signal features used for vibration/current anomaly checks. |
| ✉️ **Telegram & Gmail** | Notification channels for configured alerts. |

**Optional machine-learning component:** An Edge Impulse model can be loaded only if a trained `.eim` file is available and has been tested. Otherwise, the documented approach relies on analytical checks such as thresholds, RMS, and FFT. The optional model should not be presented as a deployed feature unless it is actually included and validated.

## ⚙️ How It Works

| Step | Process | What Happens |
|---:|---|---|
| 1 | 🔐 **Operator Authorization** | The operator scans an NFC tag. The controller checks its UID against the authorized list. |
| 2 | ⚙️ **Machine Control** | If access is authorized and no critical fault is active, the relay can switch the prototype motor on or off. |
| 3 | 📡 **Sensor Acquisition** | The Raspberry Pi reads the connected sensors and prepares telemetry. |
| 4 | 🧠 **Local Analysis** | The controller checks configured limits and evaluates available vibration/current signal features. |
| 5 | 🛑 **Protective Action** | A configured critical condition can trigger the relay to stop the prototype motor. |
| 6 | ☁️ **Data Communication** | Telemetry and alert messages are published through MQTT for connected services. |
| 7 | 🔔 **Notifications & Visualization** | Node-RED supports alert delivery, while ThingsBoard presents machine information. |
| 8 | 🗄️ **Historical Review** | Telegraf forwards timestamped telemetry to InfluxDB for later analysis. |

## 📊 Monitoring & Data Flow

### Dashboard Monitoring

The ThingsBoard dashboard is intended to provide a view of the machine's operating condition.

| Parameter | Monitoring Purpose |
|---|---|
| 🌡️ Temperature | Follow ambient/enclosure temperature and configured warnings. |
| 📳 Vibration | Observe vibration-related readings and abnormal behavior. |
| ⚡ Current & Electrical Values | Follow electrical measurements and configured overload conditions. |
| 🫧 Gas Sensor State | Display the MQ-5 digital gas-indication state. |
| ⚙️ Machine State | Show whether the prototype is ON or OFF. |
| ❤️ Health / Alert State | Present the current warning or fault status. |
| 🕒 Historical Readings | Review stored measurements and changes over time. |

### Data Pipeline

```text
Sensors → Raspberry Pi → MQTT / HiveMQ
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
         Node-RED                    Telegraf
             │                           │
       ┌─────┴─────┐                     ▼
       ▼           ▼                 InfluxDB
  Notifications  ThingsBoard
  (Telegram /
    Gmail)
```

Historical records can help identify trends and repeated abnormal readings. They do not, by themselves, prove the cause of a fault.

## 🧠 Predictive-Maintenance Approach

MachGuard demonstrates an **early-warning approach** using configured thresholds and signal analysis.

- **Threshold checks:** Compare sensor readings with configured limits.
- **RMS analysis:** Summarizes signal magnitude and can help identify changes in vibration/current behavior.
- **FFT analysis:** Examines signal frequency components that may reveal changes in operating behavior.
- **Historical review:** Allows readings over time to be examined for recurring patterns.

> [!WARNING]
> MachGuard is an early-warning prototype, not a validated remaining-useful-life (RUL) prediction system. It does not currently determine the exact time a machine will fail or diagnose every mechanical fault. Reliable industrial use would require representative machine data, calibrated thresholds, and validation under real operating conditions.

## 🧪 Testing & Validation

The project documentation records successful prototype tests for the following scenarios:

| Test Scenario | Expected Behavior | Documented Result |
|---|---|:---:|
| ✅ Authorized NFC Tag | Machine can be switched on. | **Pass** |
| 🚫 Unauthorized NFC Tag | Access is denied. | **Pass** |
| 🌡️ Temperature Reading | Sensor reading is acquired. | **Pass** |
| 📳 Vibration Warning | Health state changes to Warning. | **Pass** |
| ⚡ Overcurrent | Relay opens and the prototype stops. | **Pass** |
| 🫧 Gas Detection | Configured gas detection triggers the protective response. | **Pass** |
| 📲 Telegram Alert | Alert message is received. | **Pass** |
| 📧 Gmail Alert | Email alert is received. | **Pass** |
| 💤 Idle Stop | Machine switches off after the configured idle timeout. | **Pass** |
| 🗄️ Data Logging | Timestamped records appear in InfluxDB. | **Pass** |
| 🔌 Sensor Disconnect | Invalid reading is handled without being treated as valid data. | **Pass** |
| ☁️ Broker / Network Recovery | Publishing resumes after recovery. | **Pass** |

These results describe the documented prototype test setup only. They are not industrial safety certification or proof of performance on production machinery.

## ⚠️ Limitations

| Area | Current Limitation |
|---|---|
| 🏭 Deployment Scope | Tested as a low-voltage DC-motor prototype, not on production machinery. |
| 🎚️ Calibration | Thresholds are configured for the prototype and require machine-specific calibration. |
| 🌡️ Temperature | DHT22 measures ambient/enclosure temperature, not motor-body temperature. |
| 🫧 Gas Sensing | MQ-5 digital output does not identify gas type or provide calibrated ppm values. |
| 🧠 Prediction | Current early warnings use thresholds and FFT/RMS analysis; they do not provide validated RUL predictions. |
| 📊 Real-World Impact | Reduced downtime and maintenance costs have not been demonstrated in a real factory. |

## 🚀 Future Enhancements

- 🏭 **Multi-Machine Support:** Extend monitoring to multiple machines, each with a unique device identifier.
- 📚 **Real-World Dataset:** Collect representative industrial operating data and calibrate thresholds for each machine.
- 🤖 **Validated ML Model:** Train and evaluate a machine-learning model using labeled historical data.
- 🔍 **Fault Classification:** Explore more advanced vibration and motor-current signature analysis.
- 🫧 **Gas-Specific Sensing:** Select sensors based on the gases expected in the target environment.
- 📈 **Advanced Analytics:** Improve dashboard reporting and maintenance-planning insights.
- 🧰 **Deployment Validation:** Evaluate the system with appropriate industrial engineering review and certified protection hardware.

## 👥 Team

 

**Alhussein Ahmed Sayed** 

**George Gamal Girgis** 

**Mohamed Farouk Aboelfetouh** 

**Sherry Gerges Fares** 



---

<p align="center">
  <strong>MachGuard — Smarter Monitoring for Safer Machine Operation.</strong>
</p>
