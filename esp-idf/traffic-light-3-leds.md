---
date: 2026-09-12
tags: [esp-idf, gpio, multiple-pins, led, traffic-light]
---

# Traffic light with 3 LEDs

Controlling multiple GPIO pins to simulate a traffic light — red, yellow, green LEDs switching in sequence with different timing.

## What happened / Why this matters

After SOS with one LED, moved to controlling 3 LEDs on separate GPIO pins (GPIO2, GPIO4, GPIO5). Each LED has its own resistor and connects to a shared GND rail. The code uses separate functions for each color with different delay times — red 5s, yellow 2s, green 4s — to mimic a real traffic light.

## Key takeaway

Controlling multiple GPIOs is the same pattern repeated — `gpio_reset_pin()` + `gpio_set_direction()` for each pin in `app_main()`, then `gpio_set_level()` to toggle. Each LED needs its own `#define`, its own init, and its own resistor, but they all share GND.

## Code

```c
#define RED GPIO_NUM_2
#define YELLOW GPIO_NUM_4
#define GREEN GPIO_NUM_5

void red() {
    gpio_set_level(RED, 1);
    vTaskDelay(pdMS_TO_TICKS(5000));
    gpio_set_level(RED, 0);
}
void yellow() {
    gpio_set_level(YELLOW, 1);
    vTaskDelay(pdMS_TO_TICKS(2000));
    gpio_set_level(YELLOW, 0);
}
void green() {
    gpio_set_level(GREEN, 1);
    vTaskDelay(pdMS_TO_TICKS(4000));
    gpio_set_level(GREEN, 0);
}
```

Related: [[gpio-blink-led]], [[sos-morse-code]], [[breadboard-debugging]]
