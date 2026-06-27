# Artificial Satellite System â€” CanSat Onboard Data Logger

A MicroPython-based onboard data acquisition system for a **CanSat** (can-sized satellite), running on the **Raspberry Pi Pico (RP2040)**. The system reads telemetry from multiple sensors in real-time and logs all data to an SD card in CSV format.

---

## Overview

This project simulates onboard satellite telemetry by collecting environmental and motion data from a payload-grade sensor suite. It is designed for **CanSat competitions** and college-level satellite model projects.

The system continuously reads:
- **Atmospheric pressure and temperature** â€” via BMP280 barometric sensor
- **Gas concentrations** â€” LPG, Smoke, Methane, Hydrogen via MQ2 sensor
- **6-axis IMU data** â€” Accelerometer (Ax, Ay, Az) and Gyroscope (Gx, Gy, Gz) via MPU6050
- All data is timestamped and saved to an SD card as a `.csv` file for post-flight analysis

---

## Hardware Setup

### Components

| Component | Interface | Description |
|-----------|-----------|-------------|
| Raspberry Pi Pico (RP2040) | â€” | Main microcontroller |
| BMP280 | I2C (SDA=GP0, SCL=GP1) | Pressure + Temperature sensor |
| MPU6050 | I2C (SDA=GP0, SCL=GP1) | 6-axis IMU (Accel + Gyro) |
| MQ2 Gas Sensor | ADC (GP26) | LPG / Smoke / Methane / Hydrogen detection |
| MicroSD Card Module | SPI (SCK=GP14, MOSI=GP15, MISO=GP12) | Data storage |

### Wiring Summary

```
Pico GP0  â”€â”€â”€â”€â”€â”€â”€â”€ SDA (BMP280 + MPU6050)
Pico GP1  â”€â”€â”€â”€â”€â”€â”€â”€ SCL (BMP280 + MPU6050)
Pico GP14 â”€â”€â”€â”€â”€â”€â”€â”€ SCK (SD Card)
Pico GP15 â”€â”€â”€â”€â”€â”€â”€â”€ MOSI (SD Card)
Pico GP12 â”€â”€â”€â”€â”€â”€â”€â”€ MISO (SD Card)
Pico GP26 â”€â”€â”€â”€â”€â”€â”€â”€ AOUT (MQ2)
```

---

## Software Architecture

```
main.py (satellite_system.py)
â”œâ”€â”€ I2C Init          â”€â”€ Scan and connect I2C devices
â”œâ”€â”€ MQ2 Init          â”€â”€ Calibrate gas sensor (ADC on GP26)
â”œâ”€â”€ MPU6050 Init      â”€â”€ Connect via I2C for 6-axis data
â”œâ”€â”€ BMP280 Init       â”€â”€ Connect via I2C for pressure/temp
â”œâ”€â”€ SD Card Init      â”€â”€ Mount SD via SPI, create log file
â””â”€â”€ Data Loop (âˆž)
    â”œâ”€â”€ Read timestamp (ms)
    â”œâ”€â”€ Read pressure, temperature (BMP280)
    â”œâ”€â”€ Read LPG, Smoke, Methane, Hydrogen (MQ2)
    â”œâ”€â”€ Read Ax, Ay, Az, Gx, Gy, Gz (MPU6050)
    â””â”€â”€ Write comma-separated row to CSV
```

---

## CSV Output Format

The SD card log file contains the following columns:

```
Time, pressure, Temperature, smoke, lpg, methane, Hydrogen, Ax, Ay, Az, Gx, Gy, Gz
```

Example row:
```
12.345, 1013.25, 28.4, 0.12, 0.08, 0.03, 0.01, 0.01, -0.02, 9.80, 0.003, -0.001, 0.002
```

---

## File Structure

```
Artificial-Satellite-System/
â”œâ”€â”€ README.md
â”œâ”€â”€ satellite_system.py    â† Main onboard MicroPython script
â”œâ”€â”€ chittiSat/             â† Custom sensor library modules
â”‚   â”œâ”€â”€ mq2.py             â† MQ2 gas sensor driver
â”‚   â”œâ”€â”€ gyro.py            â† MPU6050 IMU driver
â”‚   â”œâ”€â”€ pressure.py        â† BMP280 pressure/temperature driver
â”‚   â”œâ”€â”€ sdcard.py          â† SD card SPI driver
â”‚   â””â”€â”€ assistant.py       â† Helper utilities (file naming, etc.)
â””â”€â”€ docs/
    â””â”€â”€ circuit_diagram.png â† Hardware wiring diagram
```

> **Note:** The main script is currently named `"fully working python code"` without a `.py` extension. Rename it to `satellite_system.py` for proper GitHub syntax highlighting.

---

## How to Run

1. Install MicroPython on Raspberry Pi Pico
2. Upload all files to the Pico filesystem using Thonny IDE or `rshell`
3. Connect sensors as per the wiring diagram
4. Insert a FAT-formatted microSD card
5. Power on â€” the system starts logging automatically, printing confirmation to REPL

```python
# Expected serial output
[60]           â† I2C device addresses found
sd card connected
['LOG_001.csv']
our data saved   â† Repeats every loop iteration
```

---

## What to Add Next

- [ ] Rename `"fully working python code"` â†’ `satellite_system.py`
- [ ] Add `chittiSat/` sensor library files
- [ ] Add `docs/circuit_diagram.png` â€” hardware wiring photo or schematic
- [ ] Add sample `LOG_001.csv` output file for reference
- [ ] Add altitude calculation from pressure data
- [ ] Set GitHub topics: `micropython` `raspberry-pi-pico` `cansat` `iot` `satellite` `embedded-systems` `sensors`

---

## Dependencies

All libraries are part of the custom `chittiSat` package included in this repo. MicroPython standard library (`machine`, `utime`, `uos`) is used for hardware interfaces.

---

## License

MIT License
