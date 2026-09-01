# ⚠ The ledger caught a Smash regression nobody was looking for

**2026-09-01.** The first thing the frame-cost ledger was used for after being
repaired was a comparison it was not asked to make. `smash-match-2p`, headless,
no Tracy, same machine, same build config:

```text
2026-08-29   3.185  2.866  2.816    mean 2.956    range 2.816-3.185
2026-09-01   3.649  3.831  3.533    mean 3.671    range 3.533-3.831

              +24%          ranges DISJOINT (2.816-3.185 vs 3.533-3.831)
```

Three reps each side. **The ranges do not overlap**, which is what makes this a
finding rather than a wobble — the older group's own internal spread is 13%, and
a single new point at 3.649 would have proved nothing against it.

## ⛔ It is NOT today's simulation work

The obvious suspicion, since today replaced the movement sweep, is the geometry
change. It is not implicated:

```text
parry2d / gjk / cast_shapes / sweep_hit / slab_sweep
   -> ABSENT from the smash capture's symbol report entirely
```

The sweep does not appear at all — below the report's threshold — in a workload
that is two fighters colliding continuously, which is where a penetrating-start
fallback would show if it were hot. The top symbol is the **schedule executor at
2.00%**, and the profile is otherwise flat.

## The lead, named and not adopted

```text
systems   3,218  ->  3,405     +187,  +5.8%
frame      2.956 ->   3.671    +24%
```

The largest symbol being `SingleThreadedExecutor::run` is consistent with more
systems costing more to dispatch. ⛔ **But +5.8% of systems against +24% of frame
is not proportional**, so system count alone does not explain it, and a coherent
story is not a measurement — this campaign has retired six of those today.

⚠ The window is **three days and the whole Bevy 0.19 diagnostics program**, not
one change. Seven of the 187 new systems are the sim-phase census marks added
today, and they only register when `AMBITION_PROFILE_CENSUS` is set — which every
ledger row sets, on both sides of this comparison, so they are inside the
measurement on the new side only.

## Why this entry exists

⭐ **No journal would have caught this.** Every hall number measured today came
from driving the binary directly, and none of it touches Smash. The regression is
in a workload nobody profiled, found because the ledger groups by everything that
changes a frame time and had three comparable rows waiting from three days ago.

That is the argument for the ledger, made by the ledger, on its first real use —
and it arrived alongside three defects IN the ledger found the same hour.

## Next

Bisect `31cd8218b1ed..88f4ca40e208` on the Smash capture, or accept it as the
cost of the diagnostics program and re-baseline deliberately. Either is a
decision; leaving a 24% regression unattributed in a workload with a ledger row
is not.
