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

## Addendum: decomposed, and the caveat is closed

The three-arm probe, run at both extents off one binary:

```text
       kept   builds+use     scan   brain+rest   build share   µs/build
 1x    14.4       0.1500   0.0443       0.0410          64%       10.4
 4x   113.2       2.7053   0.0402       0.0715          96%       23.9

kept ×7.85    builds ×18.0    brain+rest ×1.7    scan ×0.91
```

⇒ **Crowding is not brains thinking harder.** The peer-independent brain term
grows ×1.7 while the peer-dependent term grows ×18, and at 4x it is **96%** of
the phase. The caveat the entry above left open — that raising the extent makes
brains see more, so some growth might be cognition — is closed: whatever the
brains do with a bigger view, it is 4% of the cost.

⭐ **The scan is FLAT (×0.91).** It walks the same 129 peers whatever the extent;
only how many pass the filter changes. That is a second, independent
confirmation that the scan is not the expensive half — the first was the 8%
decomposition at fixed extent.

## ⭐⭐ AND THE CONSTRUCTION TERM IS SUPERLINEAR IN `kept`

```text
builds ×18.0 against kept ×7.85   ->   slope 1.40
per-build cost   10.4 µs  ->  23.9 µs
```

Building 113 `PerceivedActor`s per actor costs **more than twice as much each**
as building 14. That is the same signature measured independently earlier at
fixed extent — 86 ns → 160 ns per build as population doubled — and the same
explanation fits: ~13 KB written per actor per tick instead of ~1.7 KB, touched
once and discarded.

⇒ **A bounded K does not merely cap a linear cost. It caps a term that grows
faster than K.** Total `Decide` reads slope 1.13 only because the flat terms
dilute it at low `kept`; the part a budget actually removes is 1.40.

That is a stronger argument for the attention budget than the entry above made,
and it comes from the same two runs.
