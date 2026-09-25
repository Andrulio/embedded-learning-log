---
date: 2026-09-25
tags: [electronics, i2c, pull-up-resistors, breadboard, multimeter]
---

# I2C pull-up resistors and diagnosing loose breadboard contacts

When running two I2C devices (OLED + BME280) on a shared bus, added external pull-up resistors on SDA/SCL to fight display noise — and learned how to tell a genuinely bad connection from a false alarm using a multimeter.

## What happened / Why this matters

Two devices sharing an I2C bus over breadboard jumper wires showed display corruption ("noise"). Standard fix: add 4.7kΩ (or 2.2k–10k range) pull-up resistors from SDA and SCL to 3.3V, in the same breadboard rows the existing wires already used (no new wires to the ESP32 needed — I2C is a shared bus).

While testing, multimeter continuity mode ("beep" mode) falsely suggested a resistor was fine — continuity mode only beeps for very low resistance (tens of ohms), so a 4.7kΩ resistor should NOT beep in that mode. The correct check is resistance mode (Ω), reading a value close to the resistor's rated value. When resistance measured unstable (jumping between values, e.g. 2.7k to 4.7k), that indicated a loose physical contact — a resistor leg not fully seated in the breadboard hole — not a wrong component. Reseating fixed the jumping.

## Key takeaway

To verify a resistor (or any specific component) is actually in-circuit and working, measure **resistance (Ω mode)** across it directly — continuity/beep mode only catches near-zero-ohm shorts and is useless for anything above ~50Ω. A reading that jumps between values while probes stay still means a loose mechanical contact, not a component or wiring problem — reseat the part firmly. For end-to-end wiring verification (e.g. confirming a signal reaches from one board's pin all the way to another board's pin through several breadboard rows), measuring resistance directly between the two endpoints (should read ~0Ω) is more reliable than checking each wire segment separately.

Related: [[breadboard-debugging]], [[oled-ssd1306-i2c-swapped-pins]], [[sh1106-vs-ssd1306-addressing]]
