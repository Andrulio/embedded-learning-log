---
date: 2026-09-11
tags: [tools, clion, ide, cmake, setup]
---

# CLion with ESP-IDF

## Setup

No special plugins needed — CLion supports CMake natively, and ESP-IDF uses CMake.

## Important: launch CLion from ESP-IDF PowerShell

CLion needs ESP-IDF environment variables to work properly. Always start it from ESP-IDF PowerShell:

```
clion64.exe
```

Or the full path if it's not in PATH:

```
& "C:\Program Files\JetBrains\CLion\bin\clion64.exe"
```

## Opening a project

In CLion: File → Open → select the project folder (e.g., `D:\esp32-projects\esp32-i2c-bme280-driver`).

CLion picks up `CMakeLists.txt` automatically and sets up indexing. After that: autocompletion, code navigation, error highlighting — everything works.

## Building and flashing

Still done from the terminal (ESP-IDF PowerShell):

```
idf.py build
idf.py -p COM3 flash monitor
```

Related: [[esp-idf-setup-windows]]
