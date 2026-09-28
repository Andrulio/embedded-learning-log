---
date: 2026-09-28
tags: [esp-idf, i2c, migration]
---

# Legacy I2C driver removed — use driver/i2c_master.h (bus + device handles)

`driver/i2c.h` (the old `i2c_cmd_link_create` / `i2c_master_start` / `i2c_master_write_byte` API) is also gone in ESP-IDF v6.1, same story as the ADC driver. Replaced by `driver/i2c_master.h`, which is a real API redesign, not just a rename.

## What happened / Why this matters

The meteo station's whole sensor stack (SSD1306 OLED, BME280, BH1750) shared one I2C bus set up with `i2c_param_config` + `i2c_driver_install`, then each driver built manual command links (`i2c_cmd_link_create`, `start`/`write_byte`/`stop`). All three headers failed with `fatal error: driver/i2c.h: No such file or directory` on ESP-IDF v6.1.

Also learned the hard way (twice) to check *which machine's files are actually being edited* before trusting a "fixed" file — a cloud-container copy and the real Windows project are not automatically synced.

## Key takeaway

New model: one shared `i2c_master_bus_handle_t` for the whole bus (created once in `app_main` with `i2c_new_master_bus`), and each peripheral driver adds its own `i2c_master_dev_handle_t` on that bus (`i2c_master_bus_add_device`) instead of taking a raw port number. Transfers are plain calls — `i2c_master_transmit`, `i2c_master_receive`, `i2c_master_transmit_receive` (write-then-read with repeated start in one call) — no more manually building start/stop/ack sequences. Component to require: `driver` (and on some IDF versions the split-out `esp_driver_i2c` explicitly).

## Code

```c
// main.c — bus created once, handed to every sensor driver
i2c_master_bus_config_t bus_config = {
    .i2c_port = I2C_NUM_0,
    .sda_io_num = GPIO_NUM_21,
    .scl_io_num = GPIO_NUM_22,
    .clk_source = I2C_CLK_SRC_DEFAULT,
    .glitch_ignore_cnt = 7,
    .flags.enable_internal_pullup = true,
};
i2c_master_bus_handle_t bus_handle;
i2c_new_master_bus(&bus_config, &bus_handle);

bme280_init(bus_handle, BME280_ADDR);
```

```c
// bme280.c — each driver owns its own device handle
static i2c_master_dev_handle_t s_dev;

esp_err_t bme280_init(i2c_master_bus_handle_t bus, uint8_t addr)
{
    i2c_device_config_t dev_cfg = {
        .dev_addr_length = I2C_ADDR_BIT_LEN_7,
        .device_address = addr,
        .scl_speed_hz = 100000,
    };
    i2c_master_bus_add_device(bus, &dev_cfg, &s_dev);
    ...
}

static esp_err_t read_regs(uint8_t reg, uint8_t *buf, size_t len)
{
    // write(reg) + repeated start + read(len) in one call
    return i2c_master_transmit_receive(s_dev, &reg, 1, buf, len, 100);
}
```

```
# CMakeLists.txt
idf_component_register(SRCS "main.c" "ssd1306.c" "bme280.c" "bh1750.c" ...
                       REQUIRES driver esp_adc esp_driver_i2c)
```

Related: [[adc-oneshot-migration]], [[oled-ssd1306-i2c-swapped-pins]], [[i2c-pullup-resistors-loose-contacts]]
