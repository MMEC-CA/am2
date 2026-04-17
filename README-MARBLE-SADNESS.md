# Marble Sadness

A multiplayer marble-rolling game built on WebRTC for LAN play. Players control marbles rolling on a tilted table surface, with the last marble remaining on the table as the winner.

## Quick Start

### Local Testing (2 Players, 1 Machine)

1. **Serve the game**: Use a local web server
   ```bash
   cd /workspaces/am2
   python3 -m http.server 8000
   ```

2. **Open in browser**: `http://localhost:8000/am2/`

3. **Controls**:
   - **Player 1**: Arrow keys (↔) to tilt table left/right
   - **Player 2 (same keyboard)**: WASD keys to tilt table
   - **Lobby**: Hold SPACE to ready up (3-second hold to confirm)
   - **Level Select**: Arrow keys to change table tilt difficulty
   - **ESC/Backtick**: Return to lobby

### Multiplayer on LAN

1. Both machines must be on the same subnet
2. One machine serves the game (with web server)
3. Connect via the other machine's IP: `http://192.168.x.x:8000/am2/`
4. All players automatically grouped by LAN IP
5. WebRTC peer discovery and connection happens automatically via signaling server (currently configured for gm1 server infrastructure)

## Gameplay

### Objective
**Be the last marble on the table!**

### How It Works
1. **Lobby**: Up to 12 players can join (12 colored slots)
2. **Level Selection**: Choose difficulty (higher levels = more slippery, more tilt)
3. **Countdown**: 3 seconds to prepare
4. **Rolling**: Marbles roll on a tilted table surface
5. **Winning**: Marbles fall off the edges → eliminated
6. **Victory**: Last marble remaining wins!

### Physics
- Marbles experience gravity based on table tilt angle
- Friction increases with difficulty (marbles slip more)
- Table tilt angle ranges from ~0.5° (Level 1) to ~50° (Level 100)
- Marbles eliminated when they leave table boundaries

## Architecture

### Single-File Game Client
- **`am2/index.html`**: Self-contained game with all code inline
  - Canvas-based rendering (1920×1080 game coordinates)
  - WebRTC peer-to-peer connections with automatic LAN discovery
  - Physics simulation for marble rolling
  - Responsive UI for touch and keyboard

### Features
- ✅ 12-player lobby system with color-coded slots
- ✅ Multiplayer support via WebRTC (P2P, no server gameplay)
- ✅ Local 2-player on keyboard (arrows + WASD)
- ✅ Physics-based marble rolling
- ✅ Automatic host election
- ✅ State synchronization
- ✅ Audio effects
- ✅ Game-over detection

### Network Protocol
Uses same signaling infrastructure as [gm1](https://github.com/MMEC-CA/gm1) but simplified data messages:

**Data Channel Messages:**
- `lobby-join`: Player joins lobby
- `lobby-sync`: Sync slot states to late joiners
- `lobby-ready`: Player ready to start
- `game-start`: Host initiates game
- `state`: Host broadcasts marble states (position, velocity, alive status)
- `gameover`: Game ends, announce winner

## Installation & Deployment

### Development
```bash
git clone https://github.com/MMEC-CA/am2.git
cd am2
python3 -m http.server 8000
# Open http://localhost:8000/am2/ in browser
```

### Production (same as gm1)
Uses Cloudflare Workers + Durable Objects for signaling:
```bash
npx wrangler@4 deploy
```

(Requires same deployment setup as gm1 with signaling server)

## File Structure
```
am2/
├── README-MARBLE-SADNESS.md    (this file)
├── am2/
│   └── index.html              (full game - all-in-one)
└── index.html                  (root redirect)
```

## Building on gm1

This game uses the same architecture patterns as [gm1 Joustine](https://github.com/MMEC-CA/gm1):
- Single HTML file with inline game code
- WebRTC P2P with LAN auto-grouping
- Durable Object-based signaling
- Host-authoritative gameplay
- Support for 2-local-player keyboards

Key simplifications vs. gm1:
- No AI opponents
- Simpler physics (marbles vs bird-jousting)
- Marble-specific rendering
- Focus on "last one standing" elimination

## Controls Summary

| Context | Input | Action |
|---------|-------|--------|
| **Lobby** | Arrow Keys ↔ | Change level |
| **Lobby** | SPACE/ENTER | Hold to ready (3s) |
| **Lobby/Game** | WASD (P2) | Local player 2 (keyboard only) |
| **Game** | Arrow Keys | Left/right control (P1) |
| **Game** | WASD | Left/right control (P2 - local) |
| **Game/Lobby** | ESC/` | Return to lobby |

## Known Limitations

1. **Signaling Server**: Currently configured for gm1 signaling infrastructure. For local LAN testing with WebRTC, place all clients on same subnet (auto-groups by IP).
2. **Browser Support**: Requires WebRTC support (Chrome, Firefox, Safari 14.1+, Edge)
3. **Firewall**: May need UDP port forwarding for NAT traversal if across subnets
4. **No Server-Side State**: All gameplay is P2P; no persistent game history

## Future Enhancements

- [ ] Custom signaling server for standalone deployment
- [ ] Power-ups and obstacles
- [ ] Table obstacles (ramps, holes)
- [ ] Multiplayer marble collisions
- [ ] Mobile touch controls for table tilt
- [ ] Sound effects polish
- [ ] Leaderboards

## License

Same as gm1 (check main repo)

---

**Developed for**: MMEC-CA  
**Based on**: gm1 Joustine WebRTC game architecture  
**Built**: April 17, 2026
