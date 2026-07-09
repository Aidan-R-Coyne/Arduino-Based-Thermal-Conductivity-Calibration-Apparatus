# Arduino-Based-Thermal-Conductivity-Calibration-Apparatus
An automated thermodynamic testing rig using an Elegoo Mega R3 and RTD PT100 sensor with a 4-20mA current loop. The apparatus isolates material type as the independent variable to capture real-time transient temperature data, demonstrating thermal conductivity differentials and heat transfer via the Second Law of Thermodynamics.

# Automated Thermodynamic Testing Rig & RTD Instrumentation System

An automated thermodynamic testing rig using an Elegoo Mega R3 and RTD PT100 sensor with a 4-20mA current loop. The apparatus isolates material type as the independent variable to capture real-time transient temperature data, demonstrating thermal conductivity differentials and heat transfer via the Second Law of Thermodynamics.

---

## Project Overview & Objectives
The purpose of this project is to build an instrumentation rig capable of measuring and logging the temperature profiles of different materials under uniform heating and cooling conditions. By analyzing how different objects (such as metals, plastics, and wood) absorb and reject thermal energy over time, the system provides a clear, data-driven look at transient heat transfer and material thermal conductivity.

---

## System Architecture & Hardware
The apparatus utilizes a classic industrial instrumentation design, deploying a 4-20 mA loop-powered transmitter to convert low-voltage sensor resistance changes into a robust analog signal resilient to noise.

### Hardware Component List
* **Microcontroller:** ELEGOO Mega R3 (Arduino Mega 2560 compatible)
* **Temperature Sensor:** RTD PT100 Probe
* **Signal Conditioning:** 4-20 mA Loop-Powered Transmitter
* **Power Supply:** Adjustable 24V DC Bench Power Supply
* **Conversion Resistor:** 250 Ohm Resistor (Translates 4-20 mA current to a clean 1-5V signal for ADC sampling)
* **Thermal Sources:** Heating element / Hot plate & Ice bath for calibration
* **Test Specimens:** Samples of metal, plastic, and wood

### System Wiring Gallery
*Drag and drop your respective setup images into the placeholders below:*

| Component | Hardware Connection Image |
| :--- | :--- |
| **RTD PT100 Sensor & Transmitter** | ![RTD PT100 Sensor and Transmitter](<img width="384" height="512" alt="RTD PT100" src="https://github.com/user-attachments/assets/7d4841ec-5fad-4343-98a7-f8ebbb837304" />
) |
| **24V DC Power Supply Link** | ![24V Adjustable Power Source](<img width="384" height="512" alt="PowerSource" src="https://github.com/user-attachments/assets/49e61143-50dd-471a-8e04-d89bac125b58" />
) |
| **Full Instrumentation Setup** | ![Full Loop and Microcontroller Array](<img width="512" height="384" alt="FullSetup" src="https://github.com/user-attachments/assets/137af113-2999-4efc-8b07-aadca801a6fd" />
) |

---

## 🧠 Physics Concepts Illustrated

### A. Transient Thermodynamics & Heat Transfer
The apparatus demonstrates the **Second Law of Thermodynamics** in real time. When heated specimens are removed from the uniform thermal source, heat flows naturally down the temperature gradient from the hotter materials to the cooler ambient environment until thermal equilibrium is established.

### B. Thermal Conductivity Differentials
By manipulating the material type (**Input Variable**) while keeping the geometry and heating conditions constant, the system monitors the temperature response (**Output Variable**). Plotting the heating up and cooling down curves shows the distinctive thermal conductivity ($k$) properties of metals, plastics, and wood.

---

## Engineering Challenges & Troubleshooting

### 1. Hardware Diagnostics: The Broken Current Loop
* **The Challenge:** During initial prototype assembly, the serial console showed no temperature changes or incoming data signals.
* **The Resolution:** Using a digital multimeter (DMM) to trace the loop circuitry, I measured 0V across the signal-conditioning resistor, indicating a break in current flow. The root cause was an incorrectly rated/faulty resistor configuration. Replacing it with a verified 250-ohm resistor instantly closed the loop and restored the expected voltage drops.

### 2. Software Optimization: From Complicated Mapping to Calibrated Scaling
Early software designs overcomplicated the analog voltage translation. By streamlining formulas and executing precise calibration checks (such as ice bath testing at 0°C vs. room temperature environment testing), the conversion logic was optimized for processing speed and readout accuracy.

#### Initial Scaling Attempt (Overcomplicated)
```cpp
const int analogPin = A0;
const float Vref = 5.0;        
const int ADC_Resolution = 1023;
const float resistor = 250.0;  
const float tempMin = 0.0;    
const float tempMax = 100.0;  
const float currentMin = 4.0;  
const float currentMax = 20.0;

void setup() {
  Serial.begin(9600);
}

void loop() {
  int raw = analogRead(analogPin);
  float voltage = raw * Vref / ADC_Resolution;
  float current_mA = (voltage / resistor) * 1000.0;
  float temperature = mapCurrentToTemperature(current_mA);
  
  Serial.print("Raw ADC: ");
  Serial.print(raw);
  Serial.print(" | Voltage: ");
  Serial.print(voltage, 3);
  Serial.print(" | Temperature: ");
  Serial.print(temperature, 2);
  Serial.println(" °C");
  delay(1000);
}

float mapCurrentToTemperature(float current_mA) {
  float slope = (tempMax - tempMin) / (currentMax - currentMin);
  float temperature = tempMin + slope * (current_mA - currentMin);
  return temperature;
}
```

#### Final Production Code
```cpp
const int AnalogInput = A0;

void setup() {
  Serial.begin(9600);
}

void loop() {
  int sensorValue = analogRead(AnalogInput);
  
  // Calibrated map function based on experimental ice bath (0 C) and ambient baselines
  int temperature = map(sensorValue, 245, 430, 0, 700);
  float tempC = temperature / 10.0;
  float tempF = tempC * 9.0 / 5.0 + 32.0;
  
  Serial.print("Raw ADC: ");
  Serial.print(sensorValue);
  Serial.print(" | Voltage: ");
  Serial.print(sensorValue * (5.0 / 1023.0), 3);
  Serial.print(" V | Temperature: ");
  Serial.print(tempC, 2);
  Serial.print(" °C / ");
  Serial.print(tempF, 2);
  Serial.println(" °F");
  
  delay(1000);
}
  ```
