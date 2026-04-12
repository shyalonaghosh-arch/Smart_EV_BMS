# Smart EV Battery Management System

A BMS prototype on the ESP32-DevKit V1 that monitors a 2S Li-ion pack in real time,
displaying live voltage and temperature on a 16x2 I2C LCD with MOSFET-based hardware protection.

---

## How It Works

**Voltage Monitoring**
A 10kΩ/4.7kΩ voltage divider scales the 8.4V pack down to ESP32-safe levels.
Firmware reverses the math to display actual battery voltage.

**Temperature Monitoring**
NTC thermistor read via ADC. Steinhart-Hart equation (B=3435) converts resistance to °C.

**MOSFET Protection**
- IRLZ44N (N-ch) on discharge path — ESP32 pulls gate LOW on fault, load disconnects instantly
- AO3401 (P-ch) on charge path — turns OFF when pack hits ≥ 8.4V, stops charging
- 10Ω gate resistors protect ESP32 GPIOs from switching spikes
- 220Ω resistors bleed excess charge from higher cell (passive balancing)

**Fuse**
1A fuse as last line of defence against catastrophic shorts.

**Capacitors**
- 100nF ceramic — filters ADC noise
- 100µF electrolytic — smooths ESP32 current spikes

---

## Hardware

| Component | Model | Qty |
|---|---|---|
| Microcontroller | ESP32-DevKit V1 | 1 |
| NTC Thermistor | NTCLE100E3103JB0 | 1 |
| Discharge MOSFET | IRLZ44N | 1 |
| Charge MOSFET | AO3401 / IRF9540N | 1 |
| Fuse | 1A | 1 |
| Resistor | 10kΩ | 5 |
| Resistor | 4.7kΩ | 2 |
| Resistor | 220Ω | 2 |
| Resistor | 10Ω | 2 |
| Capacitor | 100nF 50V ceramic | 2 |
| Capacitor | 100µF 50V electrolytic | 1 |
| LCD | 16x2 I2C | 1 |
| Battery | Li-ion 3.7V (2S = 7.4V) | 2 |

---

## Pin Map

| GPIO | Connected To |
|---|---|
| 34 | Voltage divider |
| 32 | NTC Thermistor |
| 21 | SDA (LCD) |
| 22 | SCL (LCD) |

---

## Dependencies
- `Wire.h` (built-in)
- `LiquidCrystal_I2C` by Frank de Brabander

---

## Limitations
- MOSFET cutoff not yet in firmware
- No current sensing / SOC estimation
- WiFi unused — local display only

---

## Roadmap
- [ ] Firmware MOSFET cutoff triggers
- [ ] ACS712 current sensor + SOC via coulomb counting
- [ ] WiFi dashboard for remote monitoring
- [ ] Scale to 4S with BQ76930 for real EV use

---

## Future Scope

**Short Term**
- Implement firmware-level fault detection and automatic MOSFET cutoff
- Add ACS712 current sensor to enable real-time current monitoring
- Estimate State of Charge (SOC) using coulomb counting
- Add audible buzzer alert on thermal or voltage fault

**Medium Term**
- Build a WiFi-based web dashboard to monitor pack health remotely from any browser
- Add data logging to SD card or cloud for long-term battery health analysis
- Implement active cell balancing for better efficiency over passive resistor bleeding
- Add a dedicated gate driver IC (IR2104) for faster and cleaner MOSFET switching

**Long Term**
- Upgrade to BQ76930 / BQ76940 IC to support 10S–15S packs for real EV deployment
- Integrate CAN bus communication to interface with EV motor controllers
- Design a custom PCB to replace the breadboard — compact, noise-resistant, robust
- Implement Kalman Filter based SOC estimation for higher accuracy
- Add cell-level temperature monitoring with multiple thermistors across the pack
- Develop a mobile app for real-time BMS telemetry and alerts

---

> Academic prototype — 2S pack simulates an EV battery demonstrating
> core BMS functions: voltage monitoring, thermal monitoring, and hardware protection.
