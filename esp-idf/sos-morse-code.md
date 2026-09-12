---
date: 2026-09-12
tags: [esp-idf, gpio, morse-code, functions, loops]
---

# SOS Morse Code with GPIO

Used a single LED to blink the SOS distress signal in Morse code: three dots, three dashes, three dots, repeat.

## What happened / Why this matters

After getting the basic LED blink working, wanted a more complex blinking pattern. SOS in Morse is `... --- ...` — a perfect exercise for breaking code into reusable functions.

## Key takeaway

Splitting repeating behavior into small functions (`dot()`, `dash()`) makes code cleaner and avoids copy-paste bugs. First version had a bug — `init_dash()` accidentally called `dot()` instead of `dash()`. Naming wrapper functions clearly (like `init_dot` for "three dots") helps catch mistakes.

## Code

```c
void dot() {
    gpio_set_level(LED_PIN, 1);
    vTaskDelay(pdMS_TO_TICKS(100));
    gpio_set_level(LED_PIN, 0);
    vTaskDelay(pdMS_TO_TICKS(100));
}

void dash() {
    gpio_set_level(LED_PIN, 1);
    vTaskDelay(pdMS_TO_TICKS(500));
    gpio_set_level(LED_PIN, 0);
    vTaskDelay(pdMS_TO_TICKS(500));
}

void init_dot() {
    for (int i = 0; i < 3; i++) { dot(); }
}

void init_dash() {
    for (int i = 0; i < 3; i++) { dash(); }
}
```

Related: [[gpio-blink-led]], [[pwm-vs-gpio-toggle]]
