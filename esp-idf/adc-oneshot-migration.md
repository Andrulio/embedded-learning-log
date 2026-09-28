---
date: 2026-09-28
tags: [esp-idf, adc, migration, mq135]
---

# Legacy ADC driver removed — use adc_oneshot

`driver/adc.h` (the old `adc1_config_width` / `adc1_config_channel_atten` / `adc1_get_raw` API) no longer exists in ESP-IDF v6.1. Including it fails with a plain "No such file or directory" — no deprecation warning to hint why.

## What happened / Why this matters

Wrote the MQ-135 (analog air quality sensor) driver using the classic ADC1 API from an older tutorial. Build failed on a Windows machine running ESP-IDF v6.1. Ninja's error eventually surfaced the real hint:

```
HINT: The legacy ADC driver is removed. It should be replaced by
'esp_adc/adc_oneshot.h, esp_adc/adc_continuous.h, esp_adc/adc_cali.h,
esp_adc/adc_cali_scheme.h' in the 'esp_adc' component.
```

Adding `REQUIRES driver esp_adc` to `CMakeLists.txt` alone wasn't enough — the header itself is gone, not just missing from the component's public includes. Had to actually rewrite the driver against the new API.

## Key takeaway

In recent ESP-IDF (v5.3+ but especially by v6.1), the old single-call `adc1_*` API is gone. Use `esp_adc/adc_oneshot.h` instead: create a unit handle once (`adc_oneshot_new_unit`), configure a channel on it (`adc_oneshot_config_channel`), then read with `adc_oneshot_read`. The component to require is `esp_adc`.

## Code

```c
#include "esp_adc/adc_oneshot.h"

static adc_oneshot_unit_handle_t s_adc_handle = NULL;

esp_err_t mq135_init(void)
{
    adc_oneshot_unit_init_cfg_t init_config = { .unit_id = ADC_UNIT_1 };
    esp_err_t ret = adc_oneshot_new_unit(&init_config, &s_adc_handle);
    if (ret != ESP_OK) return ret;

    adc_oneshot_chan_cfg_t chan_config = {
        .atten = ADC_ATTEN_DB_12,       // new name for the old DB_11 (~3.3V range)
        .bitwidth = ADC_BITWIDTH_DEFAULT,
    };
    return adc_oneshot_config_channel(s_adc_handle, ADC_CHANNEL_6, &chan_config);
}

void mq135_read(int *raw)
{
    int value;
    adc_oneshot_read(s_adc_handle, ADC_CHANNEL_6, &value);
    if (raw) *raw = value;
}
```

```
# CMakeLists.txt
idf_component_register(SRCS "mq135.c" ...
                       REQUIRES driver esp_adc)
```

Related: [[i2c-master-driver-migration]]
