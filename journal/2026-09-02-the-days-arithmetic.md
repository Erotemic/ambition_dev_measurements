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
