# Where the hall spends its frame — localized to one set, not yet to a system

**Host: `toothbrush`** (i9-11900K, RTX 3090 — see `hardware.md`). 2026-09-01.

**The question:** the hall runs at ~88 fps with 130 characters and Jon's reading is
that this is far too slow — "games have shipped far higher fps with far more
complexity, and this is rust". Agreed. This entry is the localization, and it
stops honestly at the point where the evidence stops.

⛔⛔ **THIS IS NOT THE ASSET HITCH.** That is
[2026-08-31](2026-08-31-did-the-bevy-port-cost-frames.md) and
[2026-08-29](2026-08-29-the-desktop-hitch.md): a one-time burst when the room's
art arrives. This entry is about the STEADY STATE afterwards, which is a
different defect with a different owner.

---

## The chain, every step measured

From `desktop-timeline-run-20260831T210231Z` (windowed, `--features profile`),
comparing the settled hall (t=70–110, 130 bodies) against the same session before
it (t=40–65, 2 bodies):

```text
frame                7.91 ms  ->  10.52 ms      +2.61
  PreUpdate          1.83     ->   4.00         +2.18   <- 83% of the growth
  Update             2.40     ->   2.93         +0.53
  PostUpdate         1.54     ->   1.79         +0.25
  outside            1.04     ->   0.61         -0.43   (less idle: CPU-bound now)
```

⭐ **`PreUpdate` IS THE SIMULATION.** Not Bevy's input systems — `session.rs:748`
documents it: the whole sim runs inside `RunGgrsSystems`, in `PreUpdate`, because
the shipped single-player game runs under a GGRS SyncTest session with rollback
dormant. The sim-phase census sums to 4.32 ms against `PreUpdate`'s 4.00 ms,
which is the same quantity measured two ways.

Inside the sim, one phase moves and the rest do not:

```text
WorldPrep            0.534 ms -> 2.080 ms/tick  +1.546  <- 91% of the sim's growth
PresentationVisualSync                          +0.095
Progression                                     +0.068
Combat                                          +0.031
PlayerInput                                     +0.018
PlayerSimulation                                -0.050  (fell)
```

## Inside `WorldPrep`, measured 2026-09-01

`install_sim_phase_boundaries` now marks `WorldPrep`'s sub-sets. Headless,
`--start-room hall_of_characters`, `bodies=130` confirmed by the ECS census:

```text
WorldPrep.BeforeIntegrate   1.214 ms/tick   ~64%   <- HERE
WorldPrep.Integrate         0.534           ~28%   (the movement kernel)
WorldPrep.AfterIntegrate    0.081            ~4%
WorldPrep (umbrella)        0.104            ~5%
WorldPrep.ContactDamage     0.014           ~0.7%
```

**≈9.3 µs per body per tick, spent BEFORE any body has moved.**

## ⛔⛔ TWO THINGS THIS FALSIFIED

**1. It is not the O(n²) body-contact pairing.** `BodyContactSnapshot::field_for`
(`shared_tangle/src/body.rs`) is genuinely O(n²) — it linear-scans for the body's
own entry, then copies every OTHER body's blocker into a scratch, per body per
tick, with no broadphase. It looks damning and it is not the bug:

- `BodyContact` is inserted in exactly ONE place in the repo —
  `game/ambition_demo_smash/src/lib.rs:1840`. The hall's characters do not carry
  it, so `snapshot_body_contact` pushes nothing and every `field_for` returns
  `NONE` immediately.
- Confirmed independently by the measurement: `ContactDamage` is **0.014 ms**.

⇒ the quadratic code is real but DORMANT outside Smash, where n is 2–4 and n² is
12 pairs. ⛔ Do not "fix" it expecting the hall to move.

