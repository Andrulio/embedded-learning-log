---
date: 2026-09-12
tags: [electronics, breadboard, debugging, multimeter, gnd]
---

# Breadboard debugging with a multimeter

How to find wiring problems on a breadboard using a multimeter in continuity (buzzer) mode.

## What happened / Why this matters

When building a traffic light with 3 LEDs, only the red one worked. Spent a lot of time guessing — turned out to be two separate issues:

1. **GND rail split**: many breadboards have the power rail (+ and −) split in the middle. The top and bottom halves are NOT connected. Red LED used the top half (where ESP32 GND was wired), yellow and green were on the bottom half — no ground connection.
2. **Bad breadboard contact**: one pin (D5) had a dead contact point on the breadboard — the hole simply didn't make connection. Confirmed by connecting a jumper wire directly to the pin, bypassing the breadboard.

## Key takeaway

Use the multimeter in continuity mode to test each segment of the circuit: GPIO pin → wire → resistor → LED → GND. Test each connection point-to-point. Where it doesn't beep — that's your problem. Don't assume — verify. Breadboard contacts can be unreliable, especially on cheap boards.

Related: [[breadboard-basics]], [[resistor-current-limiting]], [[traffic-light-3-leds]]
