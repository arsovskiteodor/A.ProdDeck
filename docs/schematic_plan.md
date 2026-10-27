# ProdDeck — Schematic Reference

Describes the schematic as built (`KiCAD/ProdDeck/ProdDeck.kicad_sch`). The schematic is the source of truth — if this document and the schematic disagree, the schematic wins. Full BOM with MPNs: `KiCAD/ProdDeck/fab/BOM_purchasing.csv`.

---

## Component List

**DNP** = Do Not Populate: not placed by the assembler, hand-soldered after delivery.

| Ref | Part | Value / Part No. | Notes |
|---|---|---|---|
| U4 | MCU | STM32WB55CGU6 | QFN-48, 1MB flash, BLE + USB FS |
| U1 | LDO 3.3V | MIC5365-3.3YD5 | TSOT-23-5, 150mA |
| U3 | LiPo charger | MCP73831T-2ACI/OT | SOT-23-5, 4.2V single cell |
| U2 | USB ESD protection | USBLC6-2SC6 | On D+/D- |
| USB1 | USB-C receptacle | GCT USB4105-GF-A | USB 2.0, power + wired HID |
| BATT_CONN1 | Battery connector | JST-PH S2B-PH-K (horizontal) | DNP |
| PWR1 | Power slide switch | C&K OS102011MS2QN1 | DNP — disconnects battery from the system |
| SWD1 | SWD/debug pads | Tag-Connect TC2030-IDC footprint | Pads only, no part placed |
| SW3–SW8 | Key switches KEY1–KEY6 | Gateron KS-33 (LP 2.0) | DNP |
| ENC1–ENC3 | Rotary encoders | Bourns PEC11R-4220F-S0024 | DNP — 24 detent, push switch |
| R12 | Slider pot | Bourns PTA6043-2015DPB103 | DNP — 60mm travel, 10kΩ linear |
| BOOT0 | Bootloader button | C&K RS-282G05A3 | Tactile |
| NRST1 | Reset button | C&K RS-282G05A3 | Tactile |
| X1 | LSE crystal | 32.768kHz, NDK STDMUB1 | Load caps C16/C17 10pF |
| X2 | HSE crystal | 32MHz, NDK NX2016SA | No external load caps (WB55 internal trim) |
| L1, L2 | SMPS inductors | 10nH + 10µH | WB55 internal SMPS |
| L3, C20, C21 | RF matching | 2.7nH, 0.8pF, 0.3pF | Between RF1 pin and filter |
| FLT1 | RF low-pass filter | TDK DLF162500LT-5028A1 | Custom footprint in `ProdDeck_PCBlib` |
| RF1 | Antenna connector | Hirose U.FL-R-SMT | External 2.4GHz antenna |
| D2, D3 | Schottky diodes | PMEG2010AEH | Battery / USB power OR |
| D1 | Power LED | 0402 red | R3 5.1kΩ |
| D4 | Charge LED | 0402 red | R7 1kΩ |
| R1, R2 | USB-C CC pull-downs | 5.1kΩ | Device (sink) mode |
| R4 | MCP73831 PROG | 5.1kΩ | Charge current ≈ 196mA |
| R8, R9 | Battery sense divider | 100kΩ / 100kΩ | Halves battery voltage for ADC |
| R10, R11 | BOOT0 pull-down / series | 5.1kΩ / 220Ω | |
| H1, H3–H5 | Mounting holes | M3, plated, to GND | |

Decoupling: 4.7µF (C1, C3, C5, C6, C10, C19), 1µF (C2), 100nF (C4, C7–C9, C11–C15, C18, C22).

---

## Power Architecture

```
USB-C VBUS (+5V) ──┬── C2 1µF
                   ├── U3 MCP73831 ── BATT+ (BATT_CONN1)
                   ├── D4 + R7 ── U3 STAT          (charge LED)
                   └── D3 ──┐
                            ├── U1 MIC5365 LDO ── +3.3V
BATT+ ── PWR1 switch ── D2 ─┘   (C1 4.7µF in)
              │
              └── R8 100k ── BATT_SENSE (PA0) ── R9 100k ── GND
```

- **USB plugged in:** the system runs from USB through D3, and the MCP73831 charges the battery. Battery charging works even with PWR1 off, because the charger sits before the switch.
- **USB unplugged:** the battery powers the LDO through PWR1 and D2.
- **Charge current:** R4 = 5.1kΩ → I = 1000 / 5.1 ≈ 196mA. Size the LiPo so this is ≤ 1C.
- **Battery sense:** the divider is after PWR1, so it draws nothing when the device is switched off. 4.2V full charge reads as 2.1V at PA0.
- **LEDs:** D1 (power) is on whenever +3.3V is up. D4 (charge) lights while the MCP73831 STAT pin is low (charging).
- **MCU core supply:** the WB55's internal SMPS is used (L1 + L2 on VLXSMPS / VFBSMPS).

---

## MCU Pin Assignment (U4 — STM32WB55CGU6)

