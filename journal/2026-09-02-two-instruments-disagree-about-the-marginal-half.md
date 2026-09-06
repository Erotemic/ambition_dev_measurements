# ⚠ Two instruments disagree about the marginal half

**2026-09-01, correcting the entry from an hour earlier.**
`fixed-cost-is-not-marginal-cost.md` reports that the simulation is **66%** of
what 130 actors add. A `perf` attribution of the same two runs puts the game's
own code at **34%** and rendering at **44%**. Both cannot be right, and the
earlier entry's number should not be quoted alone.

## The two answers

```text
BY PHASE CENSUS (wall time between markers)
  sim-side          66%     PreUpdate + StateTransition + RunFixedMainLoop
  presentation      34%     Update + PostUpdate + outside + ...

BY SHARED OBJECT (perf, absolute cycles, same two runs)
  game code         34%     30.6 Gcyc
  rendering         44%     [JIT] 24.1 + lavapipe 16.0
  kernel + libc     22%
```

## Which to believe, and why it is not a preference

⭐ **The census warns about itself, and this is the failure it names.** Its own
row says `untrustworthy=render_blocking — attributes wall time between markers,
so GPU blocking lands in whichever phase brackets it`. Under a software
rasteriser the rasterisation is not a GPU wait at all — it is CPU work inside
llvmpipe, running in the render sub-app, which is bracketed by main-schedule
markers. So it lands in `PreUpdate`/`Update` and is counted as simulation.

`perf` attributes by **code**, not by wall-clock window. `[JIT]` and
`libvulkan_lvp.so` cannot be mistaken for `PreUpdate` no matter when they run.

⇒ **The 34% is the better measurement of this run.** The 66% is the census
faithfully reporting a split it told me not to trust, and I quoted it anyway
after saying I would only use the differential — the differential was the right
call for *which phases grow*, and I extended it to *how much is simulation*,
which is a different question the same warning covers.

## ⛔ But neither answer is the one that matters

Both describe a **software rasteriser**. On real hardware the 44% rendering
share largely disappears, which pushes the game's share up — so the original
conclusion may still hold, as an **inference from a bound rather than a
measurement**:

```text
on this host      game 34%, rendering 44%
on real hardware  rendering shrinks toward zero; game share rises toward ~60-75%
                  UNMEASURED, and it needs a display
```

⇒ The claim that survives is the weaker, sturdier one already in the planning
doc: **the fixed cost and the marginal cost are different problems**, and the
marginal one is the one that caps population. The precise simulation share of it
is not settled here.

## The marginal profile is flat too

The other reason not to keep pulling this thread:

```text
top game symbol, 2 actors      _mi_page_malloc_zero   0.99%
top game symbol, 130 actors    _mi_page_malloc_zero   1.21%
next                           mi_free                0.49%
```

Nothing else above 0.4%. Marginal growth is spread across game code (34%),
rendering (44%), kernel and libc (22%) with no symbol dominating any of them.
`libc` grows fastest in relative terms (1.90x, mostly one IFUNC-dispatched
address that is almost certainly `memcpy`) and is still only 13% of the growth.

⇒ **Both halves are now diffuse.** After today's two fixes there is no third
structural win visible to this instrument on this workload. Further simulation
progress needs either a per-system profiler — which Bevy 0.19 does not have — or
a composition change: fewer systems, fewer entities, less per frame.

That is a reason to stop measuring this workload, not a reason to measure it
harder.
