# Motion & Tilt Fault Detection System

A real-time fault detection system using an MPU-6050 IMU sensor and ESP32 / Arduino Uno.
Detects abnormal motion, tilt, and free-fall events using an adaptive threshold algorithm
that learns the normal behavior of the environment automatically.

---

## What it does

- Reads accelerometer and gyroscope data from an MPU-6050 at ~100Hz
- Builds an adaptive baseline using Exponential Moving Average (EMA)
- Computes running variance using Welford's online algorithm
- Classifies four fault states in real time:
  - **NORMAL** — no anomaly detected
  - **TILT_WARNING** — orientation shifted beyond 3σ from baseline
  - **VIBRATION_FAULT** — sudden rotational spike beyond 3.5σ
  - **FREE_FALL_DETECTED** — total acceleration dropped below 0.3g
- Outputs fault state via Serial monitor, LED blink pattern, buzzer beep code
- ESP32 version also publishes fault events to an MQTT broker as JSON

---

## How the adaptive threshold works

Most fault detection systems use fixed thresholds — if the value exceeds X, trigger an alarm.
The problem is that X has to be manually tuned for every environment.

This system learns its own baseline:

1. During the first 200 samples, it silently observes the sensor in its normal state
2. An EMA tracks the expected value of roll, pitch, and gyro magnitude
3. Welford's algorithm tracks the running standard deviation
4. Faults trigger only when a reading deviates more than 3σ (tilt) or 3.5σ (vibration) from the adaptive mean

This means the system self-calibrates to wherever it is mounted — no manual tuning needed.

---

## Fault output codes

| Fault | LED | Buzzer |
|---|---|---|
| Normal | Off | Silent |
| Tilt warning | Slow blink (500ms) | Single beep every 2s |
| Vibration fault | Fast blink (150ms) | Double beep every 1s |
| Free-fall | Solid on | Rapid 2kHz beep |

---

## Hardware required

- ESP32 DevKit v1 (or Arduino Uno)
- MPU-6050 IMU module
- LED + 220Ω resistor
- Passive buzzer
- Breadboard and jumper wires

---

## Wiring

### ESP32

| Component | ESP32 Pin |
|---|---|
| MPU-6050 VCC | 3V3 |
| MPU-6050 GND | GND |
| MPU-6050 SDA | GPIO 21 |
| MPU-6050 SCL | GPIO 22 |
| LED anode (via 220Ω) | GPIO 2 |
| Buzzer positive | GPIO 4 |

### Arduino Uno

| Component | Arduino Pin |
|---|---|
| MPU-6050 VCC | 5V |
| MPU-6050 GND | GND |
| MPU-6050 SDA | A4 |
| MPU-6050 SCL | A5 |
| LED anode (via 220Ω) | Pin 13 |
| Buzzer positive | Pin 8 |

---

## Files