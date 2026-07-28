# Modular StreamDeck — Component Spec Sheet

*For chassis / enclosure design — prepared for product designer review · v1*

This sheet lists the candidate electrical components under evaluation for the button grid, encoder (dial) row, and slider, along with the physical dimensions relevant to panel cutouts and enclosure design. Part selection is not finalized — where two options are listed, both are still under consideration. See the stack-up note at the end before finalizing cutout depths.

**Planned layout:** 6 buttons (modular grid) + 3 rotary encoders (modular row) + 1 slider (fixed position).

---

## 1. Key Switches (6x, modular grid)

| Spec | Kailh Choc V2 (PG1353) | Gateron KS-33 (Low Profile 2.0) |
|---|---|---|
| Total travel | 3.2mm (pre-travel 1.3mm ±0.3mm) | 3.0mm ±0.2mm (pre-travel 1.7mm) |
| Housing height | ~10.8mm (Choc-standard housing) | 12.2mm (taller than Gateron KS-27) |
| PCB grid spacing | 18 x 17mm — non-standard, compact grid | 19 x 19mm — standard MX grid |
| Keycap compatibility | MX cross-stem compatible (V2 only) | MX-compatible stems; standard MX low-profile keycaps fit |
| Pin count | 5-pin (plate mount) | 3-pin |

**Chassis-relevant:** switch housing height sets the minimum clearance between the PCB plane and the underside of the keycap opening. Grid spacing (18x17 vs 19x19mm) directly sets the button cutout pitch across the whole grid — this is the number that drives the panel's hole-pattern CAD, not the keycap size itself.

---

## 2. Rotary Encoders (3x, modular row) — Bourns PEC11R series

| Spec | Value |
|---|---|
| Body diameter | 12mm |
| Shaft diameter | 6mm, flatted end |
| Shaft length options | 15mm / 20mm / 25mm / 30mm (select based on knob height + panel thickness) |
| Mounting | Metal bushing, panel mount, through-hole PCB pins |
| Panel mounting hole | 7mm diameter, M7 x 0.75 threaded bushing with hex nut |
| Detent options | 12 / 18 / 24 pulses per revolution |
| Push-button option | Available ("S" suffix) — adds a click function on the same shaft |

**Chassis-relevant:** panel needs a 7mm hole per encoder to pass the mounting bushing through, secured with the hex nut from the front face. Shaft length must be chosen once panel thickness + knob height are known — too short and the knob can't be fixed to the shaft; too long and it needs trimming.

---

## 3. Slider (1x, fixed position) — Alps RS-series

| Travel | Series / part | Approx. overall length |
|---|---|---|
| 45mm (compact) | RS\*\*1 series, e.g. RS451111B010 | ~72–75mm |
| 60mm (standard) | RS60N111900H / RSA0N1219A03 (60mm variant) | ~87–90mm |
| 100mm (long-throw) | RSA0N1219A03 (100mm variant) | ~127–128mm |

**Confirmed reference part (60mm travel):** Alps RS60N111100B — overall length 88mm, body 16mm (W) x 8mm (H), mounting hole spacing 80mm. This is a vector-sourced datasheet figure, higher confidence than the general family approximations below.

General RS-series family (all travel lengths): body width ~18.5mm, actuator/lever operating length 8.2mm, mounting via 2x M3 screw holes plus locating pins. Slider travel length not yet finalized — see open decisions below.

> **Note on data confidence:** outside the confirmed 60mm reference part above, the Alps mechanical drawings are published as image-based PDFs, so other length figures are close approximations from parsed text, not verified vector-drawing tolerances. Pull the exact drawing for the final chosen part number directly from tech.alpsalpine.com before cutting any panel slot.

---

## 4. KiCad Footprint / Symbol Resources

| Component | Availability | Source |
|---|---|---|
| Kailh Choc V2 (PG1353) | Ready-made, solderable + hotswap variants | github.com/ai03-2725/MX_V2 <br> github.com/perigoso/keyswitch-kicad-library |
| Gateron KS-33 (LP 2.0) | Ready-made, hotswap footprint | github.com/ai03-2725/MX_V2 |
| Bourns PEC11R encoder | Ready-made, footprint + symbol + 3D model | github.com/ubiqueIoT/rotary-encoder-PEC11R <br> SnapMagic/SnapEDA (snapeda.com) |
| Alps RS-series slider | No dedicated library — custom footprint needed | SnapEDA has isolated part coverage only; build from tech.alpsalpine.com mechanical drawing |

> **Blocking dependency:** switch footprints differ by grid spacing and pin count between Choc V2 and Gateron KS-33 — the button grid layout cannot proceed until one is chosen. The slider footprint will need to be hand-built once travel length is finalized; budget real time for this, it will not be a quick library import like the switches and encoder.

---

## 5. Before finalizing cutouts: the standoff stack-up

None of the dimensions above translate directly into a panel cutout on their own. Each one sits on top of a PCB-to-front-panel standoff height, which has not been fixed yet. The panel opening for each component is a function of (a) the component's own shaft/actuator/housing dimension, and (b) how far the PCB is mounted below the front panel. We recommend building a simple side-profile section view (PCB plane → standoff → panel plane) with each component's shaft/actuator drawn through it before locking case dimensions — this is the step that turns the numbers on this sheet into an actual cutout drawing.

---

*Open decisions still pending: final switch family (Choc V2 vs. Gateron KS-33), slider travel length, encoder shaft length, and PCB-to-panel standoff height.*
