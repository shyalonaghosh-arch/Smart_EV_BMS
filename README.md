# Smart_EV_BMS
Overview
A BMS is the brain behind every safe Li-ion battery pack. Without one, a Li-ion battery is unprotected against overcharging, over-discharging, overheating, and short circuits — all of which can cause permanent damage or thermal runaway.
This prototype implements a BMS on the ESP32-DevKit V1 that monitors a 2S Li-ion pack in real time, displaying live voltage and temperature on a 16x2 I2C LCD. A MOSFET-based hardware protection circuit physically disconnects the battery when unsafe conditions are detected.

How the System Works
Voltage Monitoring
The ESP32 ADC can only read 0–3.3V safely. A voltage divider (R1 = 10kΩ, R2 = 4.7kΩ) scales the 8.4V pack voltage down to a safe range. The firmware then reverses the calculation to recover actual battery voltage.
Temperature Monitoring
An NTC thermistor's resistance drops as temperature rises. The firmware uses the Steinhart-Hart B-coefficient equation (B = 3435) to accurately convert the raw resistance reading into °C.
MOSFET Protection
Discharge — IRLZ44N (N-Channel)
Sits on the negative discharge path. ESP32 drives its gate HIGH under normal conditions (load connected). On a fault, gate goes LOW — load is instantly disconnected. The IRLZ44N is logic-level so ESP32's 3.3V GPIO drives it directly with no gate driver needed.
Charge — AO3401 / IRF9540N (P-Channel)
Sits on the charge input path. When the pack hits full charge (≥ 8.4V), ESP32 pulls the gate HIGH, turning the MOSFET OFF and stopping charge current.
Having separate charge and discharge MOSFETs means the BMS can independently block charging, discharging, or both — exactly how real EV BMS systems work.
Gate Resistors (10Ω) limit inrush current during switching, protecting ESP32 GPIO pins from voltage spikes.
Cell Balancing Resistors (220Ω) bleed excess charge from the higher voltage cell as heat, keeping both cells at equal voltage over time (passive balancing).
Fuse
A 1A fuse is the last line of defence. If a catastrophic short bypasses the MOSFETs, the fuse blows and permanently breaks the circuit.
Decoupling Capacitors

100nF ceramic — filters high frequency noise, keeps ADC readings clean
100µF electrolytic — smooths sudden current demands from ESP32


Hardware
ComponentModelQtyMicrocontrollerESP32-DevKit V11NTC ThermistorNTCLE100E3103JB01Protection MOSFETIRLZ44N1Charge Control MOSFETAO3401 / IRF9540N1Fuse1A1Resistor10kΩ5Resistor4.7kΩ2Resistor220Ω2Resistor10Ω2Capacitor100nF 50V ceramic2Capacitor100µF 50V electrolytic1LCD16x2 with I2C module1Breadboard830-point1Jumper WiresM-M + M-F 40pcs2 setsBatteryLi-ion 3.7V cells in 2S2

Pin Configuration
ESP32 PinConnected ToGPIO 34Voltage divider outputGPIO 32NTC Thermistor outputGPIO 21I2C SDA (LCD)GPIO 22I2C SCL (LCD)

Software Dependencies

Wire.h — built-in
LiquidCrystal_I2C — by Frank de Brabander (install via Library Manager)


How to Flash

Install Arduino IDE
Add ESP32 board support via File → Preferences → Additional Board URLs:

https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json

Select Tools → Board → ESP32 Dev Module
Install libraries and upload


Battery Specs
ParameterValueConfiguration2S1PNominal voltage7.4VFull charge voltage8.4VCut-off voltage5.0V

Limitations

MOSFET cutoff not yet implemented in firmware
No current sensing or SOC estimation
Single thermistor, one monitoring point only
WiFi unused — display only

Future Improvements

Firmware-level MOSFET cutoff triggers
ACS712 current sensor for SOC estimation
WiFi dashboard for remote monitoring
Scale to 4S+ pack with BQ76930 for real EV deployment


Project Context
This is a scaled-down academic prototype of a Smart EV BMS. The 2S pack simulates an EV battery, demonstrating core BMS functions — voltage monitoring, thermal monitoring, and hardware protection. The same architecture scales directly to real EV deployment with higher cell counts and current ratings.
