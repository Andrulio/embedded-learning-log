---
date: 2026-09-13
tags: [esp-idf, ledc, pwm, led, animation]
---

# Knight Rider — PWM crossfade chase across 3 LEDs

Three LEDs crossfade in sequence back and forth (LED1→LED2→LED3→LED2→LED1), creating a smooth "Knight Rider" running light effect using hardware PWM.

## What happened / Why this matters

Extended the two-LED crossfade to three LEDs with a bounce pattern. The key insight: the animation is just 4 crossfade transitions in sequence, each using the same `for` loop pattern but with different LEDC channel pairs. One timer drives all three channels.

## Key takeaway

Any multi-LED animation can be broken into pairwise transitions. The Knight Rider pattern is 4 transitions: 0→1, 1→2, 2→1, 1→0. Each transition uses the same code — `8191 - i` for the fading-out channel, `i` for the fading-in channel. Only the channel numbers change between transitions.

## Code

```c
// 4 transitions make the bounce pattern:
// 1. LED1→LED2: CH0 fades out, CH1 fades in
// 2. LED2→LED3: CH1 fades out, CH2 fades in
// 3. LED3→LED2: CH2 fades out, CH1 fades in
// 4. LED2→LED1: CH1 fades out, CH0 fades in

for (int i = 0; i <= 8191; i += 32) {
    ledc_set_duty(LEDC_LOW_SPEED_MODE, fade_out_ch, 8191 - i);
    ledc_update_duty(LEDC_LOW_SPEED_MODE, fade_out_ch);
    ledc_set_duty(LEDC_LOW_SPEED_MODE, fade_in_ch, i);
    ledc_update_duty(LEDC_LOW_SPEED_MODE, fade_in_ch);
    vTaskDelay(pdMS_TO_TICKS(10));
}
```

Related: [[ledc-crossfade-two-leds]], [[traffic-light-3-leds]], [[pwm-vs-gpio-toggle]]