| Pin | Port | Net | Connected to |
|---|---|---|---|
| 13 | PA4 | KEY1 | SW3 |
| 14 | PA5 | KEY2 | SW5 |
| 17 | PA8 | KEY3 | SW7 |
| 18 | PA9 | KEY4 | SW4 |
| 36 | PA10 | KEY5 | SW6 |
| 42 | PA15 | KEY6 | SW8 |
| 11 | PA2 | ENC1_A | ENC1 phase A |
| 12 | PA3 | ENC1_B | ENC1 phase B |
| 19 | PB2 | ENC1_SW | ENC1 push switch |
| 15 | PA6 | ENC2_A | ENC2 phase A |
| 16 | PA7 | ENC2_B | ENC2 phase B |
| 44 | PB4 | ENC2_SW | ENC2 push switch |
| 28 | PB0 | ENC3_A | ENC3 phase A |
| 29 | PB1 | ENC3_B | ENC3 phase B |
| 45 | PB5 | ENC3_SW | ENC3 push switch |
| 10 | PA1 | SLIDER_WIPER | R12 wiper (ADC) |
| 9 | PA0 | BATT_SENSE | Battery divider midpoint (ADC) |
| 37 | PA11 | USB_D- | USB-C via U2 |
| 38 | PA12 | USB_D+ | USB-C via U2 |
| 39 | PA13 | SWD_DIO | SWD1 |
| 41 | PA14 | SWD_CLK | SWD1 |
| 43 | PB3 | SWD_TRC | SWD1 (SWO trace) |
| 7 | NRST | SWD_NRST | NRST1 button, SWD1, C18 |
| 4 | PH3-BOOT0 | BOOT0 | BOOT0 button, R10 pull-down |
| 2, 3 | PC14, PC15 | LSE_IN / LSE_OUT | X1 |
| 25, 24 | OSC_IN / OSC_OUT | HSE_IN / HSE_OUT | X2 |
| 21 | RF1 | RF | Matching network → FLT1 → U.FL |
| 33, 31 | VLXSMPS / VFBSMPS | SMPSLX / SMPSFB | L1, L2 |

**Unused:** PB6, PB7, PB8, PB9, PE4, AT0, AT1. These are available for future additions (RGB LEDs, OLED, etc.).

---

## Key Switches (SW3–SW8)

```
KEYn (GPIO) ──[switch]── GND
```
There are no external pull-ups. Enable internal pull-ups in firmware (`GPIO_PULL_UP` in devicetree). The switches are on the 19.05mm MX grid. The reference designators don't follow the KEY numbering (e.g. KEY2 = SW5), so map by net name, not by ref.

---

## Rotary Encoders (ENC1–ENC3)

```
ENCn_A  (GPIO) ── A
ENCn_B  (GPIO) ── B
GND            ── C (common)
ENCn_SW (GPIO) ── S1
GND            ── S2
```
There are no external pull-ups or filter caps. Use internal pull-ups, and debounce in firmware.

---

## Slider (R12 — Bourns PTA6043)

```
+3.3V ── pin 1
PA1   ── pin 2 (wiper)
GND   ── pin 3
```
The wiper goes directly to the ADC input, with no filter cap. Smooth the reading in firmware if it's noisy.

---

## USB-C

```
VBUS ── +5V
D+/D- ── U2 USBLC6 ── PA12 / PA11
CC1 ──[5.1kΩ R2]── GND
CC2 ──[5.1kΩ R1]── GND
SBU1/SBU2 ── not connected
Shield ── GND
```

---

## Programming and Debug

**SWD1 (Tag-Connect TC2030 footprint)** — 6 pads, no connector fitted. A TC2030-IDC cable is needed to connect an ST-Link / J-Link.

| Pad | Signal |
|---|---|
| 1 | +3.3V |
| 2 | SWD_DIO (PA13) |
| 3 | SWD_NRST |
| 4 | SWD_CLK (PA14) |
| 5 | GND |
| 6 | SWD_TRC (PB3, SWO) |

**BOOT0 button:** pulls BOOT0 high (through R11 220Ω). R10 5.1kΩ holds it low otherwise. To enter the ST system bootloader (USB DFU), hold BOOT0 and press NRST.

**NRST1 button:** resets the MCU. C18 100nF filters the line.

---

## RF

```
RF1 (pin 21) ── C20 0.8pF shunt ── L3 2.7nH ── C21 0.3pF shunt ── FLT1 low-pass ── U.FL (RF1) ── external antenna
```
There is no on-board antenna. An external 2.4GHz antenna with a U.FL lead is needed and is **not on the BOM**. The matching values follow ST's reference design (AN5165).

---

## Net Naming Conventions

| Type | Format | Example |
|---|---|---|
| Power | As KiCad power symbols | `+3.3V`, `+5V`, `GND` |
| Named signal | `BLOCK_SIGNAL` | `ENC1_A`, `SWD_CLK`, `BATT_SENSE` |
| Keys | `KEYn` | `KEY1` |
| USB | `USB_D+`, `USB_D-` | — |
