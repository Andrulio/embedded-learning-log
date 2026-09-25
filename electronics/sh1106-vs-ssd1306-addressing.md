---
date: 2026-09-25
tags: [electronics, i2c, oled, sh1106, ssd1306, esp-idf]
---

# SH1106 vs SSD1306 — horizontal addressing commands aren't universal

A cheap "1.3\" SSD1306" OLED module displayed some text correctly (whatever was written last in the framebuffer) but silently failed to update the rest of the screen — no I2C errors anywhere, full continuity confirmed, yet most of the display stayed frozen or garbled.

## What happened / Why this matters

After ruling out every hardware cause (wiring continuity end-to-end all 0Ω, stable pull-up resistors, no NACKs logged), the SSD1306 driver still only updated a small region of the display correctly, no matter what. Every I2C write reported ESP_OK — no errors to chase.

The real cause: the display commands `0x21` (set column address range) and `0x22` (set page address range) enable **horizontal auto-addressing mode**, which only the real SSD1306 controller supports. Many cheap "SSD1306-compatible" 1.3" modules are actually built on the **SH1106** controller, which doesn't support those commands — critically, it just **silently ignores them instead of NACKing**, so there's no error anywhere in the I2C transaction to reveal the problem. Because the address range was never actually set, every subsequent data write landed in whatever page the pointer was last left at, instead of sweeping across the whole screen.

The fix: don't rely on `0x21`/`0x22` at all. Instead, set the write address **per page**, using `0xB0 | page_number` (set page start address) followed by two commands for the low/high nibble of the column start address (`0x00`/`0x10`). This addressing mode is supported by both SSD1306 and SH1106, so it works universally regardless of which chip the module actually has.

## Key takeaway

A cheap breakout board's silkscreen/listing name (e.g. "SSD1306") isn't a reliable guarantee of the actual chip inside — SH1106 clones are extremely common and mostly command-compatible except for auto-addressing mode. When an I2C write reports success but the display doesn't update as expected, and there's no error anywhere, suspect a *silently ignored command* rather than a communication fault — the device ACKed the byte but didn't act on it. Using per-page addressing (`0xB0`+nibbles) instead of the range-set commands makes a driver compatible with both controllers.

## Code

```c
for (int page = 0; page < 8; page++) {
    send_cmd(0xB0 | page);        // set page start address
    send_cmd(0x00 | (offset & 0x0F));        // column low nibble
    send_cmd(0x10 | ((offset >> 4) & 0x0F)); // column high nibble
    // ... write 128 bytes of framebuffer data for this page
}
```

Related: [[oled-ssd1306-i2c-swapped-pins]], [[breadboard-debugging]]
