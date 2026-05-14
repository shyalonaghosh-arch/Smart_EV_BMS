# Smart EV Battery Management System

A BMS prototype on the ESP32-DevKit V1 that monitors a 2S Li-ion 
pack in real time with MOSFET-based hardware protection and live 
display on a 16x2 I2C LCD.

---

## How It Works

**Voltage Monitoring**
A 10kΩ/4.7kΩ voltage divider scales the 8.4V pack down to 
ESP32-safe levels. Firmware reverses the math to recover actual 
battery voltage.

**Temperature Monitoring**
NTC thermistor read via ADC. Steinhart-Hart equation (B=3435) 
converts resistance to °C.

**MOSFET Protection**
- IRLZ44N (N-ch) on discharge path — turns OFF when voltage
  drops below 5.0V, disconnecting the load instantly
- AO3401 (P-ch) on charge path — turns OFF when pack hits
  ≥ 8.4V, stopping charge current
- Both turn OFF if temperature exceeds 45°C
- 10Ω gate resistors protect ESP32 GPIOs from switching spikes

**Capacitors**
- 100nF across ESP32 3.3V and GND — filters ADC noise
- 100nF across AMS1117 output and GND — filters regulator noise
- 100µF across battery input and GND — smooths current spikes

---

## Hardware

| Component | Model | Qty |
|---|---|---|
| Microcontroller | ESP32-DevKit V1 | 1 |
| NTC Thermistor | NTCLE100E3103JB0 | 1 |
| Discharge MOSFET | IRLZ44N | 1 |
| Charge MOSFET | AO3401 / IRF9540N | 1 |
| Resistor | 10kΩ | 5 |
| Resistor | 4.7kΩ | 2 |
| Resistor | 10Ω | 2 |
| Capacitor | 100nF 50V ceramic | 2 |
| Capacitor | 100µF 50V electrolytic | 1 |
| LCD | 16x2 I2C | 1 |
| Battery | Li-ion 3.7V (2S = 7.4V) | 2 |

---

## Pin Map

| GPIO | Connected To |
|---|---|
| 34 | Voltage divider output |
| 32 | NTC Thermistor output |
| 25 | IRLZ44N gate (discharge control) |
| 26 | AO3401 gate (charge control) |
| 21 | SDA (LCD) |
| 22 | SCL (LCD) |

---

## Protection Thresholds

| Fault | Threshold | Action |
|---|---|---|
| Overcharge | ≥ 8.4V | Charge MOSFET OFF |
| Overdischarge | ≤ 5.0V | Discharge MOSFET OFF |
| Overtemperature | ≥ 45°C | Both MOSFETs OFF |

---

## Dependencies
- `Wire.h` (built-in)
- `LiquidCrystal_I2C` by Frank de Brabander

---

## Limitations
- Single thermistor — one monitoring point only
- No current sensing / SOC estimation
- WiFi unused — local display only

## Future Scope

**Short Term**
- Add current sensing via ACS712
- SOC estimation using coulomb counting
- Buzzer alert on fault condition

**Medium Term**
- WiFi dashboard accessible from any browser
- Data logging to SD card
- Active cell balancing

**Long Term**
- Custom PCB design replacing breadboard
- Upgrade to 10S pack with BQ76930 for real EV deployment
- CAN bus communication with motor controller
- Mobile app for real time telemetry

---

> Academic prototype — 2S pack simulates an EV battery demonstrating
> core BMS functions: voltage monitoring, thermal monitoring,
> and hardware protection via MOSFET switching.
