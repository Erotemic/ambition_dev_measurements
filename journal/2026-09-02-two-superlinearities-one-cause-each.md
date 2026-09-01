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
