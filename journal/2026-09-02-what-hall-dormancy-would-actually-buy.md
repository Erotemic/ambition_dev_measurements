# What hall dormancy would actually buy

**2026-09-01.** The 2026-08-08 maintainer decision authorises making most of the
hall cast dormant, and attaches a condition the row states plainly:

> *"especially if that reduces lag" is a condition, not decoration: measure the
> frame cost before and after, and if it does not move, say so rather than
> banking an unmeasured win.*

The number the condition asks for. **Upper bound: 8–18% of the frame**, and the
uncertainty is real rather than rhetorical.

## The measurement

`capture_scene hall_of_characters --fit-room`, offscreen GPU so the render stack
runs, population 3 vs 130 by `AMBITION_ACTOR_POPULATION_CAP`, after today's
perception and sweep fixes:

```text
phase              3 bodies      130    delta   share of growth
PreUpdate             1.822    3.586   +1.764        62%
Update                1.838    2.577   +0.739        26%
PostUpdate            1.279    1.575   +0.296        10%
outside               1.017    1.045   +0.028         1%
RunFixedMainLoop      0.996    0.881   -0.115        -4%

frame p50              6.99     9.97   +2.98
```

Dormancy removes **simulation** work. It does not remove the entities, their
sprites, their transforms, or anything `Update`/`PostUpdate` does with them — a
dormant statue still draws.

```text
sim-side growth   +1.83 ms   =  18% of the 130-body frame
```

## ⚠ WHY THAT IS A RANGE AND NOT A NUMBER

Two instruments disagree about how much of the cast's cost is simulation, and
the disagreement is understood rather than mysterious:

```text
phase census (this table)   sim is 62% of the cast's cost
perf, by shared object      the game's own code is 34%, rendering 44%
```

The census attributes wall time between markers, and **says so on its own row**
(`untrustworthy=render_blocking`). Under a software rasteriser the rasterisation
is CPU work inside the render sub-app, bracketed by main-schedule markers, so it
lands in `PreUpdate` and reads as simulation. `perf` attributes by code and
cannot make that mistake.

⇒ The honest upper bound is **8–18%** of a ~10 ms offscreen frame, and on real
hardware — where the rasteriser's share collapses — it sits toward the top of
that range rather than the bottom.

⛔ **THIS IS NOT A RECOMMENDATION.** The decision is Jon's and it is already
made; this entry supplies the number its condition asked for and nothing else.

## Two facts worth having beside it

⭐ **The cast is much cheaper than it was this morning.** The same 127 actors cost
`Decide` 0.852 ms/tick at the start of today and 0.234 now, and `Integrate` 0.477
→ 0.223. Whatever dormancy is worth, it is worth roughly half what it would have
been measured at twelve hours ago — the condition is being evaluated against a
moving number, and this is the current one.

⚠ **And the hall is the only workload that finds these defects.** Every
simulation defect fixed today — the peer clone, the GJK sweep, `WorldMemory`'s
quadratic — was found by profiling 130 awake actors. A dormant cast measures a
room nobody is stressing. That is a cost of the decision, not an argument against
it, and it is a cost that can be paid separately: a dormancy policy with an
all-awake mode retained for measurement keeps both.
