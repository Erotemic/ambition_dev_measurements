# The day's arithmetic

**2026-09-01.** `hall_of_characters`, headless, 130 bodies, no Tracy, same room
and same knob throughout.

```text                     start of day     now      cut
frame p50                      3.07 ms      1.78 ms   -42%
WorldPrep.Integrate            0.637        0.223     -65%
ActorDecision.Decide           0.852        0.234     -73%
```

Four changes, and none of them was an architecture:

```text
borrow the peer snapshot        clone of the room, per actor, per tick, to
                                exclude one row
closed-form AABB sweep          parry2d's generic GJK on two axis-aligned
                                boxes -- 10.7% of the whole process
WorldMemory membership          a linear scan of the view per remembered actor,
                                O(remembered x seen), both terms the crowd size
WorldMemory refresh             a String key cloned per perceived peer per tick
                                against a key already present
```

Three of the four are the same defect in different clothes: **paying a
general-purpose price for a special case.** The fourth is a quadratic nobody had
looked for.

## The profile is flat now

```text
3.08%  WorldMemory::update
2.60%  _mi_page_malloc_zero
2.13%  (libc, IFUNC memcpy family)
1.98%  Viewport::contains
1.95%  core::iter Map::next
```

Nothing above 3.1%. At the start of the day one symbol was **10.7%**.

## What the campaign taught, in one line each

⭐ **A phase census answers WHERE; only a symbol profiler answers WHAT.** Five
rounds of boundary marks localised the cost to one system and could go no
further, because `WorldPrepSet::Integrate` holds exactly one. `perf` found the
biggest win of the day in one run.

⛔ **The term that grows fastest in COUNT was never the term that grows fastest
in TIME.** Every O(n²) proposed from source reading measured negligible; the scan
that IS exactly quadratic (×4.18 against a predicted ×4.03) is 12% of the growth.

⛔ **Compare arms in ONE BINARY.** Two of this campaign's wrong conclusions came
from subtracting a number taken from a build two fixes older.

⭐ **Poison every guard, including the ones you did not write.** Poisoning the
`WorldMemory` refresh revealed that *nothing at all* tested a visible actor being
refreshed — 395 tests green with the loop turned into a no-op.

⚠ **Say which half of a measurement is trustworthy.** The census warns about
itself under rendering; I used its differential correctly and then quoted its
split anyway, and had to withdraw a 66%.

## Still open, honestly

```text
per-build cost rises with population       slope 1.53, mechanism unconfirmed
windowed frame on real hardware            no display on this host
weak-GPU transparent overdraw ~5.3x        separate campaign, needs a GPU
the hall is not in the perf-history ledger a capture needs a full profiling
                                           rebuild and the disk is at 94%
```

## ⛔⛔ A METHODS ERROR THAT UNDERSTATES EVERY ABSOLUTE NUMBER ABOVE

`[census] sim_phases` emits one row per second **plus a `ticks=1` startup row
whose every phase reads 0.000**. Averaging "the last three windows" of a SHORT
run averages that zero in.

At 130 bodies, every steady window reads 0.332–0.352:

```text
t=0.272  ticks=1    Decide=0.000     <- startup
t=1.273  ticks=500  Decide=0.341
t=2.274  ticks=612  Decide=0.332
t=3.275  ticks=553  Decide=0.345
...      steady at ~0.341 for eleven windows
```

and a 1200-tick run produces only about three:

```text
(0.000 + 0.341 + 0.332) / 3 = 0.224
```

**0.224 is exactly the number published as `Decide` at 130 bodies.** The true
steady value is **0.341**.

⚠ **AND AN EARLIER ENTRY IN THIS FILE GOT IT BACKWARDS.** Seeing `tail -1` report
0.341 against a three-window mean of 0.234, it concluded the single window was
the anomaly and the average was right. The opposite was true, and it was written
up as a lesson about trusting the stated method. The stated method was the bug.

## What this does and does not invalidate

```text
ABSOLUTE values from 1200-tick runs   UNDERSTATED, by up to a third
                                      (one zero among three windows)

A/B deltas and RATIOS                 largely survive: both arms carry the same
                                      startup zero. ⚠ But NOT exactly — a faster
                                      arm completes more windows, so it is
                                      diluted LESS, which makes every measured
                                      improvement CONSERVATIVE rather than
                                      inflated.

Anything read from a single steady window   unaffected.
```

⇒ The direction of every fix stands, and each is understated rather than
overstated. The absolute per-phase numbers from short runs do not.

## The corrected curve, 6000 ticks, startup window excluded

```text
bodies   Decide  Integrate  frame p50   µs/body (Decide)
     9   0.0113     0.0254      0.578        1.3
    18   0.0251     0.0446      0.670        1.4
    34   0.0630     0.0733      0.795        1.9
    66   0.1583     0.1310      1.055        2.4
   130   0.3410     0.2521      1.662        2.6

slopes 9 -> 130:   Decide 1.27    Integrate 0.86    frame 0.40
```

Two reps per point, agreeing to within 3%; every point has at least four steady
windows.

⭐ **`Integrate` IS SUBLINEAR — 0.86.** Cost per body FALLS as the room fills,
which is what per-tick amortisation looks like: the collision world is rebuilt
once per tick regardless of how many bodies then sweep against it.

⭐ And the frame is `n^0.40`: **130 bodies cost 2.9x what 9 do**, not 14x.

⚠ These slopes are LOWER than the ones published earlier today (1.50 / 1.03 /
0.47) — correcting the sampling made the system look better, because the bias
fell hardest on the low-population points that anchor the left end of the fit.
