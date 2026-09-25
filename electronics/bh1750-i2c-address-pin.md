---
date: 2026-09-25
tags: [electronics, i2c, bh1750, light-sensor]
---

# BH1750 light sensor — ADDR pin sets the I2C address

Added a BH1750 (GY-302) light sensor to the shared I2C bus alongside OLED and BME280, completing a 3-sensor weather station on one ESP32.

## What happened / Why this matters

The GY-302 breakout has 5 pins: VCC, GND, SCL, SDA, and ADDR — one more than BME280/OLED. ADDR selects the I2C address: tying it to GND gives address 0x23 (the common default), tying it to VCC gives 0x5C. Left floating it often still works but is less reliable (susceptible to noise). Connected ADDR to the same GND row as the other GND wire, matching the 0x23 address used in the driver code.

Shared the same SDA/SCL/GND rows already used by OLED and BME280 (I2C is a bus — any number of devices can share the same two signal lines), only needing a new VCC wire since all three devices run on 3.3V here (unlike the OLED which specifically needed VIN/5V, wait no OLED also ended up fine on 3.3V once SDA/SCL were correctly wired).

## Key takeaway

When a breakout board has more pins than the minimum for its protocol (5 pins for an I2C sensor instead of the usual 4), the extra pin is often an address-select line — check the datasheet/silkscreen for what it does before assuming it's optional. Tying it to a defined level (GND or VCC) rather than leaving it floating is the safer choice, especially on a noisy breadboard.

Related: [[oled-ssd1306-i2c-swapped-pins]], [[sh1106-vs-ssd1306-addressing]]
