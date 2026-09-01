# Borrowing the peer snapshot

**2026-09-01.** `peers_seen_by` is gone. `WorldPrep.Decision.Decide` falls from
**0.852 to 0.415 ms/tick** at 130 bodies, and the headless sim runs **24% more
ticks per wall-second**.

## What it was

Every actor, every tick, asked for "all the peers but me" and got an owned
`Vec<PerceptionPeer>` — a full clone of the other 129 rows, each carrying an
owned `String id`. `build_world_view` then walked that copy and cloned each id
*again* into a `PerceivedActor`.

The entire purpose of the copy was to exclude **one row**.

## What it is

`PerceivedWorld::peers()` hands out the shared snapshot, borrowed. Self is
excluded by one comparison inside `build_world_view` against a new
`PerceptionBody::viewer`, taken from the body's own peer row.

## The measurement

`hall_of_characters`, headless, `bodies=130`, no Tracy, `profiling` build. Five
steady census samples each.

```text
                        before    after    change
Decision.Decide          0.852    0.415    -51%
  ÷ Integrate             1.285    0.650    -49%
sim ticks / wall-second     257      318    +24%
Decision.Targeting       0.059    0.055    (unaffected, as expected)
Integrate                0.663    0.638
```

⭐ **THREE INSTRUMENTS, AND THE THIRD IS A COUNT.** Per-tick milliseconds are a
timing on a machine whose load drifts — `Integrate` moved 4% between runs on its
own. The ratio to `Integrate` controls for that. But **ticks per wall-second**
is a count, it needs no baseline, and it moved the right way by a quarter. A
timing that improves while the tick rate does not would mean the work moved, not
that it left.

## It matches the probes, and slightly beats them

The two probes predicted this exactly:

```text
baseline                                 0.861
probe A  list emptied, clone still run   0.482   -> the view walk is ~0.38
probe B  clone removed as well           0.098   -> the clone is ~0.38
predicted for "remove the clone only"    ~0.48
measured                                  0.415
```

The extra ~0.06 is not mysterious and is worth naming rather than rounding away:
the walk now iterates a slice that is already hot, instead of one freshly
allocated and copied into a moment earlier.

## What this is NOT

⛔ **This is not the perception architecture.** It removes a copy; it does not
bound anything. Every actor still receives a `PerceivedActor` for all 129 others,
and that remains **O(n²) by construction** — the remaining 0.415 ms is mostly
that walk. See `docs/planning/engine/bounded-perception-and-attention.md`.

⛔ **And it is not "make `stand_still` cheap".** Nothing here inspects a brain.
A room of genuinely tactical fighters gets the identical saving, which is the
property that made this worth doing before the architecture rather than after.

## The guard

`a_viewer_is_not_its_own_peer` is the whole contract the deleted clone was
enforcing. Get it wrong and a body perceives itself as another actor:
`nearest_hostile` can return the viewer, a grudge-holder is hostile to itself,
and every distance query has a zero in it. **Nothing panics** — the brains simply
act on a world containing a duplicate of themselves.

Poisoned by deleting the filter; the exclusion arm failed and the premise guard
(a viewer with no row excludes nobody) stayed green, which is what tells the two
apart. 1206/1206 monolith lib tests pass.

## Addendum: the local seam is now exhausted

A third probe removed the remaining `String` clone in the view walk
(`id: p.id.clone()` → `String::new()`), the one an `Arc<str>` or a cheap semantic
id would eliminate for real:

```text
after the fix                 0.415 ms/tick
probe C, id clone removed     0.374          -10%
```

**0.04 ms.** ⛔ So cheap actor identity is *not* worth the churn — and the
suggestion to reach for `Arc<str>` next is the third candidate this campaign has
killed by measuring rather than reading, after the O(n²) body-contact pairing and
the O(n²) `select_actor_targets`.

That 0.04 also explains the earlier asymmetry. Both clones are ~16.8k `String`
allocations per tick, but `peers_seen_by` cost 0.38 ms and this costs 0.04. The
strings were never the expensive part: `peers_seen_by` also memcpy'd the whole
~150-byte `PerceptionPeer` struct 16.8k times into a freshly allocated `Vec` per
actor. It was the **bulk copy**, not the heap traffic.

⇒ The remaining 0.374 ms is the walk itself — building 129 `PerceivedActor`s per
actor, 16.8k structs per tick, with a hostility decision each. There is no local
change left that touches it. It is **O(n²) by construction**, and the next win is
the bounded representation, not a smaller constant.
