# Raycaster — GBA prototype

A first-person corn maze prototype for the Game Boy Advance. Not a full game: a small maze to walk, turn, and escape from.

![Animated demo: turn, step, turn back](screenshots/demo.gif)

## Play

Browser: https://zippy-cherry-p4th.here.now/

Or load `releases/raycaster-gba-fast.gba` in a GBA emulator.

## Controls

- Up: step forward one tile
- Down: step back one tile
- Left / Right: turn 90 degrees
- Start: start / restart
- Tap to step. Holding a direction repeats after a short pause.

Reach the clearing at the far end of the maze for the exit screen.

## Finding the exit (read this first, it will save your sanity)

The exit is deliberately, unfairly hidden in this prototype, and it is the same maze in the GBA and GBC versions:

- The exit tile is at (10,10), at the far end of the maze, and you can only step onto it from the north, from (10,9) heading south.
- It looks exactly like a normal corn wall. It does not look different, it does not glow, and it is not at the end of a straight corridor you would bump into by accident.
- The two brown walls are scarecrows, at (10,1) and (8,10). They block with a bump sound and are not the exit. If a wall dings, skip it.
- When you step onto the exit tile, you get the YOU ESCAPED! / THE FIELD LETS GO screen, then Press Start to walk it again.

Walkthrough from the start, facing east:

Turn right, step x3, turn left, step x2, turn right, step x2, turn right, step x2, turn left, step x2, turn left, step x2, turn right, step x2, turn left, step x3, turn left, step, turn right, step x4, turn right, step.

That last step is south into (10,10) and triggers the exit screen.


## Screenshots

![Title screen](screenshots/title.png)

![Start view, facing east down the corridor](screenshots/start.png)

![Corridor after stepping south](screenshots/corridor.png)

![Close wall, capped so sky and floor stay visible](screenshots/close-wall.png)

## How it works

- Mode 4 bitmap, 240 columns, DDA raycasting with fixed-point math and table trig
- Textured corn walls with distance and side shading, walking bob
- Tile-step movement: each step and turn animates over 4 frames, then the game goes idle and renders nothing until the next tap
- Per-frame divisions are replaced by precomputed tables, and wall height is capped so a close wall never fills the whole screen
- Short PSG step / bump / exit sounds only

## Build

Requires `arm-none-eabi-gcc` and Python 3.

```sh
bash build.sh
```

This produces `raycaster.gba` and copies it to `releases/raycaster-gba.gba`. The header is patched by `fix_header.py` (title `RAYCASTER`, game code `RAYC`).

## Files

- `src/main.c` — game, raycaster, sound, title / exit screens
- `src/trig.h` — sine / cosine tables
- `src/font.h` — 8x8 font glyphs
- `src/tables.h` — precomputed DDA / wall-height / texture-step tables
- `src/crt0.s`, `gba.ld` — GBA startup and linker script
- `qa/harness.c`, `qa/render.py` — host QA harness (runs the game natively, dumps the framebuffer) and its framebuffer renderer
