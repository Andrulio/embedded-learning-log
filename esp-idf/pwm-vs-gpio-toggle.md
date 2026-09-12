---
date: 2026-09-11
tags: [esp-idf, pwm, ledc, gpio, led]
---

# PWM: gpio_set_level vs LEDC

## The problem

Wanted to make an LED "breathe" (smooth dimming). Tried toggling GPIO manually with different on/off ratios — didn't work well.

## Manual approach (bad)

```c
// 10% brightness attempt
gpio_set_level(LED_PIN, 1);
vTaskDelay(pdMS_TO_TICKS(10));   // on 10ms
gpio_set_level(LED_PIN, 0);
vTaskDelay(pdMS_TO_TICKS(90));   // off 90ms
```

Result: LED just **blinks visibly** instead of appearing dim. The total period (100ms = 10Hz) is too slow for the eye to perceive as continuous light.

## Why it doesn't work

- Need thousands of toggles per second for smooth dimming
- `vTaskDelay` minimum is 10ms (1 tick) — not fast enough
- Even if it were faster, it wastes CPU cycles

## The right approach: LEDC (hardware PWM)

ESP32 has a dedicated LEDC module that toggles the pin in hardware at any frequency, without using the CPU.

Key concepts:
- **Frequency**: how fast it toggles (e.g., 5000 Hz)
- **Duty cycle**: % of time the pin is HIGH (0% = off, 50% = half bright, 100% = full)
- **Resolution**: how many steps between 0% and 100% (13-bit = 0 to 8191)

Functions: `ledc_timer_config()`, `ledc_channel_config()`, `ledc_set_duty()`, `ledc_update_duty()`

## Lesson

For anything that needs fast, precise toggling — use hardware peripherals, not software loops.

Related: [[gpio-blink-led]], [[watchdog-timer]]
