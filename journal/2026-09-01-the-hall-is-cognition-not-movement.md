# The hall is cognition, not movement

**2026-09-01.** The bucket that carried 1.2 ms/tick was never `BeforeIntegrate`.
Split correctly, **96% of it is actors deciding what to do** and 4% is the set
whose name it wore.

## The measurement

`hall_of_characters`, headless, `bodies=130` confirmed by the population census,
`--headless-ticks 1800`. **No Tracy** — a plain `profiling` build, no
`--features profile`, so these are absolute numbers rather than ratios under a
4.3x observer tax.

Six steady census samples (the first, `ticks=1`, is startup and excluded):

```text
                              min     max     mean
WorldPrep.ActorDecision      0.923   0.987   0.958   ms/tick
WorldPrep.Integrate          0.585   0.615   0.599
WorldPrep.BeforeIntegrate    0.036   0.038   0.037
WorldPrep.ContactDamage      0.013   0.014   0.014
```

`ActorDecision` spans `ActorDecisionSet::Targeting` through
`PlayerInputSet::BodyMode` — the six decision phases plus the two gate sets.
`BeforeIntegrate` is now only what its name says.

```text
old "WorldPrep.BeforeIntegrate"  =  0.958 + 0.037  =  0.995 ms/tick
                                     96.3%    3.7%
```

## What this retires

**The entire candidate list in the previous entry.** `tick_capture_holds`,
`steer_mount_from_rider`, `advance_moving_platforms`, `snapshot_body_contact`,
`sync_sprite_posed_bodies`, the `relativity2d` clocks — all of `BeforeIntegrate`,
all of it **0.037 ms/tick**, 3.7% of the bucket and 0.6% of the frame. Whichever
of them is slowest, it is not worth a day.

That includes the `sync_sprite_posed_bodies` double-`posed_body_geometry` call
flagged there. The arithmetic said microseconds and refused to adopt it as the
answer; the measurement says the whole SET it lives in is 37 µs. Still worth
deleting on its own merits. Still not the bug.

**And it retires the physics-engine question for a second, stronger reason.**
`Integrate` plus `ContactDamage` — everything avian would replace — is 0.613
ms/tick against 0.958 ms of cognition that avian does not touch at all.

## What it opens

**Which of the eight sets inside `ActorDecision` spends the 0.958 ms.** The two
standing hypotheses both live in there and want opposite fixes:

- `Targeting` — `select_actor_targets` documents itself `O(n²) in the all-actor
  case`. It builds the candidate population once, then scans every candidate per
  actor. A peaceful hall NPC with no grudge scans 129 bodies to prove none is a
  foe.
- `Decide` — `tick_actor_brains` builds `build_world_view` **unconditionally**
  ("built ALWAYS", `actors/update.rs:547`). `peers_seen_by` clones every other
  `PerceptionPeer`, each carrying an owned `String id`; `build_world_view` clones
  each id again into a `PerceivedActor`. At 130 actors that is ~16.8k heap
  allocations per tick, twice — arithmetic that reaches a millisecond, which is
  why this one is worth measuring and the sprite-geometry one was not.

Hall NPCs are authored `brain_override = "stand_still"`, and `tick_stand_still`
emits neutral. If `Decide` dominates, 130 actors are paying for a full tactical
world construction their brains never read.

⛔ **Neither is confirmed.** Both are source readings. Six more marks split
`ActorDecision` into its phases and that is the next measurement.

## Still owed

The scaling curve. `0.958 ms / 130 bodies` is 7.4 µs per actor per tick, which
*reads* like a fat linear constant, but one population cannot separate O(n) from
O(n²) and `select_actor_targets` is documented quadratic. The hall's cast is
authored in LDtk with no population knob, so the curve needs one built.
