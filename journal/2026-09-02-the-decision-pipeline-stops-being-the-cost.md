# The decision pipeline stops being the cost

**2026-09-01.** The campaign opened on "the many-actor decision pipeline". It
closes with `Decide` out of the top of the table and `Integrate` in its place.

## What landed

ADR 0034 increment 1: a brain declares what perception it needs, and one that
needs none has no `WorldView` built and no belief maintained.

```text
authored hall (None x129)    Decide 0.353 -> 0.026   -93%,  wall time -32%
brutes (TargetBelief x129)   Decide 0.735 -> 0.748    +2%   the control
hall frame, same ledger group      1.9384 -> 1.5571 ms      -20%
                              p99   3.8586 -> 2.4732        -36%
```

Three things make this a result rather than a number.

**The probe predicted it.** A throwaway probe said 0.340 → 0.024 before any of
it was wired; the landed gate delivered 0.353 → 0.026. That is what licenses
trusting the probe method next time.

**The wall clock moved.** The census window shrank 32%, so the work left the
process instead of moving between buckets — the failure mode a boundary
instrument is most prone to.

**The control arm did not move.** Re-brained to melee brutes, which classify
`TargetBelief`, the same room moves +2%. The gate discriminates; it does not
merely delete work.

## The curve it leaves behind

```text
bodies                         2       16       64      130
WorldPrep.Integrate        0.014    0.039    0.129    0.249   <- now the largest
Combat                     0.045    0.051    0.071    0.093
Decision.Targeting         0.004    0.006    0.015    0.030
WorldPrep.Decision.Decide  0.003    0.005    0.012    0.023   <- was 0.331
```

`Integrate` is linear — 2.8x / 3.3x / 1.93x against population steps of 8x / 4x
/ 2.03x — so it is a per-body constant, not an architecture. `Combat` is 0.093 at
130 and 0.045 at TWO, so nearly half of it is fixed cost a population sweep
cannot name.

## What this does NOT license

⛔ It does not say the bounded-attention architecture is unnecessary. The hall's
cast is `stand_still`; the room proves the SUPPLY of cognition can be gated, and
says nothing about a room of 130 actually-fighting tactical actors, which is
still the acceptance criterion and still needs a room authored for it. `None` was
correct for 129 of 129 here — that is a fact about this room, not a scaling law.

⛔ And it does not close `TacticalWorld`. `believed_target` derives the belief
FROM the view, so a `TargetBelief` brain still constructs a full one. Making that
road cheap is where the attention work belongs, and it is untouched.
