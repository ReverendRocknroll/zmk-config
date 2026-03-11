# Custom Split — ZMK Firmware Config

Wireless split keyboard: 62 keys (31/side), nice!nano v2 clones, Amoeba King PCBs, SSD1306 OLED displays.

## Specs

- **Layout:** 5×7 matrix per side, 3-key inner thumb cluster (cols 5-7)
- **Controller:** nice!nano v2 clone (NRF52840) × 2
- **Firmware:** ZMK (Bluetooth, fully wireless split)
- **Display:** SSD1306 I2C OLED on D20/D21 (both halves)
- **Diodes:** COL2ROW

## Layers

### Layer 0 — QWERTY (default)
```
┌─────┬─────┬─────┬─────┬─────┬─────┬─────┐       ┌─────┬─────┬─────┬─────┬─────┬─────┬─────┐
│ ESC │  1  │  2  │  3  │  4  │  5  │  6  │       │  7  │  8  │  9  │  0  │  -  │  =  │BKSPC│
├─────┼─────┼─────┼─────┼─────┼─────┼─────┤       ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤
│ TAB │  Q  │  W  │  E  │  R  │  T  │  [  │       │  ]  │  Y  │  U  │  I  │  O  │  P  │  \  │
├─────┼─────┼─────┼─────┼─────┼─────┼─────┤       ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤
│CAPS │  A  │  S  │  D  │  F  │  G  │ DEL │       │ENTER│  H  │  J  │  K  │  L  │  ;  │  '  │
├─────┼─────┼─────┼─────┼─────┼─────┼─────┤       ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤
│SHIFT│  Z  │  X  │  C  │  V  │  B  │  `  │       │  /  │  N  │  M  │  ,  │  .  │  /  │SHIFT│
└─────┴─────┴─────┴─────┼─────┼─────┼─────┤       ├─────┼─────┼─────┼─────┴─────┴─────┴─────┘
                        │CTRL │SP/L1│ ALT │       │ ALT │SP/L1│CTRL │
                        └─────┴─────┴─────┘       └─────┴─────┴─────┘
```
**SP/L1** = Tap for Space, Hold for Layer 1

### Layer 1 — Navigation / F-Keys / Bluetooth
```
┌─────┬─────┬─────┬─────┬─────┬─────┬─────┐       ┌─────┬─────┬─────┬─────┬─────┬─────┬─────┐
│     │ F1  │ F2  │ F3  │ F4  │ F5  │ F6  │       │ F7  │ F8  │ F9  │ F10 │ F11 │ F12 │     │
├─────┼─────┼─────┼─────┼─────┼─────┼─────┤       ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤
│     │     │     │     │     │     │     │       │     │HOME │PG_DN│PG_UP│ END │     │     │
├─────┼─────┼─────┼─────┼─────┼─────┼─────┤       ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤
│     │BT 0 │BT 1 │BT 2 │BT 3 │BT 4 │BTCLR│       │     │ ←   │  ↓  │  ↑  │  →  │     │     │
├─────┼─────┼─────┼─────┼─────┼─────┼─────┤       ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤
│     │     │     │     │     │     │BOOT │       │BOOT │     │     │     │     │     │     │
└─────┴─────┴─────┴─────┼─────┼─────┼─────┤       ├─────┼─────┼─────┼─────┴─────┴─────┴─────┘
                        │ GUI │     │     │       │     │     │ GUI │
                        └─────┴─────┴─────┘       └─────┴─────┴─────┘
```
- **BT 0–4:** Select Bluetooth profile
- **BTCLR:** Clear current BT profile bond
- **BOOT:** Enter bootloader (for reflashing)
- **GUI:** Windows/Super key (replaces Ctrl on Layer 1)

## File Structure
```
zmk-config/
├── .github/workflows/build.yml          ← GitHub Actions build
├── build.yaml                           ← What to build (left, right, reset)
└── config/
    ├── west.yml                         ← ZMK module config
    └── boards/shields/custom_split/
        ├── custom_split.dtsi            ← Shared hardware (kscan, matrix)
        ├── custom_split_left.overlay    ← Left half (central + OLED)
        ├── custom_split_right.overlay   ← Right half (peripheral + OLED)
        ├── custom_split.keymap          ← Keymap (both layers)
        ├── custom_split.conf            ← Settings (display, BT, sleep)
        ├── custom_split.zmk.yml         ← Metadata
        ├── Kconfig.shield               ← Shield detection
        └── Kconfig.defconfig            ← Shield defaults
```

## How to Build

### Option 1: GitHub Actions (recommended)
1. Create a new GitHub repository
2. Push all these files to it
3. Go to Actions tab → the build runs automatically
4. Download the `firmware` artifact from the completed build
5. Unzip to get `.uf2` files for each half

### Option 2: Local build
```bash
west init -l config
west update
west build -s zmk/app -b nice_nano_v2 -- -DSHIELD=custom_split_left
west build -s zmk/app -b nice_nano_v2 -- -DSHIELD=custom_split_right
```

## Flashing
1. Double-tap RST to GND within 0.5s on the nice!nano clone
2. Controller appears as a USB drive
3. Drag `custom_split_left-nice_nano_v2-zmk.uf2` onto the LEFT half
4. Drag `custom_split_right-nice_nano_v2-zmk.uf2` onto the RIGHT half
5. Flash `settings_reset-nice_nano_v2-zmk.uf2` to either half if BT pairing issues arise

## Pin Assignments (per half, wired identically)

| Function | Pro Micro # | nRF GPIO |
|---|---|---|
| Row 1 | D2 | P0.17 |
| Row 2 | D3 | P0.20 |
| Row 3 | D4 | P0.22 |
| Row 4 | D5 | P0.24 |
| Row 5 (thumb) | D6 | P1.00 |
| Col 1 (outer) | D7 | P0.11 |
| Col 2 | D8 | P1.04 |
| Col 3 | D9 | P1.06 |
| Col 4 | D10 | P0.09 |
| Col 5 | D16 | P0.10 |
| Col 6 | D14 | P1.11 |
| Col 7 (inner) | D15 | P1.13 |
| OLED SDA | D20 | P0.29 |
| OLED SCL | D21 | P0.31 |

## Troubleshooting

- **Can't enter bootloader:** Double-tap RST→GND quickly (within 0.5s)
- **Halves won't pair:** Flash `settings_reset` to both halves, then reflash firmware
- **OLED not working:** Check SDA/SCL wiring to D20/D21, verify I2C address is 0x3C
- **Key not registering:** Check diode direction (COL2ROW), check solder joints
- **BT connection issues:** Use BT_CLR (Layer 1 + G key) to clear profile, re-pair
