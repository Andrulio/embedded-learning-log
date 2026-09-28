# Ideas for what to learn next

A running list of topics that would naturally extend today's work (2026-09-25) — not learned yet, just queued up for future sessions.

## To finish the meteo station
- **NVS (Non-Volatile Storage)** — save settings (screen brightness, logging interval) so they survive a reboot
- **Deep sleep / power management** — if it ever runs on the 18650 battery instead of USB, this is essential for battery life
- **SPI protocol** — needed for the microSD module (different from I2C, worth understanding the difference hands-on)
- **MQ-135 calibration** — converting raw ADC/voltage into actual ppm requires calibrating R0 in clean air; currently just showing raw values
- **Interrupts (GPIO ISR)** — proper way to handle the tactile buttons instead of polling in the main loop
- **WiFi + a simple web server or MQTT** — push sensor data somewhere instead of only showing it on the OLED (could feed a dashboard)

## Broader ESP-IDF/embedded skills worth picking up eventually
- **FreeRTOS tasks and queues** — right now everything runs in one loop in `app_main`; real projects split work into separate tasks
- **I2C vs SPI vs UART** — solidify the mental model of when each protocol is used and why (already have I2C experience now)
- **Timers (esp_timer)** — for anything that needs to happen on a schedule without blocking the main loop
- **OTA (over-the-air) updates** — flashing new firmware without a USB cable, useful once a project is enclosed and harder to access

## Relevant to the other 2 portfolio projects
- **SPI/LoRa module basics (SX1276/SX1278)** — needed for the LoRa Messenger project
- **AES encryption on embedded hardware** — for the "encrypted" part of the LoRa messenger
- **Sub-GHz / IR / RFID basics** — needed for the DIY Flipper Zero Lite project

Not urgent — pick whichever feels most useful next time, or follow curiosity.
