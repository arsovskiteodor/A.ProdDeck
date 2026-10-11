# Decision Log

Append entries newest-first. Format: `## [DATE] — Title`, then a one-liner on what was decided and why.

---

## 2026-08-02 — Slider changed: Alps RS60N111100B → Bourns PTA6043-2015DPB103

Switched slide potentiometer from the Alps industrial part to the Bourns PTA6043-2015DPB103 — cheaper and far more available (Digikey/Mouser stocked) than the Alps part, which required pulling a 1994 mechanical drawing and hand-building a custom footprint from scratch.

- **Symbol + footprint + 3D model** added directly under `KiCAD/ProdDeck/libs/Bourns_PTA6043/` (own `sym-lib-table`/`fp-lib-table` entries, both named `slider`). The imported symbol is a multi-unit part (2 units, both exposing pins 1/2/3) — likely an artifact of however it was sourced/converted; only unit 1 is used and KiCad resolves to it by default without issue.
- **3D model linked** — the footprint (`slider:POT_PTA6043`) didn't have its `.stp` model attached when added; added the `(model ...)` block pointing at `${KIPRJMOD}/libs/Bourns_PTA6043/3dmodel/PTA6043-2015DPB103.stp`.
- **Placed as R12**, not SL1 — the imported symbol's default reference-designator prefix is "R" (never customized by whoever generated it), so KiCad auto-numbered it into the resistor sequence. Cosmetic only, not worth fixing by hand.
- **Rewired directly to the new part** (done by the user, not scripted): pin 1 → `+3.3V`, pin 2 (wiper) → `SLIDER_WIPER`, pin 3 → `GND` — same net assignments as the original Alps placement, verified via netlist trace.
- **The hand-built Alps footprint (`libs/ProdDeck_Custom.pretty/Alps_RS60N111100B.kicad_mod`) is now unused** but left in place for reference — not deleted, in case of a future part reversal. No longer referenced by anything in the schematic.
- **Panel cutout dimensions from the old Alps datasheet no longer apply** — if enclosure/panel design work happens later, the PTA6043's mechanical drawing needs to be pulled fresh (travel length, actuator overhang, mounting hole pattern likely all differ from the Alps part).

---

## 2026-07-28 — Slider: Alps RS60N111100B (60mm travel)

Overall length 88mm, body 16×8mm, mounting hole spacing 80mm. 45mm ruled out (too short), 100mm ruled out (enclosure too long). Pull exact mechanical drawing from tech.alpsalpine.com before cutting panel slot.

---

## 2026-07-28 — MCU: RP2040

30 GPIO + 3 ADC, native USB HID, UF2 drag-and-drop bootloader via USB-C (no tools needed). PIO handles encoder quadrature cleanly. Requires: RP2040 die + 2MB QSPI flash + USB-C connector + 5.1kΩ CC resistors. STM32F072 and ATmega32U4 ruled out.

---

## 2026-07-29 — MCU changed: E73-2G4M08S1C module → bare STM32WB55 (CEU6/CGU6)

Switched from the nRF52840 castellated module to a bare STM32WB55 chip, 48-pin UFQFPN package. Driver: user wants to learn full custom RF/PCB design (antenna matching, crystal selection, RF layout) rather than use a pre-built module, and is now outsourcing PCB assembly to the manufacturer (JLCPCB/PCBWay) instead of hand-soldering — which removes the hand-solderability constraint that originally motivated the E73 module.

