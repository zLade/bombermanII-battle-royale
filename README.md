# Bomberman II / Dynablaster 4P Royal Mode - Source Changes Summary

This project converts the patched Bomberman II Famicom ROM into a buildable ca65/ld65 assembly project and extends the Battle Mode code path with a new 4-player "Royal Mode" while preserving the original 3-player Battle Mode behavior.

## Core Project / Build System

- Rebuilt the ROM as a ca65/ld65 assembly project.
- Kept the ROM on the MMC3 conversion base instead of the original MMC1 mapper.
- Added reproducible build outputs and release ROM/IPS generation.
- Verified generated IPS patches against the original Japanese ROM.
- Added helper/debug scripts for ROM inspection, hashing, emulator checks, screenshots, and FCEUX/Mesen-oriented testing.

## New Royal Mode Flow

- Added a new title menu entry: `ROYAL MODE`.
- Preserved original modes:
  - `NORMAL MODE` remains 1P.
  - `VS MODE` remains 2P.
  - `BATTLE MODE` remains original 3P.
  - `ROYAL MODE` uses the new 4P Battle implementation.
- Added Royal Mode-specific state flags so the 4P code does not affect 3P Battle Mode or other modes.
- Added player-count selection support in Royal Mode where needed.
- Fixed match score counting so Royal Mode ends correctly when a player reaches the selected number of wins.

## Player 4 Runtime Support

- Added separate RAM-backed state storage for Player 4.
- Added load/save routines to copy P4 state into the original player work area when running shared gameplay logic.
- Hooked Player 4 into:
  - movement
  - collision
  - animation
  - bomb placement
  - death handling
  - timeout death handling
  - skull/malus handling
  - round winner detection
  - score tracking
- Added dedicated P4 input reading using NES Four Score-compatible controller data.
- Added fallback/guard logic so disconnected P3/P4 controllers do not generate ghost inputs.

## Input / Pause Fixes

- Added Four Score input handling for P3/P4.
- Fixed ghost input issues when the emulator/console is configured without Four Score.
- Added a pause guard so directional input noise cannot accidentally trigger Start/pause.
- Restricted pause detection to a debounced P1 Start press.

## Bomb System Fixes

- Reworked bomb slot allocation to support four players safely.
- Added bomb ownership tracking for shared bomb slots.
- Fixed cases where P4 could move but not place bombs.
- Fixed crashes caused by several players placing bombs at once.
- Fixed bomb allocation so campaign/1P behavior is not broken by Royal Mode changes.

## Battle Map / Spawn Logic

- Added Royal Mode spawn placement for four players.
- Adjusted the Battle arena layout clearing around spawn zones so players do not spawn inside blocks.
- Added logic for different spawn-safe layouts depending on player count.
- Preserved original 3P Battle map behavior.

## READY / Score Screens

- Rebuilt the Battle READY layout for four players.
- Added P4 label, head icon, and score display.
- Repositioned P1-P4 columns for even spacing and clean alignment.
- Added P4 score initialization and reset.
- Fixed P4 score persistence between matches.
- Fixed score incrementing after each round.
- Fixed the final match winner panel trigger based on the selected win count.

## HUD Changes

- Extended the in-game Battle HUD to display:
  - `P1:score`
  - `P2:score`
  - `P3:score`
  - `P4:score`
  - timer
- Reworked HUD spacing to fit four players.
- Fixed skull/malus HUD coloring so each affected player’s own `PX:Y` text is highlighted without bleeding into the next player.
- Fixed malus color reset when a player dies.

## Skull / Malus System

- Added P4 support for skull pickup effects.
- Added P4 support for skull malus transmission by player contact.
- Fixed P4 being immune to skull effects.
- Fixed P4 not transmitting skull effects to other players.
- Restored random skull effect selection after it was accidentally narrowed during earlier patches.
- Verified support for speed, slow, and auto-bomb style maluses.

## Winner / Round-End Screens

- Added `PLAYER 4!` round-win support.
- Fixed cases where P4 wins displayed as draw.
- Fixed malformed `PLAYER 4!` text and sprite tile issues.
- Added a proper P4 final winner screen path.
- Added P4 winner animation handling using safe existing animation logic and palette adjustments.
- Preserved P1/P2/P3 winner screens and original Battle Mode winner behavior.

## Player 4 Graphics / Palette Work

- Added P4-specific gameplay sprite handling.
- Created P4 visual identity distinct from P1/P2/P3 while staying within NES sprite palette limits.
- Added adjusted P4 palettes for gameplay, READY screen, and winner screen.
- Fixed broken P4 up/down/back-facing animation frames.
- Fixed P4 visual similarity issues from behind.
- Preserved original player colors where possible.

## Timer / Draw Handling

- Fixed Royal Mode timeout behavior.
- Prevented P4 from incorrectly winning when the timer reaches zero with multiple players alive.
- Added missing timeout death animation support for P4.
- Restored correct draw behavior when appropriate.

## Stability / Compatibility Fixes

- Fixed campaign crashes caused by Battle/Royal-specific patches leaking into non-Battle modes.
- Added Royal Mode guards around code that must not affect Normal, VS, or original Battle Mode.
- Audited RAM usage to avoid collisions with existing game state.
- Kept the final ROM MMC3-compatible for real NES/Famicom hardware using MMC3 behavior.
