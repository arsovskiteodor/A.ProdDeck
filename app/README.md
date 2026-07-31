# ProdDeck Configurator App

Desktop app for configuring the ProdDeck. Lets users assign shortcuts, macros, and app-specific profiles to each key, dial, and slider position.

## Planned Features

- Key binding editor (per key, per app profile)
- Dial action editor (CW / CCW / press actions)
- Slider assignment (volume, scroll, zoom, custom)
- App-profile switching (auto-detect active app or manual)
- Firmware flash / device management

## Stack

**Not yet decided.** Candidates:
- Tauri (Rust backend + web frontend) — small binary, native feel
- Electron (Node.js backend + web frontend) — larger but faster to prototype

Decision will be logged in `.claude/decisions.md` once made.

## Development

```
app/
└── src/    ← source goes here once stack is chosen
```
