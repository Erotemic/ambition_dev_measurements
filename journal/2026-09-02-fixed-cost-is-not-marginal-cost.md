# Fixed cost is not marginal cost

**2026-09-01.** "The simulation is only ~25% of a windowed hall frame" is true
and was steering the campaign wrong. That 25% is a share of the **absolute**
frame, most of which is a fixed presentation cost that 130 actors do not change.
Of the **marginal** cost of those actors, the simulation is **66%**.

## How it was measured with no display

`capture_scene hall_of_characters player --fit-room` runs the room through the
real render stack offscreen (`VisibleRenderMode::OffscreenGpu`, software
rasteriser). `--fit-room` matters: with the default player focus most of the cast
is off-camera and culled, which would make presentation look flat for free.

Population varied by `AMBITION_ACTOR_POPULATION_CAP`, 400 warmup frames, 1280x720.

```text
phase              3 bodies      130    delta   share of growth
First                 0.084    0.090   +0.006          0%
PreUpdate             1.655    3.112   +1.457         60%
StateTransition       0.170    0.245   +0.075          3%
RunFixedMainLoop      0.770    0.850   +0.080          3%
Update                1.814    2.077   +0.263         11%
SpawnScene            0.178    0.177   -0.001          0%
PostUpdate            1.258    1.384   +0.126          5%
Last                  0.103    0.125   +0.022          1%
outside               0.902    1.308   +0.406         17%

frame p50              6.73     8.90   +2.17
```

```text
sim-side growth       +1.61   66%
presentation growth   +0.82   34%

fixed cost at 3 bodies   6.93 ms
marginal for 127 actors  2.43 ms
```

## The two numbers are different problems

```text
FIXED     ~6.9 ms with THREE bodies in the room. Presentation and rendering own
          almost all of it. This is what caps the baseline frame rate, and no
          amount of simulation work touches it.

MARGINAL  ~2.4 ms for 127 more actors, 66% of it simulation. This is what caps
          POPULATION, and it is the half this campaign has been cutting.
```

Reading the absolute 25% as "presentation is the problem" would have pointed the
next campaign at a cost that does not grow with the thing we want more of.

## ⚠ What this measurement cannot say

**It is a software rasteriser.** `outside` (+0.41, 17% of growth) is llvmpipe
doing on the CPU what a GPU does in parallel, so the render-side share here is an
**upper bound** — on real hardware the simulation's 66% is larger, not smaller.

**The census warns about itself**, and the warning is on the row:

```text
[census] phases_warning untrustworthy=render_blocking world_rendering=1 offscreen=1
  — attributes wall time between markers, so GPU blocking lands in whichever
    phase brackets it. Trust phase splits only from a run with no rendering.
```

⭐ That is why this entry reports a **differential** and not a split. Both arms
carry the same blocking contamination under identical render settings, so what
survives is which phases GROW — the question actually being asked.

⛔ Do not quote the absolute per-phase numbers above. They are one instrument
reading a workload it says it cannot attribute.

⚠ And `sprites=233` was identical in both arms, which is either a census of
registered sheets rather than instances, or something worth understanding. It is
NOT read as evidence here either way.

## What it means for 200 characters

The marginal cost is now ~2.4 ms per 127 actors with the sim's share already cut
by half today. Extending to 200 is a question about the marginal half, and the
fixed 6.9 ms — the baseline frame — is a separate campaign that was never about
population at all.
