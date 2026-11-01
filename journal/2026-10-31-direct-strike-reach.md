# Direct strike reach in perception

Host: `aivm-2404`, machine ID `ec9af5ee73e34e07a46bddf870f96f2e`,
Intel Core i9-11900K at 3.50 GHz. These timings are from this VM.

The input Android capture is
`android-all-ambition-auto-20261101T003533Z`, from the Pixel 5.
Its frame census shows repeated slow intervals in the Hall of Characters.
The sample addresses have no Rust function names in this checkout. The matching
Android library is absent here. The capture does not establish perception as
the main frame cost.

Code inspection found repeated full move-data construction in
`collect_perception_peers`. Both the current attack and the repertoire reach
queries called `MoveSpec::frame_data`. Each call derived unrelated timing,
coverage, launch and hazard facts, and allocated active and cancel windows.

`MoveSpec::strike_reach` now supplies the shared reach rule. Full frame data
uses that rule too. Perception calls the direct query. Active hittable volumes
and captures extend reach; windboxes and spawned hazards do not. No attention,
viewport, belief or actor population rule changed.

Implementation: superproject commit `3f338378b`. The catalog, body-kernel and
Smash test suites pass. `cargo check -p ambition_app` passes. Six existing
Smash integration tests remain ignored.

## Local measurement

A temporary example in `ambition_entity_catalog` parsed the 25 authored move
catalogs under `game/ambition_content/assets/data/movesets`. They contained
461 moves. The direct query and full frame-data query returned equal reach
bits for each move. This corpus is not the full published game roster.

The timed workload used the perception repertoire filter: exclude verbs that
start with `air`, contain `special`, or equal `grab`. It resolved each remaining
verb to its move, for 322 queries per pass. Each arm ran 1,000 passes. Input
references and output values passed through `std::hint::black_box`.

Both arms ran in one release binary, built with `CARGO_INCREMENTAL=0`.
Eight repetitions alternated arm order. The first two pairs were excluded.
The temporary example was removed after the run. Raw output is local at
`/tmp/ambition-reach-measure.log`.

| query | median ns/query | min | max |
| --- | ---: | ---: | ---: |
| full frame data, then read reach | 117.71 | 117.08 | 120.85 |
| direct strike reach | 15.355 | 15.00 | 16.23 |

The query cost fell by about 87%, or 7.7 times. This measures the query, not
perception collection as a whole, simulation throughput, or Android FPS.

Open: repeat `scripts/autoprofile_android.sh` on the Pixel 5. Compare room
intervals and transition stalls as well as the aggregate FPS. Function-level
Android attribution still needs the matching unstripped library.

## Busy-room batch

The next Android capture, `android-all-ambition-auto-20261101T013457Z`,
completed after the APK packaging fix. It reports median 42.79 FPS. The earlier
capture reports 41.91 FPS. Warmup changed from 40 to 10 seconds and recording
changed from 120 to 90 seconds. The phone also started cooler. This does not
isolate the reach-query change.

The next code batch removes repeated work in ordinary engine paths:

- Brains with no perception requirement skip sensor-input construction. Their
  belief clearing and brain tick remain active.
- Perception snapshots reuse ID and team strings. An entity index replaces
  each observer's full scan for its own row. Room grouping remains stable.
- Crowd construction records liveness for bystanders without building their
  crowd identity maps.
- Melee spacing reads strike reach and startup directly from shared move rules.
- Contact damage and provocation read body components without constructing a
  mutable body view. Contact damage resolves victims directly.
- Rigged sprites prepare links between tween frames once. Drawing uses indexed
  links instead of a full next-frame scan per part.
- Cloned rigged assets share immutable draw tables, clips, and tween links.
- Rig binding uses borrowed sheet keys. Direct drawing reuses its pose buffer.
- The sprite watch parses a publication only when its bytes change. It reads
  loose files without building all published file records. Manifest comparison
  uses map lookups instead of nested scans.

The same authored actor population, texture tiers, perception extent, and
simulation tick rate apply. No Android throughput improvement
is claimed for this batch before the next phone run.

Two sprite-art tests fail with both the original and changed implementation:
`mary_os_parts_are_placed_by_her_body_rigs_pose` and
`the_rows_a_rig_and_the_animator_time_differently_are_these`. The first reports
the current expressive tall sheet's `ceiling_tech` pose. The second compares rig timing differences with a stored set. These tests were excluded from the
final crate lane after both baseline failures were reproduced. The existing
published tween, tier, and draw-envelope checks pass.

Implementation commits: `77a93cfc6` (body work), `e5e59dc4e` (rigged sprites),
and `3bd289f3c` (publication reads).

Validation:

- The six changed crate suites pass, with the two baseline art failures above
  excluded from the sprite-sheet run. Their integration tests and doctests run
  in the same lane.
- The complete Smash app suite passes, with six existing ignored tests.
- The live Hall test and its moving-slug control pass.
- `cargo check -p ambition_app` passes after the final render-cache change.
- Workspace policy reports five existing pose and velocity writes in the
  unchanged `crates/ambition_sim_view/src/camera_scenarios.rs`. No waiver was
  added.

Open: measure the full batch in one Android run. The two art checks and the
camera-scenario policy failures remain separate work.
