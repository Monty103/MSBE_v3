## MSBE_v3

An ultra-compact, low-power wearable audio device featuring bone-conduction technology, digital PDM voice capture, and multi-gesture touch controls.

---

## 🏗️ Core Component Architecture

| Component | Part Number | Function | Interface |
| :--- | :--- | :--- | :--- |
| **Main MCU** | **nRF5340-CLAA-R** | Dual-core Bluetooth 5.4 SoC (App & Network) | N/A |
| **PMIC** | **nPM1100** | Battery charging & high-efficiency power regulation | Analog/NTC |
| **Audio Driver** | **MAX98357A (x2)** | Class D I2S Amp for Bone Conduction Speakers | I2S |
| **Microphone** | **SPH18R1LM4H-1** | Digital MEMS Microphone for Voice/Calls | PDM |
| **Motion Sensor** | **BMA400** | Ultra-low power accelerometer (Proximity/Wake-up) | I2C |
| **Touch Controller**| **MPR121** | 12-Channel Capacitive Touch (Slider/Gestures) | I2C |

---

## ⚡ System Design Details

### 1. Processing (nRF5340)
The **Application Core** handles the I2S audio stream and gesture logic from the MPR121, while the **Network Core** manages the Bluetooth Low Energy (BLE) stack. 
* **Key Config:** Uses Port 1 for high-speed audio to isolate switching noise.

### 2. Audio Output (MAX98357A)
Two drivers are utilized to provide a stereo bone-conduction experience. These chips convert the digital I2S signal from the nRF5340 directly into analog power for the transducers.
* **Feature:** Filterless Class D, ideal for compact wearable PCB routing.

### 3. Voice Input (SPH18R1LM4H-1)
A high-performance PDM microphone. It operates in multiple power modes (Sleep, Low-Power, Normal) controlled dynamically by the nRF5340’s clock frequency to preserve battery life.

### 4. Power Management (nPM1100)
The PMIC ensures maximum battery life using a high-efficiency buck regulator. 
* **Safety:** Integrated Li-Po charger with JEITA thermal monitoring via a 10k NTC thermistor.
* **Form Factor:** Optimized for WLCSP-style compact assembly.

### 5. Interaction (BMA400 & MPR121)
* **BMA400:** Acts as a "wear-detect" sensor. It triggers an interrupt to wake the system when the headset is moved or put on.
* **MPR121:** Connected to a 3-pad copper slider on the PCB. It supports:
    * **Single/Double Tap:** Play/Pause/Assistant.
    * **Swipe Left/Right:** Volume and Track control via a state-machine on the nRF5340.

---

## 🛠️ Pin Mapping Summary

| Signal | nRF5340 Pin | Hardware Pin | Shared Bus |
| :--- | :--- | :--- | :--- |
| **I2C SCL** | **P0.04** | BMA400 (12) / MPR121 (3) | Yes |
| **I2C SDA** | **P0.05** | BMA400 (2) / MPR121 (4) | Yes |
| **PDM CLK** | **P0.26** | Mic (Clock) | No |
| **PDM DATA** | **P0.25** | Mic (Data) | No |
| **I2S SCK** | **P1.12** | MAX98357A (BCLK) | Yes |
| **I2S LRCLK** | **P1.13** | MAX98357A (WS) | Yes |
| **I2S SDOUT** | **P1.14** | MAX98357A (Din) | Yes |
| **BMA INT** | **P0.06** | BMA400 (INT1) | No |
| **Touch IRQ** | **P0.08** | MPR121 (IRQ) | No |

---

## 📋 Hardware Implementation Notes
* **Touch Slider:** 3-electrode design (ELE0, ELE1, ELE2) for swipe detection.
* **Mic Configuration:** SEL pin tied to GND for mono/left channel timing.
* **I2C Mode:** MPR121 CSB pin must be tied to VDDIO to enable I2C communication.
* **Power Rail:** All sensors and MCU peripherals are powered via the 1.8V VOUT from the nPM1100.

