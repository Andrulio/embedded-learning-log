---
date: 2026-09-12
tags: [esp-idf, wifi, networking, events]
---

# WiFi Station mode — connecting to a network

ESP32 connects to a WiFi network as a client (station mode) and receives an IP address.

## What happened / Why this matters

Before the weather station project, needed to verify that ESP32 can connect to WiFi — this is how it will send sensor data later via MQTT. The setup involves a lot of boilerplate but follows a clear pattern.

## Key takeaway

WiFi setup has 6 steps: init NVS (flash storage for WiFi settings) → init network interface → init WiFi driver → register event handlers → set SSID/password config → start. The event-driven model is key: you register a handler function that reacts to events like "connected", "disconnected", "got IP". The handler runs automatically when events happen — you don't poll in a loop.

## Code

```c
// Event handler — reacts to WiFi events
static void wifi_event_handler(void *arg, esp_event_base_t event_base,
                               int32_t event_id, void *event_data)
{
    if (event_base == WIFI_EVENT && event_id == WIFI_EVENT_STA_START) {
        esp_wifi_connect();
    }
    else if (event_base == WIFI_EVENT && event_id == WIFI_EVENT_STA_DISCONNECTED) {
        esp_wifi_connect();  // auto-reconnect
    }
    else if (event_base == IP_EVENT && event_id == IP_EVENT_STA_GOT_IP) {
        ip_event_got_ip_t *event = (ip_event_got_ip_t *)event_data;
        printf("Connected! IP: " IPSTR "\n", IP2STR(&event->ip_info.ip));
    }
}

// In app_main: init, configure, start
nvs_flash_init();
esp_netif_init();
esp_event_loop_create_default();
esp_netif_create_default_wifi_sta();
wifi_init_config_t cfg = WIFI_INIT_CONFIG_DEFAULT();
esp_wifi_init(&cfg);
// ... register handlers, set config, esp_wifi_start()
```

Related: [[gpio-blink-led]], [[watchdog-timer]]
