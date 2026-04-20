# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

**Marble Sadness** — a multiplayer WebRTC P2P marble-rolling elimination game for 1–12 players. Players control colored marbles on a tilted circular table; last marble on wins. Supports LAN auto-grouping, 2-player local (keyboard split), and AI fill. Game accomidates small browser windows.

## Commands

```bash
# Local dev server
python3 -m http.server 8000
# then open http://localhost:8000/am2/

# Deploy to Cloudflare Workers
npx wrangler@4 deploy
```

No build step, no transpilation, no tests.

## Architecture

The entire game is a **single self-contained file**: `am2/index.html`. All JS is inline — no modules, no frameworks, no npm dependencies (only a CDN QR code library). Do not extract to separate files or introduce a build system.

### Key sections of `am2/index.html`

| Lines | Responsibility |
|-------|---------------|
| 31–61 | Constants: physics params, table dimensions, 12-color palette |
| 64–117 | Global state: lobby, networking, gameplay, input tracking |
| 123–136 | Audio (Web Audio API — tone generation) |
| 165–318 | Physics: `Marble` class, player control, AI behavior, collision resolution |
| 321–510 | Lobby state machine + host election |
| 513–580 | Game lifecycle: start, gameover detection |
| 583–769 | Rendering: lobby, game, gameover screens (Canvas 2D, 1920×1080 coords) |
| 773–1044 | Networking: WebRTC peer connections, WebSocket signaling, data channels |
| 1087–1165 | Input handlers (keydown/keyup) |
| 1170–1234 | Main game loop (`requestAnimationFrame`, dt-based physics) |

### Lobby state machine

`lobbyState` drives all behavior: `lobby` → `countdown` (3 s) → `game` → `gameover` → back to `lobby`. Transitions are triggered by player ready-up (hold SPACE/W for 3 s) or game-over detection.

### Networking model

- **Signaling**: WebSocket server (shared with gm1) for peer discovery, grouped by LAN IP prefix
- **Game data**: RTCDataChannel (unreliable) — host broadcasts marble positions/velocities ~5% of frames; non-hosts interpolate
- **Host election**: lowest `peerId` is authoritative host; re-elected on disconnect
- **Message types**: `lobby-join`, `lobby-sync`, `lobby-ready`, `game-start`, `state`, `gameover`

### Physics

- Marbles eliminated when distance from table center > `TABLE_RADIUS`
- Level 1–100 adjusts friction (`0.96` → `0.88`); higher = more slippery
- AI marbles added when fewer than 3 human players; they chase nearest non-AI marble
- Collision resolution: impulse-based elastic collisions

## Code conventions

- Vanilla JS only — no TypeScript, no frameworks
- All game state lives in module-level variables (not classes or stores)
- Canvas coordinates are 1920×1080; scaled to viewport on resize
- Physics constants (`PLAYER_ACCEL`, `MAX_PLAYER_SPEED`, etc.) are at the top of the script block