**2. It is not resident assets.** `PreUpdate` tracks BODIES, not images. The
natural experiment is in the same run at t=110–115: bodies drop 130 -> 2 while all
270 images stay resident, and `PreUpdate` falls 4.0 -> 2.54 ms. `bevy_asset` owns
41 of `PreUpdate`'s 163 systems and is not what scales.

## What is NOT known, and it is the next measurement

**Which system inside `BeforeIntegrate` spends the 1.2 ms.** The set holds at
least five candidates and the census measures the SET, not its members:

```text
tick_capture_holds          (monolith)
steer_mount_from_rider      (monolith)
advance_moving_platforms    (monolith)
snapshot_body_contact       (monolith — near-free in the hall, see above)
sync_sprite_posed_bodies    (ambition_character_sprites)
resolve_and_advance_clocks / advance_coordinate_time / advance_proper_time_cooldowns
                            (ambition_relativity2d — all `run_if(spacetime_is_active)`,
                             so probably inert here; CHECK rather than assume)
```

⚠ **A CANDIDATE I FOUND BY READING AND DID NOT MEASURE.**
`sync_sprite_posed_bodies` calls `posed_body_geometry` TWICE per body per tick —
once for the live anim, and again for `CharacterAnim::Idle` purely to recompute
`base_size`, which is a static property of the sheet. The `!=` guard prevents the
WRITE, not the WORK. That is genuine waste and worth removing on its own merits.
⛔ But the arithmetic says it is not 1.2 ms: `record_for_sheet_key` is a HashMap
lookup, so 260 of them per tick is microseconds. **Do not adopt this as the
answer without measuring it.**

## For whoever picks this up

1. **Per-system marks inside `BeforeIntegrate`** are the next step, the same way
   `install_sim_phase_boundaries` marks the phases. That is one instrumentation
   round and it ends the guessing.
2. **The scaling curve is still owed** (2, 16, 64, 130, 200 bodies). Two points
   cannot separate O(n) from O(n²), and the fix differs: a fat linear constant
   wants profiling one system, a superlinear term wants a spatial structure.
   At 130 bodies the per-body cost is 9.3 µs, which reads as a fat constant, but
   that is an impression and not a measurement.
3. ⛔ **DO NOT REACH FOR A PHYSICS ENGINE.** avian2d is already a dependency,
   behind the `physics_debris` feature, and is deliberately PAUSED when no debris
   exists — `world/physics.rs` states the principle: "a cosmetic effect paying for
   a physics engine is that invariant broken". Ambition's own collision runs
   inside `GgrsSchedule`, and putting avian's solver there makes its internal
   state, substep count and iteration order rollback state that must be both
   registered and checksummed. It would also replace `Integrate` and
   `ContactDamage` — 0.55 ms together — and not touch the 1.21 ms that is actually
   expensive.
4. ⚠ Every millisecond here is from a `--features profile` build. The no-Tracy
   comparison on a headless sandbox is 0.91 ms/frame against 3.95 ms with it — a
   **4.3x** tax (`desktop-perf-run-20260901T012959Z` vs `…T003332Z`). The RATIOS
   between sub-sets survive that; the absolute numbers do not.

## Frame numbers, for reference

```text
headless sandbox, no tracy     0.91 ms   1099 fps   (20260901T012959Z)
headless sandbox, tracy        3.95 ms    253 fps   (20260901T003332Z)
headless smash 2p, no tracy    2.82 ms    355 fps   (20260829T052757Z)
headless smash 2p, tracy       7.04 ms    142 fps   (20260829T055819Z)
windowed hall, 130 bodies,
  tracy, settled              11.39 ms     88 fps   (20260831T210231Z, t=70-110)
windowed same session,
  2 bodies                     7.92 ms    126 fps   (same run, t=40-65)
```

⛔ The two 08-29 sandbox rows in `runtime_frame_cost.jsonl` reading 13.6 ms and
1.52 ms are `source: "prose"` — transcribed, no distribution, and the second has
no bundle at all. They are notes, not measurements. Do not quote them.
