# ProdDeck

Custom PCB productivity deck — StreamDeck-style device with key switches, rotary encoders, and a slider. Targets editors, designers, and video editors.

**Planned hardware:**
- 6x key switches (button grid)
- 3x rotary encoders (dial row)
- 1x slider (fader)

## Repository Layout

| Folder | Contents |
|---|---|
| `KiCAD/ProdDeck/` | Schematic and PCB layout (KiCAD 7+) |
| `firmware/` | MCU firmware |
| `app/` | Desktop configurator — maps keys/dials/slider to shortcuts and app profiles |
| `enclosure/` | 3D mechanical files for chassis/panel |
| `Docs/` | Component specs, datasheets notes, BOM |
| `.claude/` | Session context for AI-assisted development |

## Status

Early planning phase — component selection in progress. See `Docs/streamdeck_component_specs.md` for current candidates and open decisions.
