# `WorldMemory::update` scanned the whole view, per remembered actor

**2026-09-01.** At crowd density it was **the single largest symbol in the
profile — 12.89%**. One line:

```rust
for (id, mem) in self.actors.iter_mut() {
    if !view.actors.iter().any(|a| &a.id == id) {
```

O(remembered × seen), with a `String` comparison inside, and **both terms are the
crowd size**. At 113 perceived peers that is ~12,800 string compares per actor
per tick — **~1.6 million per tick** across 130 actors.

## The measurement

One binary, both membership tests selected at runtime, three interleaved reps,
`hall_of_characters` at 130 bodies with the tactical extent widened 4x so
`kept` ≈ 113:

```text
rep    linear scan    sorted keys
 1        3.2587         2.0680
 2        3.1150         2.0800
 3        3.2047         2.1277
mean      3.193          2.092      -34.5%
```

`Decision.Decide` ms/tick. Spread 4.5% and 2.9%; the arms do not overlap.

⭐ **AND IT IS WORTH NOTHING TODAY.** At the hall's real density (`kept` ≈ 14)
the same change measures 0.2613 → 0.2423, inside the noise — 14 × 14 compares is
nothing. This is a fix for the regime the room is heading toward, not the one it
is in, and the profile only showed it because the extent was widened to look
there.

## The fix

Collect the in-view ids **once** into a sorted slice of borrowed `&str` and
binary-search it. No `String` is cloned — the ids are borrowed from the view.

⚠ **SORTED, NOT HASHED, AND THAT IS DELIBERATE.** ADR 0023 keeps this map on
`BTreeMap` because `last_known_hostile` breaks confidence ties by iteration
order, and under `RandomState` an enemy chases a different player on every run of
the same binary. Only membership is asked here, so a `HashSet` would be correct
today — and would put process-seeded iteration back inside the one function
deliberately kept free of it, where the next change might read it. A sorted slice
cannot regress that way.

## The guard was already there

`memory_retains_target_after_it_leaves_view`, `memory_forgets_after_long_absence`
and `a_tie_between_two_remembered_hostiles_breaks_the_same_way_every_time` all
fail when the membership test is inverted — verified by inverting it. The
semantics were pinned before this change and did not need extending.

## ⛔ The fourth O(n²) named this campaign, and the first that was real

```text
BodyContactSnapshot::field_for    dormant, contact_empty=true
select_actor_targets              measured slope 1.03
the peer scan in build_world_view measured 8% of the phase
WorldMemory::update               12.89% of the whole process    <- this one
```

The first three were proposed from source reading and died on measurement. This
one was never proposed by anyone; it was found by pointing `perf` at a regime
nobody had profiled, and it cost a third of the phase in that regime.
