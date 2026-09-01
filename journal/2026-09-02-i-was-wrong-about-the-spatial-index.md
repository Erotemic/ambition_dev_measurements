# ⛔⛔ I was wrong about the spatial index

**2026-09-01, correcting the entry published two hours earlier.**
`2026-09-02-the-scan-is-the-problem-not-the-view.md` concluded "the spatial index
is the whole win". **The scan is 8% of the cost.** The index is worth 0.032
ms/tick and the entry's prescription should not be acted on.

## What the earlier entry did wrong

It measured two counts — `offered = n-1`, `kept ≈ 14.4` — and *inferred* from
them which term dominated the TIME. It never measured the time. A count that
grows quadratically is not automatically the expensive one.

## The decomposition, measured

Three builds at 130 bodies, `Decision.Decide` ms/tick:

```text
shipped                                          0.415
scan every peer + viewport test, build NOTHING   0.130
no peers at all (probe B, earlier)               0.098
```

```text
the scan over discarded candidates   0.130 - 0.098 =  0.032    8%
building and using the ~14 kept      0.415 - 0.130 =  0.285   69%
brain tick, self view, projectiles, collision world  0.098    24%
```

**A spatial index removes the 0.032.** It does not touch the 0.285, because those
peers are kept and must be built either way. 1,873 `PerceivedActor` constructions
per tick at **152 ns each** is where the money is.

## And `kept` SATURATES

```text
   n     kept
   8     5.00
  17     5.88
  33    11.61
  65    14.63
 130    14.41      <- flat
```

So `builds = n × kept(n)` grows **linearly** once the room saturates, and the
earlier framing of an O(n²) construction cost was wrong twice over: the scan is
quadratic but cheap, and the construction is expensive but linear.

⇒ **The hall is asymptotically O(n) in perception construction.** 200 or 400
actors cost proportionally, not quadratically.

## ⚠ AN OPEN DISCREPANCY, STATED RATHER THAN SMOOTHED

The terms above do not reproduce the measured slope, and I cannot yet say why:

```text
        builds     Decide
17→33   x3.83      x3.78     agrees
33→65   x2.48      x2.35     agrees
65→130  x1.97      x3.17     DOES NOT AGREE
```

Past saturation, builds grow 1.97× and `Decide` grows 3.17×. The scan grows 4×
but is only 8% of the total, so it cannot account for the gap. **Something
superlinear between 65 and 130 bodies is unattributed.**

Candidates, none measured: the peer array crossing a cache level (130 × ~150 B ≈
19.5 KB against a 32 KB L1, times each actor's own working set); the brain-tick
term not being linear; `believed_target` and the brain's own reads of
`view.actors` scaling differently from construction.

⛔ **Until that is named, no number here supports a design decision about 200+
actors.** The saturation result says the shape *should* be linear; the slope says
it is not. One of those is wrong and it is not yet known which.

## What this retires

- ⛔ "The spatial index is the whole win" — **withdrawn**. It is worth 8%.
- ⛔ "The attention budget has nothing to win because the viewport delivers ~14" —
  still true as far as it goes, and now beside the point: ~14 is the number that
  is *expensive to build*, not the number that is cheap to have.

## The next measurement

Instructions retired at 65 and 130, interleaved, on the shipped build. A
wall-clock slope cannot separate "more instructions" from "the same instructions
missing cache more often", and that distinction is the whole of the open
discrepancy above.
