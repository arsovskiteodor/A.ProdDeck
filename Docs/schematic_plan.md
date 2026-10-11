# ProdDeck — Schematic Plan

Reference document for placing and wiring components in KiCAD. Work through each section in order.

---

## Component List (full BOM draft)

| Ref | Part | Value / Part No. | Notes |
|---|---|---|---|
| U1 | nRF52840 module | E73-2G4M08S1C | Castellated module — nRF52840 + antenna + crystals + RF passives + DC-DC inductor all internal. Hand-solder to PCB. |
| U2 | LDO 3.3V | AP2112K-3.3TRG1 | SOT-23-5, 600mA, low dropout |
| U3 | LiPo charging IC | MCP73831T-2ACI/OT | SOT-23-5, 4.2V single-cell, 500mA max |
| ~~Y1~~ | ~~32MHz crystal~~ | — | **Removed — internal to E73 module** |
| ~~Y2~~ | ~~32.768kHz crystal~~ | — | **Removed — internal to E73 module** |
| ~~ANT1~~ | ~~2.4GHz antenna~~ | — | **Removed — internal to E73 module** |
| J1 | USB-C connector | USB4105-GF-A or GCT USB4135 | Charging + wired HID + DFU programming |
| J2 | Battery connector | JST-PH 2-pin | LiPo cell connection |
| J3 | SWD debug header | 1×4 2.54mm pin header | SWDCLK, SWDIO, GND, 3V3 |
| SW1–SW6 | Key switch | Gateron KS-33 (LP 2.0) | Solderable. Footprint: `Gateron_KS33_Solderable:Gateron-KS33-Solderable-1U` |
| ENC1–ENC3 | Rotary encoder | Bourns PEC11R-4220F-**S0024** | 24 detent, push button. Footprint: `PEC11R_Encoder:Bourns-PEC11R-4220F-S0024-0-0-0` |
| SL1 | Fader slider | Alps RS60N111100B | 60mm travel, custom footprint |
| SW7 | DFU button | 3×6mm SMD tactile | Enter ZMK USB DFU bootloader |
| SW8 | RESET button | 3×6mm SMD tactile | Pulls nRF52840 RESET pin low |
| LED1 | Charge status LED | Red, 0402 | Driven by MCP73831 STAT pin |
| BAT1 | LiPo cell | 400–600mAh, 3.7V nominal | Size TBD based on enclosure depth |
| ~~L1~~ | ~~DC-DC inductor~~ | — | **Removed — internal to E73 module** |
| F1 | Polyfuse | 500mA resettable | USB VBUS protection |
| D1, D2 | Schottky diodes | BAT54 or similar | Power path OR — USB 5V and LiPo to LDO input |
| C1, C2 | LDO input decoupling | 100nF + 10µF | On U2 VIN |
| C3, C4 | LDO output decoupling | 100nF + 10µF | On U2 VOUT |
| C5–C10 | Module VDD decoupling | 100nF × 6 | On E73 module VDD/VDDIO pins — check E73 datasheet for exact count |
| ~~C11, C12~~ | ~~nRF52840 DCDC caps~~ | — | **Removed — DCDC internal to E73 module** |
| ~~C13, C14~~ | ~~Crystal load caps~~ | — | **Removed — crystals internal to E73 module** |
| C15 | USB bulk cap | 10µF | On 5V rail after polyfuse |
| C16 | Charge IC decoupling | 4.7µF | On MCP73831 VDD |
| R1, R2 | USB-C CC resistors | 5.1kΩ | CC1 and CC2 to GND (device mode) |
| R3–R8 | Switch pull-ups | 10kΩ × 6 | SW1–SW6 (or use nRF52840 internal pull-ups) |
| R9–R17 | Encoder pull-ups | 10kΩ × 9 | A, B, SW pins × 3 encoders |
| R18 | Charge LED resistor | 1kΩ | In series with LED1 |
| R19 | Battery sense divider high | 100kΩ | BATT+ to P0.29 |
| R20 | Battery sense divider low | 100kΩ | P0.29 to GND |
| R21 | MCP73831 PROG resistor | 2kΩ | Sets charge current: 500mA |

> **No external flash needed.** The nRF52840 has 1MB internal flash — ZMK firmware + keymap fits comfortably. The W25Q16 from the original RP2040 plan is removed.

---

## Power Architecture

```
USB VBUS (5V)
    │
   [F1] 500mA polyfuse
    │
    ├──[C15 10µF]── GND
    │
    ├──[D1 Schottky]───────────────────┐
    │                                  │
    └──[U3 MCP73831]── [J2] LiPo ──[D2 Schottky]──┤
         (charging)      3.7V                       │
                                              LDO input (~4.4V USB / ~3.4V batt)
                                                    │
                                             [U2] AP2112K LDO
                                                    │
                                                  +3V3 rail
                                                    │
                                  ┌─────────────────┼──────────────────┐
                               U1 VDD           peripherals        pull-ups
                            + decoupling     (encoders, slider,
                                              switches)
```

**Mode behaviour:**
- USB connected: D1 wins (5V - 0.3V = 4.7V > LiPo 4.2V - 0.3V = 3.9V). LiPo charges via MCP73831. System runs from USB.
- USB disconnected: LiPo through D2 powers LDO. BLE HID active.
- Both modes produce stable +3V3 from LDO.

**nRF52840 internal DCDC:**
Enable the internal DC-DC converter (buck regulator) via L1 (4.7µH inductor on DCC pin). This reduces current draw from the LiPo by ~30%, meaningfully extending battery life.

---

## E73 Module Pin Assignment

