# Project Guidelines

## Code Style
Vanilla JavaScript in a single HTML file. No frameworks or build tools. Reference [am2/index.html](am2/index.html) for code patterns and structure.

## Architecture
Canvas-based multiplayer marble game using WebRTC P2P networking with WebSocket signaling. Host broadcasts game state to peers. See [README-MARBLE-SADNESS.md](README-MARBLE-SADNESS.md) for detailed architecture and physics.

## Build and Test
No build step required. Deploy to Cloudflare Workers using `wrangler deploy`. See [README.md](README.md) for deployment setup and commands.

## Conventions
- Game state machine: `lobbyState` transitions through 'lobby' → 'countdown' → 'game' → 'gameover' → 'observer'
- Networking constants: HOLD_DURATION=3.0s, COUNTDOWN_DURATION=3.0s, GAME_OVER_LOCKOUT=5.0s
- Multiplayer supports up to 12 players with color-coded lobby slots
- Physics: marbles with friction=0.95/frame, tilt-based elimination