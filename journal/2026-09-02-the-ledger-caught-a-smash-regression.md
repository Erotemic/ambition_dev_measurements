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

## Addendum: two candidates eliminated, the bisect still owed

### Not the geometry change

`parry2d`, `gjk`, `cast_shapes`, `sweep_hit` and `slab_sweep` are **absent from
the smash capture's symbol report entirely** — in a workload of two fighters
colliding continuously, which is exactly where the closed form's
penetrating-start fallback would show if it were hot.

### Not the diagnostics plugins

The phase decomposition pointed at `PostUpdate` (+0.260) and `Update` (+0.241) —
68% of the regression — and those are where the F1 diagnostics program landed. So
each of `FpsOverlayPlugin`, `SystemInformationDiagnosticsPlugin`,
`DiagnosticsOverlayPlugin` and `EcsDiagnosticsPlugin` was made skippable by
environment, one binary, and measured.

```text
smash_match_profile --ticks 2000, three interleaved reps

all-on    3.190  3.367  3.160   mean 3.239
all-off   3.140  3.123  3.110   mean 3.124   -3.5%
```

⇒ **~0.12 ms of a 3.2 ms frame.** Real, consistent in direction across all three
reps — and a seventh of the 24% it was proposed to explain.

⛔ **AND THE FIRST VERSION OF THIS TEST WAS RUN ON THE WRONG WORKLOAD.** One
single-shot pass over the *hall* suggested 1.987 → 1.72 and looked like a 13%
win; three interleaved reps put all-on at 1.725 and all-off at 1.758, overlapping
completely. The plugins cost nothing there because `run_headless` is the DIRECT
SANDBOX composition, which never adds `DevToolsPlugin` — its `PostUpdate` is 0.066
ms against Smash's 0.845. `smash_match_profile` uses
`build_visible_app(NoWindow, true)`, the shared host, which does.

⭐ Two lessons in one experiment: n=1 produced a 13% win that repping erased, and
a workload that does not compose the thing under test cannot falsify it either.

### What is left

```text
systems  3,218 -> 3,405    of which ~4 are the diagnostics plugins' own
                            registrations and 7 are today's census marks
window   31cd8218b1ed..88f4ca40e208   three days, the whole 0.19 program
```

The bisect is owed. It is ~6 steps over that range with a rebuild each, which is
hours rather than minutes — but two dead ends are now closed, and neither would
have been closed by reading.
