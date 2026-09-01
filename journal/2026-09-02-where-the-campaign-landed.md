# Where the campaign landed

**2026-09-01.** Same room, same knob, same build settings as the first curve.
The headless hall frame at 130 bodies is **3.07 → 1.94 ms p50**, and the profile
is now flat: nothing above 2.4%.

## The curve, before and after

```text
metric        slope before  slope after   @130 before   @130 after   cut
Decide             1.64         1.50         0.4123       0.2247     46%
Integrate          1.32         1.12         0.6365       0.2863     55%
frame p50          0.51         0.54         3.0700       1.9400     37%
```

Two changes did it: borrowing the peer snapshot instead of cloning the room per
actor, and sweeping axis-aligned boxes with the closed form instead of parry's
generic GJK. Neither is an architecture; both are the same defect in different
clothes — **paying a general-purpose price for a special case.**

⭐ `Integrate`'s slope fell 1.32 → **1.12**. The superlinearity was never a
missing broadphase; it was a per-sweep constant large enough to look like one.

## Does 200 characters work?

Projected from the measured post-fix slopes:

```text
n = 200      Decide      0.43 ms/tick
             Integrate   0.46
             frame p50   2.45 ms   -> ~400 fps headless
```

⚠ **A PROJECTION, NOT A MEASUREMENT.** The hall's authored cast is 130 and the
population cap can only remove actors, never add them. The honest statement is
that nothing in the measured range shows a term that would break at 200, and the
two superlinear terms that did exist have been cut and flattened.

⛔ And it is the **headless** frame. The windowed hall was last measured at ~9.4
ms with the simulation only ~25% of it, on a capture that was 88.5% CPU inside
the game binary — so presentation, not the GPU, owns the rest. That half has
never been attributed and cannot be from this host (no display). It is the
largest open question about 200 characters, and this entry does not answer it.

## The profile is flat now

```text
2.37%  Viewport::contains
2.07%  ron::parse::Parser::next_chars_while_from_len
1.69%  WorldMemory::update
1.53%  _mi_page_malloc_zero
1.36%  getenv
1.25%  slab_sweep                 (was 10.67% as parry cast_shapes)
1.08%  Aabb2d::strict_intersects
```

No dominant cost survives. That is the signal that the cheap structural wins are
done and further simulation work is diffuse — a different kind of campaign, and
one with a much worse ratio of effort to milliseconds.

⚠ `getenv` at 1.36% is real waste and unexplained. The obvious suspect
(`AMBITION_FIGHTER_TRACE`) is behind a `LazyLock` and reads once. Frame pointers
are absent from this build, so `--call-graph fp` returns nothing and a dwarf
report on one symbol does not finish. **Left open and named rather than guessed
at.**

## What the campaign actually taught

Six candidates were proposed by review or by me, from real source. Every one was
retired by measurement, and the fix that paid was named by none of them:

```text
O(n²) body-contact pairing        dormant           contact_empty=true
select_actor_targets, self-doc'd  slope 1.03        linear
Arc<str> actor identity           0.04 ms
"the spatial index is the whole win"  8%            withdrawn, mine
"the superlinear term is a count"     per-unit cost withdrawn, mine
peers_seen_by clone               0.38 ms           PAID
parry GJK on two AABBs            10.7% of process  PAID
```

**The term that grows fastest in COUNT was never the term that grows fastest in
TIME.** Twice the count-based reasoning was mine and published before measuring;
both were withdrawn in the same day.

And the instrument that found the winner was not the one built for the job. Five
rounds of phase-boundary census answered *where* with increasing precision and
could never answer *what* — `WorldPrepSet::Integrate` holds exactly one system,
so there was nothing left to split. `perf record` answered it in one run.
