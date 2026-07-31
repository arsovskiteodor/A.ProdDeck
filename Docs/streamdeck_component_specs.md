# Modular StreamDeck — Component Spec Sheet

*For chassis / enclosure design — prepared for product designer review · v1*

This sheet lists the candidate electrical components under evaluation for the button grid, encoder (dial) row, and slider, along with the physical dimensions relevant to panel cutouts and enclosure design. Part selection is not finalized — where two options are listed, both are still under consideration. See the stack-up note at the end before finalizing cutout depths.

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

## 3. Slider (1x, fixed position) — Alps RS60N111100B

**Selected: Alps RS60N111100B (60mm travel)** — decided 2026-07-28. Custom KiCAD footprint must be built from datasheet before schematic footprint assignment.

| Spec | Value |
|---|---|
| Travel | 60mm |
| Overall length | 88mm |
| Body | 16mm (W) × 8mm (H) |
| Mounting hole spacing | 80mm |
| Mounting | 2× M3 screw holes + locating pins |

Other travel lengths considered and ruled out: 45mm (too short for comfortable use), 100mm (enclosure too long).

**Panel cutout (slider slot):**

| Dimension | Value | Confidence | Notes |
|---|---|---|---|
| Slot shape | Rectangular | — | |
| Slot length | ~68mm | Approximate | 60mm travel + ~4mm actuator overhang each end. **Verify actuator overhang from datasheet.** |
| Slot width | ~5mm | Approximate | Actuator lever width + clearance. **Verify from datasheet.** |
| Corner radius | ~1mm | Approximate | Smooth corners reduce stress concentration |

The slot length is **not** just the 60mm travel. The actuator lever occupies physical space at both ends of its range, so the opening must extend beyond the travel limits. Pull the mechanical drawing from tech.alpsalpine.com and measure the actuator lever dimensions before cutting.

> **Before cutting the panel slot:** pull the exact mechanical drawing for RS60N111100B directly from tech.alpsalpine.com and verify all cutout dimensions. The custom KiCAD footprint must also be built from this drawing.

---

## 4. KiCad Footprint / Symbol Resources

| Component | Availability | Source |
|---|---|---|
| Kailh Choc V2 (PG1353) | Ready-made, solderable + hotswap variants | github.com/ai03-2725/MX_V2 <br> github.com/perigoso/keyswitch-kicad-library |
| Gateron KS-33 (LP 2.0) | Ready-made, hotswap footprint | github.com/ai03-2725/MX_V2 |
| Bourns PEC11R encoder | Ready-made, footprint + symbol + 3D model | github.com/ubiqueIoT/rotary-encoder-PEC11R <br> SnapMagic/SnapEDA (snapeda.com) |
| Alps RS-series slider | No dedicated library — custom footprint needed | SnapEDA has isolated part coverage only; build from tech.alpsalpine.com mechanical drawing |

> **Switch decided (2026-07-28):** Gateron KS-33, 19x19mm grid, 3-pin. Button grid layout can now proceed once the library is added to KiCAD. The slider footprint will still need to be hand-built once travel length is finalized; budget real time for this, it will not be a quick library import like the switches and encoder.

---

## 5. Panel Cutout Summary

| Component | Shape | Size | Qty | Confidence |
|---|---|---|---|---|
| Key switch (Gateron KS-33) | Rectangle | 14.0 × 14.0mm, r0.5mm corners | 6 | High — MX standard |
| Rotary encoder (PEC11R) | Circle | Ø7.0mm | 3 | High — from datasheet |
| Slider (Alps RS60N111100B) | Rectangle (slot) | ~68mm × ~5mm | 1 | Approximate — verify from Alps datasheet |

> Slot length and width for the slider must be confirmed from tech.alpsalpine.com before finalizing the enclosure design.

---

## 6. Before finalizing cutouts: the standoff stack-up

None of the dimensions above translate directly into a panel cutout on their own. Each one sits on top of a PCB-to-front-panel standoff height, which has not been fixed yet. The panel opening for each component is a function of (a) the component's own shaft/actuator/housing dimension, and (b) how far the PCB is mounted below the front panel. We recommend building a simple side-profile section view (PCB plane → standoff → panel plane) with each component's shaft/actuator drawn through it before locking case dimensions — this is the step that turns the numbers on this sheet into an actual cutout drawing.

---

*Open decisions still pending: encoder shaft length and PCB-to-panel standoff height. Switch family (Gateron KS-33), slider (Alps RS60N111100B, 60mm), and encoder model (Bourns PEC11R-4220F-S0024, 24 detent) all resolved.*
