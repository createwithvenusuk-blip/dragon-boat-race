# Dragon Boat Race — Game Mechanics

**Applies to:** `play.html` (deployed) and `game/Dragon Boat Race v23 STANDALONE.html` (canonical source — kept byte-identical to `play.html`).
**Live:** https://createwithvenusuk-blip.github.io/dragon-boat-race/play.html
**Last updated:** 2026-06-07

This file is the single source of truth for *how the game behaves*. Update it whenever the
gameplay constants or rules below change.

---

## Core rule: the harder tapper ALWAYS wins

The race is a pure tapping contest. **Every effective tap pushes the boat forward by a fixed
distance.** If you stop tapping, the boat does **not** keep travelling — it only carries a tiny
inertial glide that decays to a stop within a fraction of a second.

> ⚠️ This replaced the old (≤ v22) "speed/inertia" model, where a tap added *speed* and the boat
> coasted for nearly a minute with almost no friction. That let a player tap less and still glide
> to a win as the camera panned. **That bug is fixed** — distance now comes only from taps.

The winner is the first boat to reach the finish (`position >= RACE_DISTANCE`). Because distance
comes only from taps, the player who taps more reaches the finish first.

---

## How a tap moves the boat

On each **effective** tap (i.e. not while exhausted):

1. **Direct push** — the boat's `position` jumps forward by `TAP_IMPULSE`.
2. **Tiny glide** — a small inertial `speed` is added (capped) purely so the motion looks smooth.
   This glide decays fast via `FRICTION` and stops the boat almost immediately when you stop tapping.

`position` is clamped to `RACE_DISTANCE`. `progress = position / RACE_DISTANCE` drives the boat
sprite and the camera pan. The camera pan is **cosmetic** — it never affects who wins.

---

## Exhaustion (stamina)

To stop pure mashing, the boat tires:

- Tapping faster than `BURST_WINDOW` apart counts as a **consecutive** tap and raises `burstCount`.
- When `burstCount` reaches **`BURST_LIMIT` (20 taps)**, the boat is **exhausted**: it freezes and
  ignores taps for `TIRED_DURATION`, with a tired visual on the boat and stamina bar.
- Resting (no taps for a moment) bleeds `burstCount` back down, refilling the stamina bar.

So the winning strategy is **sustained** fast tapping with brief rests — not a single frantic burst.

---

## Controls

| Player | Touch / mouse | Keyboard |
|---|---|---|
| **Red (P1)** | tap the top half (or red tap zone) | `A`, `Q`, or `←` |
| **Green (P2)** | tap the bottom half (or green tap zone) | `L`, `P`, or `→` |
| Either (single player practice) | — | `Space` (alternates P1/P2) |

**Laptop / desktop play is fully supported via the keyboard** — two people can share one keyboard
(`A` vs `L`) instead of fighting over the same touchscreen. Key auto-repeat is blocked (`e.repeat`),
so holding a key does nothing; each push needs a real, separate key press. Pressing any key on the
opening screen also starts the game.

---

## Start scene (orientation)

The game is **locked to option B** (`const MODE = 'b'`). The old `?m=a|b|c` test switch is removed,
so every URL shows the same start scene:

1. **Lock rotation** prompt.
2. 3-second countdown.
3. **Press start** → game begins (requests fullscreen, hides browser UI).

On phones the board rotates 90° in portrait so you play with the phone held sideways while rotation
stays locked (this dodges the iOS landscape toolbar). On desktop it plays in the normal landscape stage.

---

## Tunable constants (in `play.html`)

| Constant | Value | Meaning |
|---|---|---|
| `TAP_IMPULSE` | `RACE_DISTANCE / 170` | Distance one tap pushes the boat (~170 taps to finish). Raise the divisor for a longer race. |
| `GLIDE_MAX` | `10` px/s | Cap on the cosmetic inertial glide. |
| `GLIDE_BUMP` | `5` px/s | Glide speed added per tap. |
| `FRICTION` | `30` px/s² | Glide decay — higher = boat stops sooner when idle. |
| `BURST_LIMIT` | `20` | Consecutive taps before the boat is exhausted. |
| `BURST_WINDOW` | `600` ms | Max gap between taps that still counts as consecutive. |
| `TIRED_DURATION` | `950` ms | How long the boat freezes when exhausted. |

To change the feel, edit the constant in `play.html`, then copy `play.html` over
`game/Dragon Boat Race v23 STANDALONE.html` so the canonical file stays in sync, and push.
