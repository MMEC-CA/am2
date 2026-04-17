# am2 – Marble Sadness

Multiplayer WebRTC marble-rolling game. Last marble on the table wins!

**Repository**: MMEC-CA / am2  
**Status**: Ready for LAN play with local 2-player support

## About Marble Sadness

A fast-paced elimination game where up to 12 players control marbles rolling on a tilted table surface. Marbles that fall off are eliminated—the last one standing wins!

- **Lobby**: 12 color-coded player slots
- **Gameplay**: Marbles roll due to gravity; higher difficulty levels increase tilt and friction
- **Victory Condition**: Last marble remaining on table
- **Multiplayer**: WebRTC P2P (auto-groups by LAN IP)
- **Local Play**: 2 players on 1 keyboard (arrows + WASD)

👉 **[Full Game Documentation](./README-MARBLE-SADNESS.md)**

## Quick Start

### Local (Development)
```bash
cd /workspaces/am2
python3 -m http.server 8000
# Open http://localhost:8000/am2/
```

### Deployment

- Place the site on Cloudflare Pages or another static host
- Ensure the root of the site is the repository root
- With the current structure, `am2/index.html` will be served at `erd.mmec.ca/am2/`
- Root `index.html` already redirects to `/am2/`

## Architecture

- **Game Client**: `am2/index.html` (self-contained, all-in-one HTML file)
- **Rendering**: Canvas-based with 1920×1080 game coordinates
- **Networking**: WebRTC peer-to-peer with WebSocket signaling
- **Based on**: [gm1 Joustine](https://github.com/MMEC-CA/gm1) — same multiplayer architecture

## Controls

| Context | Input | Action |
|---------|-------|--------|
| Lobby | Arrow Keys ↔ | Change level (difficulty) |
| Lobby | SPACE | Hold 3s to ready up |
| Game | Arrow Keys | P1 control (tilt table left/right) |
| Game | WASD | P2 control (local 2-player) |
| Any | ESC / ` | Return to lobby |

## Files

```
am2/
├── README.md                          (this file)
├── README-MARBLE-SADNESS.md          (detailed documentation)
├── index.html                         (root redirect)
└── am2/
    └── index.html                     (full game)
```

---

*Marble Sadness – Built April 17, 2026 – MMEC-CA*
