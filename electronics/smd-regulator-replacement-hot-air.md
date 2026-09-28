---
date: 2026-09-28
tags: [electronics, hot-air-rework, smd, troubleshooting, power-regulator]
---

# Replacing a burnt SOT-223 regulator with hot air

First real repair job: an AMS1117-3.3 (linear voltage regulator, SOT-223, 1A) had burned out on an old ESP32 dev board. Replaced it using a hot air rework station, without a bench power supply — just a multimeter and a USB port.

## What happened / Why this matters

Before touching the iron/hot air: checked for a dead short on the 3.3V and 5V rails with a multimeter in continuity mode (probe on GND, probe on the rail) with the board fully unpowered. No short found, so it was safe to proceed with desoldering.

Removal: covered nearby components with kapton tape, applied flux, heated the regulator evenly at ~300-330°C until all 4 contacts (3 legs + the GND tab) released together, lifted with tweezers. Cleaned the pads with solder wick, tacked the new part with one leg first to fix orientation, then reflowed the rest.

Biggest gotcha during testing: probed the rightmost leg of the new regulator against board GND and got continuity (~0.005Ω, buzzer beeping) — looked like a dead short. It wasn't. **The SOT-223 package's metal tab is internally bonded to one of the three front legs** (the GND leg) inside the component itself. So GND-leg-to-tab (or GND-leg-to-board-GND) reading near 0Ω is expected, not a fault. The real bridge-detection test is checking *between the front legs themselves* (e.g. VOUT leg to VIN leg) — those should NOT read near 0Ω.

No bench power supply available, so verified the repair by powering through a plain USB cable (which has its own built-in current limit, ~500mA-1A) instead of a lab PSU: plugged in briefly first while watching/smelling for anything wrong, then left it connected and measured VIN (~5V) and VOUT (~3.3V) on the regulator directly with the multimeter.

## Key takeaway

1. Always check for a dead short with a multimeter (power off) before applying any power to a repaired board.
2. On components with a tab/exposed pad (SOT-223, DPAK, QFN, etc.), the tab is often internally tied to one of the pins — a low-resistance reading between that pin and the tab (or board GND if that pin is GND) is normal, not a short. Test pin-to-pin between the *other* pins to actually catch a solder bridge.
3. Without a lab power supply, a plain USB port's built-in current limit is a workable (if less controlled) substitute for a first power-up test — apply briefly, watch/smell for trouble, then measure.

Related: [[first-soldering-pin-headers]], [[adc-oneshot-migration]]
