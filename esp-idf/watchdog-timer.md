---
date: 2026-09-11
tags: [esp-idf, freertos, watchdog, debugging, error]
---

# Watchdog timer

ESP32 has a watchdog that monitors if the system is responsive. If a task hogs the CPU without yielding, the watchdog triggers and logs an error.

## What happened

Tried to do manual PWM with very short delays:

```c
gpio_set_level(LED_PIN, 1);
vTaskDelay(pdMS_TO_TICKS(1));   // 1ms
gpio_set_level(LED_PIN, 0);
vTaskDelay(pdMS_TO_TICKS(9));   // 9ms
```

Got this error:

```
E (5261) task_wdt: Task watchdog got triggered.
E (5261) task_wdt:  - IDLE0 (CPU 0)
```

## Why

`pdMS_TO_TICKS(1)` converts to **0 ticks** because the default FreeRTOS tick period is 10ms. So `vTaskDelay(0)` doesn't actually delay — the loop runs non-stop, starving the IDLE task, and the watchdog fires.

## Lesson

- FreeRTOS tick = 10ms by default. Anything below 10ms rounds to 0.
- Always make sure your loops yield enough time for the system.
- For fast toggling (PWM), use hardware modules like [[pwm-vs-gpio-toggle|LEDC]] instead of manual delays.

Related: [[gpio-blink-led]], [[pwm-vs-gpio-toggle]]
