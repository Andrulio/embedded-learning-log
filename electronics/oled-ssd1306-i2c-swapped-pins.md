---
date: 2026-09-25
tags: [electronics, i2c, oled, ssd1306, troubleshooting]
---

# OLED SSD1306 I2C — mislabeled SCK/SDA pins on a cheap module

Connected a 1.3" OLED SSD1306 module (4 pins: VCC, GND, SCK, SDA) to an ESP32 over I2C. Wiring looked correct, but the I2C scanner found nothing until the two data pins were swapped — against what the module's own silkscreen said.

## What happened / Why this matters

Wired VCC->3V3, GND->GND, SCK->GPIO22, SDA->GPIO21 per the labels printed on the board. Verified every connection with multimeter continuity (all beeped correctly), confirmed 3.3V at VCC with USB connected, lowered I2C clock to 100kHz, and added a startup delay before scanning. The I2C scanner still found nothing at any address (1-127) — completely silent, not even a wrong-address response.

As a last resort, swapped the SCK and SDA wires (crossing them, ignoring the printed labels). The scanner immediately found the device at 0x3C and `ssd1306_init()` succeeded.

## Key takeaway

On cheap/no-name I2C breakout boards, the printed pin labels aren't always reliable — the silkscreen can have SCK and SDA swapped from the actual PCB routing. If wiring, power, and I2C speed are all verified correct (continuity checks pass, voltage is right) but a scan still finds nothing, try physically swapping the two data-line wires before concluding the module is dead.

Related: [[breadboard-debugging]], [[breadboard-component-orientation]]
