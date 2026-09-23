---
date: 2026-09-23
tags: [electronics, breadboard, wiring, safety]
---

# Breadboard component orientation — pins must be in separate rows

When placing a multi-pin component (like a sensor module) on a breadboard, each pin must land in a different row number. Placing pins in the same row shorts them together.

## What happened / Why this matters

Connected a BME280 module vertically on the breadboard so all 4 pins (VIN, GND, SCL, SDA) landed in the same row (e.g. A1, B1, C1, D1). Since holes A-E in the same row are internally connected, this shorted power to ground through the sensor. The ESP32 started smoking and its voltage regulator burned out. The BME280 survived (verified with multimeter: ~500 kOhm between VIN and GND — no short).

## Key takeaway

Breadboard holes A-B-C-D-E in the same numbered row are all connected. A component with multiple pins must span different row numbers: e.g. pins in A1, A2, A3, A4 (each in its own row). Never A1, B1, C1, D1 (all in row 1 = all shorted). This applies to any module with pin headers — sensors, displays, motor drivers. When in doubt, use the multimeter continuity mode to check which holes are connected before powering on.

Related: [[breadboard-basics]], [[breadboard-debugging]], [[first-soldering-pin-headers]]
