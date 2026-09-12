---
date: 2026-09-12
tags: [esp-idf, ledc, pwm, led, crossfade]
---

# LEDC crossfade between two LEDs

Using ESP32's hardware LEDC PWM module to smoothly crossfade between two LEDs — one fades out while the other fades in, then reverses.

## What happened / Why this matters

After learning that manual GPIO toggling can't do smooth dimming (too slow, triggers watchdog), used the LEDC hardware PWM to control two LEDs simultaneously. One timer drives two channels, each on its own GPIO pin. By setting complementary duty values (`i` and `8191 - i`), the LEDs crossfade smoothly.

## Key takeaway

LEDC setup requires three steps: configure a timer (frequency + resolution), configure a channel per LED (GPIO + timer), then use `ledc_set_duty()` + `ledc_update_duty()` in a loop. The `&` before a struct name passes its memory address (a pointer) — standard C pattern for passing structs to functions. 13-bit resolution gives 0–8191 range (2¹³ = 8192 values). Loop step `+= 32` reduces iterations from 8191 to ~256 for a smooth 2.5s transition.

## Code

```c
// timer config — shared by both channels
ledc_timer_config_t timer = {
    .speed_mode = LEDC_LOW_SPEED_MODE,
    .duty_resolution = LEDC_TIMER_13_BIT,  // 0-8191
    .timer_num = LEDC_TIMER_0,
    .freq_hz = 5000,
    .clk_cfg = LEDC_AUTO_CLK
};
ledc_timer_config(&timer);

// crossfade loop — complementary duty values
for (int i = 0; i <= 8191; i += 32) {
    ledc_set_duty(LEDC_LOW_SPEED_MODE, LEDC_CHANNEL_0, 8191 - i);
    ledc_update_duty(LEDC_LOW_SPEED_MODE, LEDC_CHANNEL_0);
    ledc_set_duty(LEDC_LOW_SPEED_MODE, LEDC_CHANNEL_1, i);
    ledc_update_duty(LEDC_LOW_SPEED_MODE, LEDC_CHANNEL_1);
    vTaskDelay(pdMS_TO_TICKS(10));
}
```

Related: [[pwm-vs-gpio-toggle]], [[traffic-light-3-leds]], [[gpio-blink-led]]
