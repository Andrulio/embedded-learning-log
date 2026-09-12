---
date: 2026-09-11
tags: [electronics, breadboard, basics, beginner]
---

# How a breadboard works

Breadboard lets you prototype circuits without soldering.

## Key concept

Inside the breadboard, **holes in the same row are connected horizontally** (5 holes = 1 conductor). The center groove splits the left and right halves.

```
Row 1:  [a][b][c][d][e]  |  [f][g][h][i][j]
        ←── connected ──→    ←── connected ──→
```

Everything you plug into the same row is automatically connected.

## Power rails

The long strips along the edges marked **+** (red) and **-** (blue) run the full length. Use them for power (3.3V/5V) and GND — connect once, use everywhere.

## Tips

- ESP32 DevKit is wide — it covers almost all holes on both sides. Use Dupont wires to connect to pins rather than plugging components next to it.
- The sticky film on the bottom is adhesive tape for mounting — don't peel it off unless you want to stick the board to a surface.
- New breadboards are tight — pins go in with a click. Gets easier over time.

Related: [[led-polarity]], [[resistor-current-limiting]], [[gpio-blink-led]]
