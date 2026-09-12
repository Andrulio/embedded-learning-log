---
date: 2026-09-11
tags: [tools, esp-idf, windows, setup, cp2102, driver]
---

# ESP-IDF setup on Windows

## Installation

Download and install ESP-IDF from https://dl.espressif.com/dl/esp-idf/

Installs to `C:\Espressif` by default.

## Important: use ESP-IDF PowerShell

`idf.py` does NOT work in regular CMD or PowerShell. You must use **ESP-IDF PowerShell** (find it in the Start Menu). It sets up all environment variables (IDF_PATH, compiler paths, etc.).

```
(venv) PS C:\> idf.py --version
ESP-IDF v6.1
```

## Creating a project

```
cd D:\esp32-projects
idf.py create-project esp32-i2c-bme280-driver
cd esp32-i2c-bme280-driver
idf.py build
```

## Flashing

1. Connect ESP32 via USB
2. Find COM port in Device Manager → Ports (COM & LPT)
3. Flash and monitor:

```
idf.py -p COM3 flash monitor
```

Press `Ctrl+]` to exit monitor.

## CP2102 driver

If ESP32 shows as unknown device with yellow triangle in Device Manager — need to install CP2102 driver from Silicon Labs: https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers

Install via Device Manager → right-click device → Update driver → Browse → point to extracted folder.

Related: [[clion-with-esp-idf]], [[gpio-blink-led]]
