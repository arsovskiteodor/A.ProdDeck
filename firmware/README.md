# ProdDeck Firmware

ZMK firmware for the ProdDeck. Handles BLE HID, wired USB HID, key scanning, encoder reading, and slider ADC.

## MCU

**Bare STM32WB55** (STM32WB55CEU6 or STM32WB55CGU6) — decided 2026-07-29, superseding the earlier E73-2G4M08S1C (nRF52840) module.

- 48-pin UFQFPN package. CEU6 (512KB flash) and CGU6 (1MB flash) are pin-compatible/drop-in — same footprint either way, flash size is a late BOM decision. Leaning CGU6 for headroom (wireless-coprocessor + FUS overhead eats a larger fraction of the 512KB part).
- **Dual-core:** Cortex-M4 runs the application (ZMK/Zephyr); a separate Cortex-M0+ runs ST's closed-source BLE wireless-coprocessor stack, linked via IPC/HCI. This is a real architectural difference from nRF52840 (single-core, no separate stack binary) — version mismatches between the M0+ stack and Zephyr's HCI driver are a known source of BLE bring-up issues.
- BLE 5.x — wireless HID
- Native USB 2.0 FS — wired mode when plugged in
- Bare chip, not a module: HSE + LSE crystals, RF matching network, and antenna are now on-PCB (previously internal to the E73 module). Reference design: Phil's Lab's "KiCad 7 STM32 Bluetooth Hardware Design" (youtube.com/watch?v=nkHFoxe0mrU), citing ST app notes AN5165 and AN2867.
- PCB assembly outsourced to the manufacturer (JLCPCB/PCBWay) — the fine-pitch QFN package is not a hand-soldering constraint here.

## Firmware Framework

**ZMK** — decided 2026-07-28.

ZMK is purpose-built for wireless keyboards and macro pads. Configuration is done in devicetree `.overlay` files and `Kconfig` — no C code needed for standard builds.

Key ZMK features used by ProdDeck:
- `kscan` — key matrix / direct GPIO scanning (6 switches + 3 encoder push buttons)
- `encoder` — rotary encoder quadrature decoding (3× PEC11R)
- `zmk,battery` — LiPo battery level reporting over BLE
- ADC (STM32 ADC peripheral, not Nordic SAADC) — analog slider position (Alps RS60N111100B)
- `bt` — BLE HID profiles, up to 5 paired devices

No off-the-shelf ZMK board definition exists for this exact custom PCB — board bring-up (devicetree overlay, pinctrl, Kconfig) is from-scratch work. Reference config: github.com/Hakkei-Co/zmk-config-wb55. Reference Zephyr board: `stm32wb55_core` / `nucleo_wb55rg`.

## Mode Switching

- **USB plugged in** → wired USB HID, charges battery
- **USB unplugged** → BLE HID, runs on LiPo
- Mode switches automatically, no user action needed

## Programming

**Tooling needed:** SWD probe (ST-Link clone or J-Link) + STM32CubeProgrammer, in addition to a USB-C cable. STM32CubeIDE is not part of this workflow — ZMK builds through Zephyr's `west`, not CubeIDE.

- **Wireless-coprocessor stack** (the M0+ BLE binary) is flashed via STM32CubeProgrammer over SWD — this is ST's documented path and is separate from flashing the application.
- **ZMK application** (M4 core) flashes via `west flash` over SWD (openocd/pyocd/J-Link runner).
- A USB DFU bootloader path exists on STM32 (BOOT0 pin + reset) and can flash the application over USB alone, but SWD is still needed for the wireless stack and for any real debugging of the CPU1/CPU2 handshake — treat SWD as required, not optional, especially during bring-up.

## Development

```
firmware/
└── src/    ← ZMK config files (.overlay, Kconfig, keymap) go here
```

ZMK is built as a Zephyr RTOS application. Setup:
1. Install Zephyr SDK and `west` build tool
2. Clone ZMK: `west init -m https://github.com/zmkfirmware/zmk`
3. Add ProdDeck board definition (from scratch, referencing `stm32wb55_core`/`nucleo_wb55rg`) and keymap under `src/`
4. Build: `west build -b proddeck`
5. Flash the wireless-coprocessor stack via STM32CubeProgrammer (SWD), then flash the application via `west flash` (SWD)
