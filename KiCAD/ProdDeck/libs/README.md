# KiCAD External Libraries

Libraries in this folder are already registered in `../fp-lib-table` and `../sym-lib-table` — KiCAD loads them automatically when the project is opened. No manual setup needed.

---

## Cloned libraries (in this folder)

### 1. Gateron KS-33 switch footprints — `MX_V2/`

Source: `git clone https://github.com/ai03-2725/MX_V2.git`

Registered libraries:
- `Gateron_KS33_Solderable` → `MX_V2/Gateron_KS33_Solderable.pretty` — **use this one** (`Gateron-KS33-Solderable-1U`)
- `Gateron_KS33_Hotswap` → `MX_V2/Gateron_KS33_Hotswap.pretty` — available but not used

### 2. Bourns PEC11R encoder footprint — `rotary-encoder-PEC11R/`

Source: `git clone https://github.com/ubiqueIoT/rotary-encoder-PEC11R.git`

Registered library:
- `PEC11R_Encoder` → `rotary-encoder-PEC11R/` — use `Bourns-PEC11R-4220F-S0024-0-0-0`

### 3. Project custom footprints — `ProdDeck_Custom.pretty/`

Registered library:
- `ProdDeck_Custom` → `ProdDeck_Custom.pretty/`

**Alps RS60N111100B slider footprint must be built here by hand** — no library exists for this part. Build from the mechanical drawing at tech.alpsalpine.com:
- Overall length: 88mm
- Body: 16mm (W) × 8mm (H)
- Mounting hole spacing: 80mm (2× M3)
- Travel: 60mm
- Locating pin positions: verify from datasheet before finalizing

---

## KiCAD built-in libraries (no clone needed)

### E73-2G4M08S1C module (nRF52840)

Castellated module — replaces bare nRF52840-QIAA chip. No QFN footprint needed.

Get the KiCAD footprint from:
- **SnapEDA**: snapeda.com — search "E73-2G4M08S1C"
- **Ultra Librarian**: ultralibrarian.com — same search
- Or build manually from the E73 mechanical drawing (castellated pad layout)

Place the `.kicad_sym` and `.kicad_mod` files in `ProdDeck_Custom.pretty/`, then add the symbol to `sym-lib-table` manually.

Module positioning note: place with antenna end toward a board edge — refer to E73 datasheet keep-out zone.

### MCP73831 charging IC

Available in KiCAD built-in: `Battery_Management:MCP73831`
No external clone needed.

### AP2112K LDO

Available in KiCAD built-in: `Regulator_Linear:AP2112K-3.3`
No external clone needed.

### USB-C connector

Available in KiCAD built-in: `Connector:USB_C_Receptacle_*`
Match to the specific part ordered (USB4105-GF-A or GCT USB4135).
