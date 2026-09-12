---
date: 2026-09-11
tags: [esp-idf, gpio, led, freertos, beginner]
---

# GPIO blink — first program

The "Hello World" of embedded. Toggle a GPIO pin on and off to blink an LED.

## Circuit

```
ESP32 GPIO2 → 220 ohm resistor → LED (+) → LED (-) → GND
```

## Code

```c
#include <stdio.h>
#include "driver/gpio.h"
#include "freertos/FreeRTOS.h"
#include "freertos/task.h"

#define LED_PIN GPIO_NUM_2

void app_main(void)
{
    gpio_reset_pin(LED_PIN);
    gpio_set_direction(LED_PIN, GPIO_MODE_OUTPUT);

    while (1) {
        gpio_set_level(LED_PIN, 1);   // ON
        vTaskDelay(pdMS_TO_TICKS(500));
        gpio_set_level(LED_PIN, 0);   // OFF
        vTaskDelay(pdMS_TO_TICKS(500));
    }
}
```

## Key functions

- `gpio_reset_pin()` — reset pin to default state
- `gpio_set_direction()` — set as input or output
- `gpio_set_level(pin, 0 or 1)` — set pin HIGH or LOW
- `vTaskDelay(pdMS_TO_TICKS(ms))` — delay in milliseconds (FreeRTOS)

## Note

GPIO2 on many ESP32 DevKit boards has a **built-in blue LED**. So even without an external LED, you can see the blink on the board itself.

Related: [[resistor-current-limiting]], [[led-polarity]], [[watchdog-timer]], [[breadboard-basics]]
