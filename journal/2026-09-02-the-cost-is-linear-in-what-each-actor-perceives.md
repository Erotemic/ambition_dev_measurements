# The cost is linear in what each actor perceives

**2026-09-01.** Driving the tactical extent instead of the population puts the
same 130 bodies through the **density** regime, and `Decide` tracks `kept`
almost exactly linearly:

```text
extent   mean kept   Decide ms/tick   µs per kept
  1x        14.41         0.2613          18.1
  2x        56.10         1.4110          25.2
  3x        93.93         2.1430          22.8
  4x       113.15         2.6553          23.5

slope over the whole range   1.13     (1.0 = linear in kept)
slope over the crowded half  0.90
```

**Cost per perceived peer is flat at ~23 µs.** Crowding does not make perception
super-linear; it just gives every actor more to perceive.

## Why this was measured this way

The room authors 129 `NpcSpawn` placements and the population cap can only
REMOVE, so 200 bodies is not reachable in this workload. Duplicating placements
would raise the count, but at offset positions it would also change the room's
crowding — and crowding is exactly the variable under test, so the duplicate
would confound the thing it was built to measure.

⭐ **The tactical extent is the same experiment without that confound.** Same
cast, same geometry, same brains; only how far each body sees. `kept` runs from
14 to 113 of a possible 129 — most of the range a crowded room could ever
produce.

## ⛔ THIS CORRECTS "the attention budget has nothing to win"

An earlier entry priced the three pieces of
`bounded-perception-and-attention.md` and concluded the attention budget would
impose a K the viewport already delivers. That is true **at the hall's current
density and only there.**

```text
kept  14.4  ->  Decide 0.26 ms/tick     today
kept   113  ->  Decide 2.66 ms/tick     a genuinely crowded room, 10x
```

⇒ The attention budget is worth **10x** in the regime it was designed for. It has
nothing to win today because the hall is sparse, not because the idea is wrong,
and those are completely different statements. The earlier entry made the first
sound like the second.

⭐ And the linearity is the design's own premise, confirmed: **cap what an actor
perceives and you cap the cost**, because cost per perceived peer does not rise
with crowding. A budget of K exact actors buys a hard ceiling, not a discount.

## ⚠ What this does not separate

Raising the extent makes brains *see* more, so some of the growth could be brains
deciding harder rather than perception building more. The earlier probe put the
brain tick at 0.098 ms/tick when peers were removed entirely; even if brain cost
scaled fully with `kept`, that is ~0.8 ms of the 2.66 — construction still
dominates. **Not decomposed here**, and the cheap way to decompose it is the same
three-arm probe used before, run at `4x`.

## For the design

The acceptance test in `bounded-perception-and-attention.md` asks for 200 tactical
fighters. This says what to hold constant while doing it: **`kept`, not the
population.** A room of 200 with each fighter attending to 16 costs about what
130 attending to 14 costs today; a room of 200 where everyone sees everyone is
the 10x case, and it arrives through density long before it arrives through
headcount.
