# ProdDeck — Claude Session Context

> Read this at the start of every session. Update the "Current State" and "Open Decisions" sections before ending a session. Keep it short — this is an index, not a notebook.

## What This Project Is

Custom PCB productivity deck (StreamDeck-style). Targets editors, designers, video editors. Has:
- **6x key switches** — button grid
- **3x rotary encoders** — dial row
- **1x slider** — fader/volume-style control
- **Desktop configurator app** — maps keys/dials/slider to shortcuts, macros, app profiles

## Repo Structure

```
ProdDeck/
├── .claude/            ← you are here
│   ├── context.md      ← this file (update each session)
│   └── decisions.md    ← component & architecture decision log
├── Docs/               ← specs, datasheets notes, BOM
├── KiCAD/ProdDeck/     ← KiCAD schematic + PCB layout
├── firmware/           ← MCU firmware (language/framework TBD)
├── app/                ← desktop configurator (stack TBD)
│   └── src/
└── enclosure/          ← 3D/mechanical files (Fusion360 or FreeCAD)
```

## Current State (as of 2026-07-28)

- **Phase:** Early planning / component selection
- KiCAD project initialized (empty schematic + PCB)
- Component specs documented in `Docs/streamdeck_component_specs.md`
- **Switch decided: Gateron KS-33 solderable, 19.05×19.05mm grid** — footprint: `Gateron_KS33_Solderable:Gateron-KS33-Solderable-1U`
- **Encoder decided: Bourns PEC11R-4220F-S0024** (24 detent, push button)
- **Firmware decided: ZMK** (devicetree YAML config, BLE HID + encoder + SAADC native)
- **MCU decided: bare STM32WB55** (STM32WB55CEU6 512KB or STM32WB55CGU6 1MB, pin-compatible — flash size is a late BOM decision). 48-pin UFQFPN, dual-core (Cortex-M4 app + Cortex-M0+ BLE wireless coprocessor). PCB assembly outsourced to manufacturer (JLCPCB/PCBWay) — hand-solderability no longer a constraint. Supersedes the E73-2G4M08S1C module.
- **New components:** MCP73831 charging IC, LiPo battery (400–600mAh), JST-PH connector, diode-OR power path (unchanged). **Now also needed on-PCB** (previously internal to the E73 module): HSE + LSE crystals, RF matching network + antenna, SWD programming header (BOOT0 + SWD, not just USB) — see Phil's Lab reference design and ST app notes AN5165/AN2867
- **Slider decided: Bourns PTA6043-2015DPB103** (2026-08-02, superseded the Alps RS60N111100B — cheaper/more available; see decisions.md). Placed in schematic as **R12** (imported symbol's default ref prefix, cosmetic quirk), fully wired.
- Schematic has substantial progress — see `KiCAD/ProdDeck/ProdDeck.kicad_sch` directly for current state (component/net count changes frequently; this doc lags behind, always verify live rather than trusting this section)
- No firmware started
- No app started
- No enclosure started

## Open Decisions (blocking)

1. ~~**Switch family**~~ — **Decided: Gateron KS-33 (Low Profile 2.0)**, 19.05×19.05mm grid (0.75"), 3-pin, MX-compatible stems. Footprint source: github.com/ai03-2725/MX_V2
2. ~~**Slider travel**~~ — **Decided: Alps RS60N111100B (60mm travel).** Overall 88mm, body 16×8mm, mounting hole spacing 80mm. Pull datasheet from tech.alpsalpine.com before cutting panel slot.
3. **Encoder shaft length** — depends on panel thickness + knob height. Not decidable until standoff stack-up is known.
4. **PCB-to-panel standoff height** — needed before any cutout dimensions can be finalized.
5. ~~**MCU**~~ — **Decided: bare STM32WB55** (CEU6/CGU6, 48-pin UFQFPN, pin-compatible across flash sizes). Dual-mode: wired USB HID + BLE. ZMK firmware retained (not CubeIDE/HAL). Needs on-PCB: HSE/LSE crystals, RF matching + antenna, SWD header. Needs tooling: ST-Link (SWD) + STM32CubeProgrammer for the wireless-coprocessor stack. Still open: exact flash size (E vs G) — deferred to order time.
6. **App stack** — not yet selected. Options: Electron/Tauri + Rust/Node, or Python + Qt.

## Component Candidates (from Docs/streamdeck_component_specs.md)

| Component | Candidate A | Candidate B | Status |
|---|---|---|---|
| Key switch | ~~Kailh Choc V2 (PG1353)~~ | **Gateron KS-33, solderable** | **Decided** |
| Encoder | **Bourns PEC11R-4220F-S0024** | — | **Decided (24 detent)** |
| Slider | **Alps RS60N111100B (60mm)** | — | **Decided** |

## KiCAD Footprint Libraries

All registered in `KiCAD/ProdDeck/fp-lib-table` — KiCAD loads automatically.

- `Gateron_KS33_Solderable` → `libs/MX_V2/Gateron_KS33_Solderable.pretty` ✓
- `Gateron_KS33_Hotswap` → `libs/MX_V2/Gateron_KS33_Hotswap.pretty` ✓ (available, not used)
- `PEC11R_Encoder` → `libs/rotary-encoder-PEC11R` ✓
- `ProdDeck_Custom` → `libs/ProdDeck_Custom.pretty` (has a hand-built Alps RS60N111100B slider footprint — now unused, superseded by the Bourns swap below, kept for reference only)
- `slider` → `libs/Bourns_PTA6043/` (symbol + `footprints.pretty` + 3D model) — Bourns PTA6043-2015DPB103, the current slider part. Own `sym-lib-table` entry too (not just fp-lib-table).

**Removed:** `nrfmicro` library (E73-2G4M08S1C footprint) — no longer needed after the MCU switch to bare STM32WB55. Deleted from `libs/` and unregistered from `fp-lib-table`.

## Notes for Next Session

Schematic plan is fully written in `Docs/schematic_plan.md` — use it as the reference while working in KiCAD.

**Immediate next steps (in order):**
1. ~~Clone external libraries~~ — done. `libs/MX_V2` and `libs/rotary-encoder-PEC11R` cloned.
2. ~~Add libs to KiCAD~~ — done. `fp-lib-table` and `sym-lib-table` written; KiCAD will pick them up on next open.
3. ~~**Decide: solderable vs hotswap**~~ — **Decided: solderable.** Footprint: `Gateron_KS33_Solderable:Gateron-KS33-Solderable-1U`
4. ~~**Decide: encoder detent count**~~ — **Decided: PEC11R-4220F-S0024 (24 detent).** Footprint matches.
5. Build custom Alps RS60N111100B footprint in `libs/ProdDeck_Custom.pretty/` from datasheet (tech.alpsalpine.com)
6. Build STM32WB55 (CEU6/CGU6) schematic symbol + footprint in `libs/ProdDeck_Custom.pretty/` — follow Phil's Lab's KiCad 7 STM32 Bluetooth tutorial for symbol/footprint creation, pin power/decoupling, HSE/LSE crystal circuit, RF matching + antenna, SWD/BOOT0 header, and USB circuit
7. Start schematic — work through `Docs/schematic_plan.md` top to bottom
8. Decide app stack (Tauri / Electron) — firmware framework already decided (ZMK)
