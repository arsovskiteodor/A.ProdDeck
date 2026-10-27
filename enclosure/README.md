# ProdDeck Enclosure

3D mechanical files for the chassis and front panel.

## Status

Not started. The PCB is done (117 x 77 mm, 4-layer) and all components are chosen:

| Component | Part | Panel cutout |
|---|---|---|
| Key switch (6x) | Gateron KS-33 (Low Profile 2.0), 19 x 19 mm grid | 14.0 x 14.0 mm square, r0.5 mm corners |
| Rotary encoder (3x) | Bourns PEC11R-4220F-S0024 | Ø7.0 mm circle |
| Slider (1x) | Bourns PTA6043-2015DPB103, 60 mm travel | Slot — take length/width from the Bourns datasheet |

Still open before cutouts can be finalized:
- Encoder shaft length (footprint is identical across shaft lengths, so this doesn't affect the PCB)
- PCB-to-panel standoff height

See `Docs/streamdeck_component_specs.md` section 6 for the stack-up note.

## CAD Tool

Not yet decided — Fusion 360 or FreeCAD.

The PCB can be exported from KiCad as STEP (File → Export → STEP) to model the enclosure around it.

## Files

Place `.step`, `.f3d`, `.fcstd`, or `.stl` exports here once design starts.