- **Part:** STM32WB55CEU6 (512KB flash) or STM32WB55CGU6 (1MB flash) — pin-compatible/drop-in, same 48-pin UFQFPN package; flash size is a late BOM decision, not a layout decision. Leaning CGU6 (1MB) for headroom, since the wireless-coprocessor stack + FUS overhead (~80–170KB+) eats a larger fraction of the 512KB part, and this is a professionally-assembled bare QFN — not easily reworked if flash runs out mid-project.
- **Design reference:** Phil's Lab's "KiCad 7 STM32 Bluetooth Hardware Design" tutorial (schematic: youtube.com/watch?v=nkHFoxe0mrU) — covers MCU power/decoupling, SMPS, HSE/LSE crystal design, RF section/antenna matching, USB circuit, and SWD/BOOT0 programming header, citing ST app notes AN5165 and AN2867. Addresses the hardware-design risk (antenna/RF layout) that the module previously handled internally.
- **New PCB requirements vs. the module:** HSE + LSE crystals, RF matching network + antenna, are now needed on-PCB (previously internal to the E73 module). SWD header required (not just USB) — needed both for flashing the wireless-coprocessor binary via STM32CubeProgrammer and for debugging CPU1/CPU2 bring-up.
- **Firmware stays ZMK.** STM32CubeIDE/CubeMX are not part of the ZMK build (that goes through Zephyr's `west`) — CubeIDE would only be relevant if custom bare-metal C firmware were chosen instead, which was considered and rejected in favor of keeping ZMK.
- **Firmware risk, carried over from the module decision:** STM32WB55 is dual-core (Cortex-M4 app + Cortex-M0+ wireless coprocessor running ST's closed-source BLE stack, linked via IPC/HCI). This is a genuine architectural difference from nRF52840 (single-core, no separate stack binary) — version mismatches between the M0+ stack binary and Zephyr's HCI driver are a known friction point. Reference ZMK config: github.com/Hakkei-Co/zmk-config-wb55. Reference Zephyr board: `stm32wb55_core` / `nucleo_wb55rg`.
- **Tooling needed:** SWD probe (ST-Link clone) + STM32CubeProgrammer, in addition to a USB-C cable.
- **Power path unchanged:** LiPo + MCP73831 charging IC + diode-OR path still applies — battery/charging architecture is independent of MCU choice.
- **Removed:** `libs/nrfmicro` (E73-2G4M08S1C footprint, no longer needed) and its `fp-lib-table` entry. No STM32WB55 KiCad footprint/symbol sourced yet — will be hand-built in `libs/ProdDeck_Custom.pretty/` following Phil's Lab's schematic symbol + footprint creation steps, or pulled from SnapEDA if a suitable one is found.

---

## 2026-07-29 — E73-2G4M08S1C KiCad footprint sourced: joric/nrfmicro

Pulled `E73-2G4M08S1C-52840.kicad_mod` from github.com/joric/nrfmicro (`hardware/nrfmicro.pretty/`) — cloned into `libs/nrfmicro`, registered as `nrfmicro` in `fp-lib-table`. 43 pads, matches Ebyte's pin count exactly. Chosen over a from-scratch footprint since nrfmicro is a real, shipped ZMK-based BLE controller using this exact module. Includes a 3D model (.step/.FCStd) not yet linked in the footprint. No schematic symbol in this repo — still need to source one (SnapEDA) or hand-draw before schematic placement.

---

## 2026-07-29 — MCU packaging: bare nRF52840-QIAA → E73-2G4M08S1C module

E73-2G4M08S1C castellated module chosen over bare QFN73 chip. Module includes antenna, crystals, RF passives, and DC-DC inductor internally — eliminating those components from the PCB BOM. Hand-solderable (castellated edges). GPIO count (48 exposed) meets ProdDeck's ~19-pin requirement. XIAO nRF52840 and nice!nano ruled out — insufficient exposed GPIO (14 and 18 pins respectively) for direct-GPIO design without a switch matrix.

---

## 2026-07-28 — MCU changed: RP2040 → nRF52840 (wireless)

nRF52840-QIAA (QFN73). BLE 5.0 + native USB HID. Dual-mode: wired USB HID when plugged in, BLE HID on battery. 1MB internal flash (no external flash needed). Firmware: ZMK. Power: MCP73831 charging IC + LiPo + diode-OR power path.

---

## 2026-07-28 — Firmware framework: ZMK

Purpose-built for wireless keyboards/macropads. Configured via devicetree YAML overlays. Supports BLE HID, encoder input, SAADC (slider), and up to 5 BLE profiles natively.

---

## 2026-07-28 — Switch mounting: solderable

Direct through-hole solder, no hotswap sockets. Footprint: `Gateron_KS33_Solderable:Gateron-KS33-Solderable-1U`. Hotswap ruled out — unnecessary complexity for a fixed productivity deck.

---

## 2026-07-28 — Encoder: Bourns PEC11R-4220F-S0024 (24 detent)

24 pulses per revolution. Footprint already matches this variant (`libs/rotary-encoder-PEC11R`). Order specifically the S0024 suffix.

---

## 2026-07-28 — Switch family: Gateron KS-33 (Low Profile 2.0)

19x19mm standard MX grid, 3-pin, 12.2mm housing height, MX-compatible stems. Footprint: github.com/ai03-2725/MX_V2. Kailh Choc V2 ruled out.

---

## 2026-07-28 — Project structure defined

Folders: `KiCAD/`, `firmware/`, `app/`, `enclosure/`, `Docs/`, `.claude/`.
App and firmware stacks not yet chosen — tracked as open decisions in `context.md`.

---

<!-- Add new decisions above this line -->
