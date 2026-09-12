---
date: 2026-09-11
tags: [electronics, resistor, ohms-law, led, beginner]
---

# Why you need a resistor before an LED

Without a resistor, too much current flows through the LED — it burns out, or worse, the GPIO pin on the microcontroller dies.

## The math (Ohm's law)

```
I = V / R
```

- ESP32 GPIO outputs 3.3V
- LED drops ~2V (forward voltage)
- Remaining voltage on resistor: 3.3 - 2.0 = 1.3V
- With 220 ohm: I = 1.3 / 220 = ~6mA — safe and bright

## Effect of different resistor values

| Resistor | Current | Brightness |
|----------|---------|------------|
| 220 ohm  | ~6mA    | Bright     |
| 330 ohm  | ~4mA    | Normal     |
| 1k ohm   | ~1.3mA  | Dim        |
| None     | ???     | Dead       |

ESP32 GPIO max is 40mA per pin. Always use a resistor.

Related: [[led-polarity]], [[gpio-blink-led]]