| Pin | Net | Connected to |
|---|---|---|
| P0.02 | SW1 | Key switch 1 |
| P0.03 | SW2 | Key switch 2 |
| P0.04 | SW3 | Key switch 3 |
| P0.05 | SW4 | Key switch 4 |
| P0.06 | SW5 | Key switch 5 |
| P0.07 | SW6 | Key switch 6 |
| P0.08 | ENC1_A | Encoder 1 phase A |
| P0.09 | ENC1_B | Encoder 1 phase B |
| P0.10 | ENC1_SW | Encoder 1 push button |
| P0.11 | ENC2_A | Encoder 2 phase A |
| P0.12 | ENC2_B | Encoder 2 phase B |
| P0.13 | ENC2_SW | Encoder 2 push button |
| P0.14 | ENC3_A | Encoder 3 phase A |
| P0.15 | ENC3_B | Encoder 3 phase B |
| P0.16 | ENC3_SW | Encoder 3 push button |
| P0.28 (AIN4) | SLIDER | Alps slider wiper (SAADC input) |
| P0.29 (AIN5) | BATT_SENSE | Battery voltage divider midpoint (optional) |
| P0.17 | CHG_STAT | MCP73831 STAT pin → LED1 |
| P0.20 | DFU_BTN | DFU button SW7 to GND |
| P0.00 | XL1 | — internal to E73 module — |
| P0.01 | XL2 | — internal to E73 module — |
| P0.18 | RESET_N | Reset button SW8 to GND (dedicated reset pin) |
| USB_DP | USB_DP | USB-C D+ |
| USB_DM | USB_DM | USB-C D- |
| SWDCLK | SWDCLK | SWD debug header J3 |
| SWDIO | SWDIO | SWD debug header J3 |
| P1.00–P1.15 | — | Spare GPIO (future: RGB, OLED, BLE profile buttons) |

---

## Key Switches (SW1–SW6)

```
+3V3 ──[10kΩ]── P0.0x ──[switch]── GND
```
- ZMK configures internal pull-ups — R3–R8 can be omitted if using `gpio-pull-up` in devicetree
- Grid: 19.05×19.05mm spacing, 3-pin solderable Gateron footprint

---

## Rotary Encoders (ENC1–ENC3)

Each PEC11R has 5 pins: A, B, GND (encoder) + C, D (switch):
```
+3V3 ──[10kΩ]── P0.xx_A
+3V3 ──[10kΩ]── P0.xx_B
+3V3 ──[10kΩ]── P0.xx_SW
ENCn GND pins ── GND
```
- Pull-ups can be replaced by ZMK `gpio-pull-up` in devicetree
- Add 100nF filter caps on A and B lines for debounce

---

## Slider (SL1 — Alps RS60N111100B)

```
+3V3 ──── SL1 pin 1 (top of track)
GND  ──── SL1 pin 3 (bottom of track)
P0.28 ─── SL1 pin 2 (wiper → SAADC AIN4)
```
- Add 100nF cap from wiper to GND for noise filtering
- ZMK reads slider via SAADC (Successive Approximation ADC) — configure in devicetree

---

## Power Path — USB-C + Charging

```
J1 VBUS ──[F1]──┬──[D1]── LDO input
                │
               [U3] MCP73831
                │
               [J2] LiPo ──[D2]── LDO input

J1 D+   ── USB_DP (nRF52840)
J1 D-   ── USB_DM (nRF52840)
J1 CC1  ──[5.1kΩ R1]── GND
J1 CC2  ──[5.1kΩ R2]── GND
J1 GND/shield ── GND
```

**MCP73831 connections:**
```
VDD  ← USB 5V (after polyfuse)
VSS  → GND
VBAT → LiPo+ (via J2)
PROG ──[2kΩ R21]── GND   (sets 500mA charge current: I = 1000/R kΩ)
STAT ──[1kΩ R18]── LED1 ── GND  (LOW = charging, Hi-Z = done/fault)
```

---

## DFU Button (SW7)

```
P0.20 ──[SW7]── GND
```
ZMK enters USB DFU bootloader when SW7 is held during power-on or via ZMK's `&bootloader` keymap action. No resistor needed — use internal pull-up in devicetree.

---

## RESET Button (SW8)

```
RESET_N (P0.18) ──[SW8]── GND
```
nRF52840 has an internal pull-up on RESET_N. Momentary press resets the chip.

---

## SWD Debug Header (J3)

4-pin 2.54mm header:
```
Pin 1: SWDCLK
Pin 2: SWDIO
Pin 3: GND
Pin 4: +3V3
```
Use a J-Link, Black Magic Probe, or another nRF52840 running the Blackmagic firmware.

---

## Antenna Keep-Out Zone

**Not applicable — antenna is internal to the E73-2G4M08S1C module.**

The module itself requires a keep-out zone around the antenna end (the end opposite the castellated pads). Refer to the E73-2G4M08S1C datasheet mechanical drawing — typically no copper pours under or beside the module's antenna section. Position the module at a board edge for best RF performance.

---

## Schematic Sheet Organisation (suggested)

1. **Power** — USB-C, polyfuse, diode OR, MCP73831 charging, LDO, battery connector, charge LED
2. **nRF52840 core** — MCU symbol, crystals, DCDC inductor, decoupling, SWD header, antenna, DFU/RESET buttons
3. **Input grid** — 6 switches
4. **Encoders** — 3× PEC11R
5. **Slider** — Alps + ADC connection

---

## Net Naming Conventions

| Type | Format | Example |
|---|---|---|
| Power | Uppercase | `+3V3`, `+5V`, `VBAT`, `GND` |
| GPIO | `P{port}.{pin}` | `P0.08`, `P0.28` |
| Named signal | `BLOCK_SIGNAL` | `ENC1_A`, `CHG_STAT` |
| USB | `USB_DP`, `USB_DM` | — |
