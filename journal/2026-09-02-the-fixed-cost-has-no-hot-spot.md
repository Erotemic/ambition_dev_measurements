# The fixed cost has no hot spot

**2026-09-01.** The ~6.9 ms that a hall frame costs with **three** bodies in it
is not one thing. Half of it is the software rasteriser this host is stuck with;
the game's own share takes **197 symbols to reach half**, and the largest single
one is **0.99%**.

## Concentration

`capture_scene hall_of_characters --fit-room --warmup 2500`, population capped to
2, `perf record` over the whole run so startup is diluted by 2500 rendered
frames.

```text
by shared object
  35.96%  [JIT]              llvmpipe's compiled shaders
  30.35%  capture_scene      the game's own code
  19.59%  libvulkan_lvp.so   Lavapipe
  10.15%  kernel
   3.84%  libc
```

⇒ **55.5% is software rasterisation** and would be near-zero on real hardware.

Inside the game's own code, 5020 distinct symbols were sampled:

```text
 25% of the game's own time takes    28 symbols
 50%                                197
 75%                                751
 90%                               1103

largest single symbol: 0.99%   (_mi_page_malloc_zero — the allocator)
```

## What that rules out

⛔ **There is no fixed-cost optimisation to find.** A campaign aimed at "make the
baseline frame faster" would be looking for a hot spot that does not exist:
halving the single largest symbol in the game buys **0.5% of a frame**, and
reaching half the cost means touching 197 of them.

This is the measured version of a claim `performance-and-iteration.md` has made
from impression since it was written — *"the schedule is broad: hundreds of
individually small systems"*. It is now a number.

⭐ **The only levers on a diffuse cost are structural**: run fewer systems, hold
fewer entities, do less per frame. Not "optimise the hot one." That is a
composition question, not a profiling one, and it should be opened as such or not
at all.

## What it does NOT rule out

⚠ The 55.5% rasteriser share is this host's, not a player's. Nothing here says
anything about real-GPU rendering cost, and the weak-GPU overdraw work
(~5.3x transparent fragments, measured separately) is untouched by this and
remains the live rendering lead.

⚠ Diffuse at the SYMBOL level is not diffuse at the SYSTEM level. 197 symbols may
sit inside a handful of Bevy systems that a schedule-level instrument would name
in one row. The per-system profiler that would answer this does not exist in
0.19 — see `reference_a_phase_census_measures_the_gpu` — so this entry stops at
"no symbol dominates" and does not claim "no system dominates."

## Where the effort should go instead

The marginal cost — what each additional actor adds — is the number that caps
population, it is 66% simulation, and it responded to two changes today by
halving. That is where a profile still finds structure. The fixed cost does not
have any.
