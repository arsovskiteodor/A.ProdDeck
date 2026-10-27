# Modular StreamDeck — Component Spec Sheet

*For chassis / enclosure design — prepared for product designer review · v1*

This sheet lists the selected electrical components for the button grid, encoder (dial) row, and slider, along with the physical dimensions relevant to panel cutouts and enclosure design. All parts are selected and the PCB is laid out; ruled-out options are kept struck through for reference. See the stack-up note at the end before finalizing cutout depths.

**Planned layout:** 6 buttons (modular grid) + 3 rotary encoders (modular row) + 1 slider (fixed position).

---

## 1. Key Switches (6x, modular grid)

**Selected: Gateron KS-33 (Low Profile 2.0), direct solderable** — decided 2026-07-28. Hotswap ruled out.

| Spec | ~~Kailh Choc V2 (PG1353)~~ | **Gateron KS-33 (Low Profile 2.0)** ✓ |
|---|---|---|
| Total travel | ~~3.2mm (pre-travel 1.3mm ±0.3mm)~~ | 3.0mm ±0.2mm (pre-travel 1.7mm) |
| Housing height | ~~~10.8mm (Choc-standard housing)~~ | 12.2mm (taller than Gateron KS-27) |
| PCB grid spacing | ~~18 x 17mm — non-standard, compact grid~~ | **19.05 x 19.05mm — standard MX grid (= 0.75")** |
| Keycap compatibility | ~~MX cross-stem compatible (V2 only)~~ | MX-compatible stems; standard MX low-profile keycaps fit |
| Pin count | ~~5-pin (plate mount)~~ | **3-pin** |

KiCAD footprint: **github.com/ai03-2725/MX_V2** (hotswap variant available).

**Chassis-relevant:** 19x19mm grid pitch drives all button cutout positions on the front panel. Housing height (12.2mm) sets minimum PCB-to-panel clearance.

**Panel cutout (per switch):**

| Dimension | Value | Notes |
|---|---|---|
| Hole shape | Rectangular | Standard MX plate cutout |
| Width | 14.0mm | MX-compatible standard |
| Height | 14.0mm | MX-compatible standard |
| Corner radius | 0.5mm | Typical for CNC plate; sharp corners acceptable for laser cut |
| Grid pitch | 19.05mm × 19.05mm | Center-to-center (= 0.75") — 5.05mm of material between adjacent hole edges |

The 14×14mm hole is for the switch housing to clip into the plate. The keycap sits above the plate surface. For 6 switches in a 3×2 arrangement, the total button grid area is (2×19.05 + 14) mm × (1×19.05 + 14) mm = **52.1mm × 33.05mm**.

---

## 2. Rotary Encoders (3x, modular row) — Bourns PEC11R series

**Selected: Bourns PEC11R-4220F-S0024** — decided 2026-07-28. 24 detents/rev, flatted shaft, push-button switch included (switch pins may be left unconnected if the click function isn't used — it's electrically independent from the A/B/Common quadrature pins, so no footprint or firmware cost either way).

| Spec | Value |
|---|---|
| Terminal config | `4` — PC pin, horizontal/rear-facing |
| Body (across flats) | 12.5mm × 12.5mm |
| Housing depth | 13.4mm |
| Shaft diameter | 6.0mm, flatted |
| Shaft length | 20mm (placeholder — **still open**, see below) |
| Mounting | Metal bushing, panel mount, through-hole PCB pins |
| Panel mounting hole | 7mm diameter, M7 x 0.75 threaded bushing with hex nut |
| PCB pin pitch (A/Common/B) | 5.0mm |
| PCB pin pitch (switch pins, S variant) | 7.0mm |
| Mounting/anti-rotation pins | Ø1.0 +0.1/−0mm, on a 13.2 × 13.4mm pattern |
| Detent option | 24 pulses per revolution |
| Switch option | `S` — push momentary switch (SPST, 0.5±0.3mm travel) |
| General tolerance | ±0.3mm (<10mm dims), ±0.5mm (≥10mm dims) |

Source: official Bourns PEC11R datasheet (bourns.com/docs/Product-Datasheets/PEC11R.pdf).

**Chassis-relevant:** panel needs a 7mm hole per encoder to pass the mounting bushing through, secured with the hex nut from the front face. Shaft length must be chosen once panel thickness + knob height are known — too short and the knob can't be fixed to the shaft; too long and it needs trimming. **Shaft length does not affect the footprint** — the PCB pin layout, panel bushing, and mounting hole pattern are identical across all PEC11R-4xxxF shaft-length variants (15/20/25/30mm), so the final length can be swapped in later without touching the schematic or footprint.

**Panel cutout (per encoder):**

| Dimension | Value | Notes |
|---|---|---|
| Hole shape | Circular | |
| Diameter | 7.0mm | Passes M7 × 0.75 threaded bushing through panel |
| Tolerance | +0.1 / −0mm | Bushing must pass through; hex nut clamps from front |

---

## 3. Slider (1x, fixed position) — Bourns PTA6043-2015DPB103

**Selected: Bourns PTA6043-2015DPB103 (60mm travel)** — changed 2026-09-20 to a smaller, cheaper part. Supersedes the Alps RS60N111100B (decided 2026-07-28).

| Spec | Value |
|---|---|
| Series | Bourns PTA slide potentiometer |
| Travel | 60mm |
| Resistance | 10kΩ (`B103`), linear taper |
| PCB footprint | `slider:POT_PTA6043` — 7 pads, 8.4mm × 73.4mm, long axis along PCB Y |
| Mounting | Through-hole PCB pins + mounting tabs (no panel screws) |

Footprint, symbol and 3D model: `KiCAD/ProdDeck/libs/Bourns_PTA6043/`.

**Panel cutout (slider slot):**

| Dimension | Value | Confidence | Notes |
|---|---|---|---|
| Slot shape | Rectangular | — | |
| Slot length | TBD | — | 60mm travel + actuator lever width. **Take from the Bourns PTA datasheet.** |
| Slot width | TBD | — | Actuator lever width + clearance. **Take from the Bourns PTA datasheet.** |
| Corner radius | ~1mm | Approximate | Smooth corners reduce stress concentration |

The slot length is **not** just the 60mm travel. The actuator lever occupies physical space at both ends of its range, so the opening must extend beyond the travel limits.

> **Before cutting the panel slot:** pull the PTA series mechanical drawing from bourns.com and verify the lever width and slot dimensions. The 3D model in `libs/Bourns_PTA6043/3dmodel/` can also be measured in the enclosure CAD.

---

## 4. KiCad Footprint / Symbol Resources

| Component | Availability | Source |
|---|---|---|
| Kailh Choc V2 (PG1353) | Ready-made, solderable + hotswap variants | github.com/ai03-2725/MX_V2 <br> github.com/perigoso/keyswitch-kicad-library |
| Gateron KS-33 (LP 2.0) | Ready-made, solderable + hotswap footprints + 3D model — **used** | github.com/ai03-2725/MX_V2 (vendored in `libs/MX_V2/`) |
| Bourns PEC11R encoder | Ready-made, footprint + symbol + 3D model — **used** | github.com/ubiqueIoT/rotary-encoder-PEC11R (vendored in `libs/rotary-encoder-PEC11R/`) <br> SnapMagic/SnapEDA (snapeda.com) |
| Bourns PTA6043 slider | Ready-made, footprint + symbol + 3D model — **used** | `libs/Bourns_PTA6043/` |

> All footprints are in place and the PCB is laid out. The old hand-built Alps footprint (`libs/ProdDeck_Custom.pretty/Alps_RS60N111100B.kicad_mod`) is unused and does not fit the Bourns part.

---

## 5. Panel Cutout Summary

| Component | Shape | Size | Qty | Confidence |
|---|---|---|---|---|
| Key switch (Gateron KS-33) | Rectangle | 14.0 × 14.0mm, r0.5mm corners | 6 | High — MX standard |
| Rotary encoder (PEC11R) | Circle | Ø7.0mm | 3 | High — from datasheet |
| Slider (Bourns PTA6043) | Rectangle (slot) | TBD | 1 | Take from Bourns PTA datasheet |

> Slot length and width for the slider must be taken from the Bourns PTA datasheet before finalizing the enclosure design.

---

## 6. Before finalizing cutouts: the standoff stack-up

None of the dimensions above translate directly into a panel cutout on their own. Each one sits on top of a PCB-to-front-panel standoff height, which has not been fixed yet. The panel opening for each component is a function of (a) the component's own shaft/actuator/housing dimension, and (b) how far the PCB is mounted below the front panel. We recommend building a simple side-profile section view (PCB plane → standoff → panel plane) with each component's shaft/actuator drawn through it before locking case dimensions — this is the step that turns the numbers on this sheet into an actual cutout drawing.

---

*Open decisions still pending: encoder shaft length and PCB-to-panel standoff height. Switch family (Gateron KS-33), slider (Bourns PTA6043-2015DPB103, 60mm), and encoder model (Bourns PEC11R-4220F-S0024, 24 detent) all resolved.*
