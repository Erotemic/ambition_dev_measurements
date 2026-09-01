# Two superlinearities, one cause each — and I had attributed both to the wrong one

**2026-09-01.** The `WorldMemory` fixes removed the **density** superlinearity
entirely and left the **population** one untouched. That separates two effects I
had been treating as one, and corrects an earlier attribution.

## Density, at fixed population — solved

```text
   kept    before     after    cut
   14.4    0.2613    0.2367     9%
   56.1    1.4110    1.1160    21%
   93.9    2.1430    1.7750    17%
  113.2    2.6553    1.9947    25%

slope in kept:  1.13 -> 1.03
```

**Now essentially exactly linear in what each actor perceives.** An earlier entry
decomposed this and reported the construction term at slope **1.40**, attributing
it to per-build cache cost. That was wrong: the three-arm probe's "builds + use"
bucket contains `WorldMemory::update` — the `scan` arm empties `view.actors`, so
the quadratic membership test collapsed along with the builds and hid inside the
same number. **The 1.40 was `WorldMemory`'s O(remembered × seen), and it is gone.**

## Population, at fixed extent — still open, and NOT WorldMemory

```text
 65 -> 130 bodies    0.1237 -> 0.3900   slope 1.66   before
                     0.0810 -> 0.2337   slope 1.53   after
```

Absolute cost fell 35-40%, but the shape barely moved. And a count rules
`WorldMemory` out as the cause:

```text
population 65    mean_remembered 14.6    mean_seen 14.6
population 130   mean_remembered 14.4    mean_seen 14.4
```

**Memory size is constant in population** — so its per-call work is constant, and
it cannot produce a term that grows faster than the actor count. The residual
slope of 1.53 belongs to something else, and the per-build cost measurement from
the population sweep (86 ns → 160 ns) still stands as the leading explanation
there.

⇒ **Two experiments, two different causes**, and conflating them is what produced
the wrong attribution:

```text
more CROWDING at fixed population   -> was WorldMemory's quadratic   FIXED
more POPULATION at fixed crowding   -> per-unit cost rises           OPEN, slope 1.53
```

## ⚠ The hall UNDERSTATES WorldMemory, and by construction

`mean_remembered == mean_seen`, exactly, at both populations. Memory is holding
nothing that is not currently visible — which looks like the retention feature not
working, and is not: the hall's cast is `stand_still` and every actor's visible
set never changes, so nothing ever leaves view to be remembered.

⛔ **In a room where bodies MOVE, `remembered` exceeds `seen`** — that is the
entire point of the type — and `WorldMemory::update`'s per-call work is linear in
`remembered`. So the fixes measured here are a **lower bound** on what they are
worth in a real fight, and the hall cannot show the difference.

The measurement that would: the same extent sweep in a room with a moving cast,
watching `mean_remembered / mean_seen` climb above 1.

## Addendum: the population term, decomposed cleanly

Three arms, one binary, `WorldMemory` already fixed so it is no longer hiding
inside the build bucket:

```text
term            65       130   growth   share of growth
builds+use   0.0560    0.1687   3.01x        76%
scan         0.0055    0.0230   4.18x        12%
fixed        0.0200    0.0390   1.95x        13%
```

Every term now has a confirmed shape:

```text
fixed        x1.95   against population x2.00   -> LINEAR, as expected
scan         x4.18   against a predicted x4.03  -> EXACTLY QUADRATIC
builds+use   x3.01   against x1.97 builds       -> per-build cost x1.53
```

⭐ **THE SCAN IS CONFIRMED QUADRATIC BY PREDICTION, NOT BY FITTING.** 65×64 → 130×129
is ×4.03 of work; the measurement is ×4.18. That is the strongest form this
campaign has produced for any shape claim — a number predicted from the loop
bounds before the run, then met.

## ⛔ Which changes what the spatial index is worth

An earlier entry priced it at **8%** and said "not the first thing to build". True
at 130. But it is the only term growing **quadratically**, so its share grows with
population while everything else's shrinks:

```text
              scan share of Decide
  65 bodies          7%
 130 bodies         10%
 260 bodies         ~25%   (extrapolated from a CONFIRMED n², not a fitted slope)
```

⇒ The index is not urgent, and it is the one piece whose value **compounds**. The
attention budget caps a linear term; the index caps the quadratic one. They are
not competing for the same milliseconds.

## The residual is per-unit cost, and its mechanism is still unconfirmed

`builds+use` grows ×3.01 while the number of builds grows ×1.97 — per-build cost
rises ×1.53 with population, at a **constant 14.4 builds per actor**. Same
signature as the pre-fix measurement (×1.85), now isolated from `WorldMemory`.

⚠ **Cache is the leading explanation and it is NOT established.** An earlier
`perf stat` over the whole process put IPC essentially flat (1.29 → 1.27), which
argues against stalls — but that was process-wide, dominated by startup and
rendering, and cannot speak for one phase. Separating "more instructions" from
"the same instructions stalling" inside `Decide` needs per-phase counters this
instrument does not have.

⇒ Recorded as an open shape, not a diagnosis. At 130 bodies `Decide` is 0.23
ms/tick and projects to ~0.45 ms at 200, so nothing here is urgent enough to
justify guessing.
