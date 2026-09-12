---
date: 2026-09-11
tags: [electronics, led, basics, beginner]
---

# LED polarity

LEDs only work in one direction. If nothing lights up — try flipping it.

## How to tell which leg is which

- **Long leg** = anode (+) — connects toward the signal/power
- **Short leg** = cathode (-) — connects toward GND

## Circuit direction

```
GPIO pin → resistor → long leg (+) → LED → short leg (-) → GND
```

If the LED doesn't light up and the code is correct, the first thing to try is reversing the LED.

Related: [[resistor-current-limiting]], [[gpio-blink-led]], [[breadboard-basics]]
