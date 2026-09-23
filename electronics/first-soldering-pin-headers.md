---
date: 2026-09-23
tags: [electronics, soldering, bme280, pin-headers]
---

# First soldering — pin headers on BME280

Soldered pin headers onto a GY-BME280 sensor module for the first time using an HX36 2-in-1 soldering/rework station.

## What happened / Why this matters

Needed to attach pin headers to the BME280 breakout board before it could be plugged into a breadboard for the weather station project. This was the first time using a soldering iron.

## Key takeaway

For through-hole pin headers: insert the long pins into a breadboard (acts as a jig to hold them straight), place the module on top, then solder the short ends poking through the pads on top. Use rosin flux on the iron tip before each joint, heat both the pin and pad for 2-3 seconds, then feed solder into the joint (not onto the iron). A good joint looks like a shiny cone. Verify each joint with a multimeter in continuity mode — touch the long pin below and the solder joint above, it should beep.

Settings: soldering iron at 320-350 C (SOLDER channel on HX36). REWORK (hot air) is for SMD components, not needed here.

Related: [[breadboard-basics]], [[breadboard-debugging]], [[resistor-current-limiting]]
