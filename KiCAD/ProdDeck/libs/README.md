# KiCAD Project Libraries

Libraries in this folder are registered in `../fp-lib-table` and `../sym-lib-table` with `${KIPRJMOD}` paths — KiCAD loads them automatically when the project is opened. No manual setup needed.

The third-party libraries below are vendored (plain copies committed to this repo, not Git submodules), and some files have been edited locally. Don't re-clone over them.

---

## Libraries in this folder

### 1. Gateron KS-33 switch footprints — `MX_V2/`

Source: github.com/ai03-2725/MX_V2

Registered libraries:
- `Gateron_KS33_Solderable` → `MX_V2/Gateron_KS33_Solderable.pretty` — **used** (`Gateron-KS33-Solderable-1U`, SW3–SW8)
- `Gateron_KS33_Hotswap` → `MX_V2/Gateron_KS33_Hotswap.pretty` — available, not used

3D model: `MX_V2/3D/Gateron-KS33-Solderable-Body.step`. The footprints have no courtyard, which is why the `missing_courtyard` DRC check is ignored on the PCB.

### 2. Bourns PEC11R encoder — `rotary-encoder-PEC11R/`

Source: github.com/ubiqueIoT/rotary-encoder-PEC11R

Registered library:
- `PEC11R_Encoder` → `rotary-encoder-PEC11R/` — **used** (`Bourns-PEC11R-4220F-S0024-0-0-0`, ENC1–ENC3)

3D model: `rotary-encoder-PEC11R/Bourns_-_PEC11R-4220F-S0024.step`.

### 3. Bourns PTA6043 slider — `Bourns_PTA6043/`

Registered libraries:
- `slider` (footprint) → `Bourns_PTA6043/footprints.pretty` — **used** (`POT_PTA6043`, R12)
- `slider` (symbol) → `Bourns_PTA6043/2026-08-02_19-27-57.kicad_sym`

3D model: `Bourns_PTA6043/3dmodel/PTA6043-2015DPB103.stp`.

### 4. Old custom footprints — `ProdDeck_Custom.pretty/`

Registered library `ProdDeck_Custom`. It contains only `Alps_RS60N111100B.kicad_mod`, the hand-built footprint for the old Alps slider. That part was replaced by the Bourns PTA6043 and this footprint is **unused** — don't use it for R12, the two parts are not interchangeable.

---

## Other project libraries (outside this folder)

| Library | Path | Contents |
|---|---|---|
| `ProdDeck_PCBlib` (footprints) | `../ProdDeck_PCBlib.pretty/` | `DLF162500LT-5028A1` — TDK RF low-pass filter (FLT1) |
| `ProdDeck_SchemLib` (symbols) | `../../ProdDeck_SchemLib.kicad_sym` | `DLF162500LT-5028A1` symbol. **Registered in the global KiCAD symbol table, not the project's `sym-lib-table`** — on another machine, add it there (Preferences → Manage Symbol Libraries). The schematic embeds its own copy, so it still opens without it. |

---

## KiCAD built-in libraries used

Everything else comes from the standard KiCAD libraries, for example:
- `Package_DFN_QFN:QFN-48-1EP_7x7mm_P0.5mm_EP5.6x5.6mm` — STM32WB55CGU6
- `Connector_USB:USB_C_Receptacle_GCT_USB4105-xx-A_16P_TopMnt_Horizontal` — USB-C
- `Connector_JST:JST_PH_S2B-PH-K_1x02_P2.00mm_Horizontal` — battery
- `Connector_Coaxial:U.FL_Hirose_U.FL-R-SMT-1_Vertical` — antenna
- `Connector:Tag-Connect_TC2030-IDC-FP_2x03_P1.27mm_Vertical` — SWD
- `Package_TO_SOT_SMD:*` — MIC5365 LDO, MCP73831 charger, USBLC6 ESD
