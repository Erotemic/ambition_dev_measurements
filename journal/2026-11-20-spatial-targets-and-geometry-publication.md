# Spatial targets and geometry publication on aivm-2404 @calculex

Host: `aivm-2404`, machine ID `716a275cbdcb4fe3889ae543368500f7`, Intel
i7-7700HQ at 2.80 GHz, six guest CPUs, 17.31 GiB guest RAM. These are headless
phase measurements in the VM. They are not desktop frame measurements.

## Source and method

The [desktop follow-up](2026-11-19-desktop-parallel-body-result.md) found about
1.6 ms per tick in both targeting and FeatureViewSync. This work removes the
candidate scan per observer and measures allocation work in combat geometry
publication. It keeps all actor updates and exact room-wide target selection.

The baseline is root `d14c5ae9d131` plus the same ignored measurement fixtures.
Saved test binaries were built before production edits. The final code is in
`7d4bc595d9` (tree and targeting) and `1add31e00e` (publication). Three later
trait-path edits have no runtime effect and passed the assembled app check.
The [data file](../summaries/2026-11-20-spatial-targets-and-geometry-publication.json)
contains every sample, output hash, median, and full source ID.

Both arms use package defaults and the normal Cargo development/test profile:
workspace code at optimization level zero, external packages at level three,
and no debug information. No custom profile, Tracy, rendering, netcode, frame
cap, or vsync is involved. No build ran during the timed measurements.

Each sample creates a real Bevy app, runs ten warmup updates, then times forty
updates. Each workload and population has three samples. Every body changes
position on every update. The movement fixture is a deterministic trajectory;
it does not run a tactical brain or the physics kernel. Target timing includes
movement, candidate collection, index rebuild, selection, and disposition
publication. View timing includes movement and the selected publication system.

To repeat the final measurements from the root:

```bash
cargo test -p ambition_combat -p ambition_sim_view --lib measure_moving_ -- --ignored --nocapture --test-threads=1
```

## Target selection

The geometry crate owns `Spatial` and `SpatialTree`. The existing world grid
uses the same bounds trait. The tree accepts owned or borrowed objects, reuses
storage, supports nearest and bounds-overlap queries, and resolves nearest ties
by input order. Callers can supply conservative subtree summaries.

Combat owns the room trees and relation summaries. Faction and team masks call
the existing `combat_relation` policy. Body grudges use candidate identity
lookup and the same final distance/order comparison. Every tick rebuilds the
derived index. No rollback state or persistent relation cache is added.
Invalid numeric positions use the canonical scan. No distance cutoff is added.

The first prototype used one node per candidate. Leaf buckets reduced rebuild
and traversal cost. The final tree uses at most 32 items per leaf. A small-room
linear policy path was not added. At 32 actors, the opposed and team cases cost
25 and 34 microseconds more per update. That is a real setup tradeoff.

Median milliseconds per update:

| Workload | Bodies | Before | After | Speedup |
| --- | ---: | ---: | ---: | ---: |
| Mixed factions | 32 | 0.083531 | 0.071638 | 1.17 |
| Opposed factions | 32 | 0.066010 | 0.091368 | 0.72 |
| Four teams | 32 | 0.067546 | 0.101177 | 0.67 |
| Neutral | 32 | 0.077660 | 0.058033 | 1.34 |
| Mixed factions | 512 | 12.560681 | 1.373418 | 9.15 |
| Opposed factions | 512 | 5.829594 | 2.230688 | 2.61 |
| Four teams | 512 | 6.092705 | 2.354967 | 2.59 |
| Neutral | 512 | 12.445201 | 1.151500 | 10.81 |
| Mixed factions | 1024 | 49.418473 | 2.920749 | 16.92 |
| Opposed factions | 1024 | 22.445728 | 4.923307 | 4.56 |
| Four teams | 1024 | 22.037979 | 5.780082 | 3.81 |
| Neutral | 1024 | 48.965647 | 2.420604 | 20.23 |

All 48 before/after target hashes agree. Hashes cover the final snapshots, not
each intermediate update. The hash orders rows by SimId and
includes target position, target semantic identity, and disposition. Separate
oracle tests cover moving and shrinking snapshots, custom relations, teams,
body and faction grudges, rooms, duplicate candidate identities, and invalid
numbers. Geometry tests compare nearest and range queries with exhaustive
queries over moving points and rectangles.

## Publication

FeatureViewIndex and ActorRenderIndex already reuse strings. They were left
unchanged. CombatGeometryView built uncut Arc-backed material pieces, copied
their world volumes, and discarded each body row's vector on every update.
It now projects through the same material kernel into reused row storage.
Portal partition and refusal publication keep their existing path.

Median milliseconds per update:

| Workload | Bodies | Before | After | Speedup |
| --- | ---: | ---: | ---: | ---: |
| Feature index control | 512 | 0.480917 | 0.490836 | 0.98 |
| Body fallback geometry | 512 | 1.005763 | 0.619066 | 1.62 |
| Published geometry | 512 | 0.921030 | 0.584398 | 1.58 |
| Feature index control | 1024 | 0.986558 | 0.964340 | 1.02 |
| Body fallback geometry | 1024 | 2.048933 | 1.280156 | 1.60 |
| Published geometry | 1024 | 1.831480 | 1.281673 | 1.43 |

All 36 before/after view hashes agree. The fixture changes positions, hit flash,
and published/intangible material. Direct projection is also checked against
uncut material pieces. A refused projection clears partial output. Despawn and
intangible tests check that reused rows cannot retain old geometry.

The unchanged feature-index control varies by about two percent at the large
populations. These timings do not measure the whole FeatureViewSync schedule,
portal cutting, strike-heavy combat, or GPU presentation.

## Validation and open work

Owner library checks pass: combat 865 tests, geometry 68 tests, and simulation
view 166 tests. Seven opt-in measurements are ignored in the normal run.
`cargo check -p ambition_app` passes. All 53 absence contracts pass at schema
v511. Documentation links pass.

After the upstream merge at `da68c0399e`, the assembled app test build passes.
`cargo test -p ambition_app --features rl_sim --test app_it -- unified_body_movement`
passes all three selected tests. They cover the common home/actor integration
phase, the slot-owned control path for a brain-driven player, and the boss
envelope. The other 1,727 app tests were filtered out.

The core run passes 908 unit tests. One unit measurement, one allocation probe,
and one doctest remain ignored. The Smash app passes 18 unit tests and 55
integration tests. Six existing integration tests remain ignored. One camera
integration test fails:
`the_stage_kills::the_framing_centre_absorbs_an_elimination_instead_of_cutting`.
It measures an 8.3-unit elimination step against an 8.2-unit ordinary maximum,
with a 248.3-unit change in the cast center. An isolated rerun gives the same
failure.

To test cause, restore `targeting.rs` and `combat_geometry_view.rs` from
`d14c5ae9d131` in the same checkout. Keep the other current code and content.
Rebuild and run only that camera test. This old-selector and old-publication
control gives the same failure and the same reported values. It establishes
that these two optimizations did not cause the failure. Restore the optimized
files from saved copies and verify them against HEAD before further checks.
No camera code or assertion was changed.

The first policy run passes 34 tests and fails `engine_policies`, with eleven
reports. Two reports name the new measurement file: its clock and fixture pose
were scanned as production code. `10a0c0e1a7` moves that file to the existing
`_tests.rs` convention. It changes no workload or production behavior and adds
no waiver. The final `engine_policies` check reports nine failures. All seven
source paths named by those reports, and the policy definitions, match upstream
`4734a06022`. They cover dependency lists, catalog access, pose/velocity writes,
and a render read of the controlled subject. None is introduced by this work.

At `ef5914eaf1`, the focused actor-monolith crowd run passes both selected
behavior tests, with one measurement ignored and 604 other tests filtered out.
It drives real patrol brains through roaming, converging, and moving-wall
workloads. It checks repeated state, actual travel, and stable body-effect order.
The final simulation-view run passes all 166 tests, with three measurements
ignored. The final `cargo check -p ambition_app` passes on the same source.
All 53 absence contracts pass at schema v511. The final link check passes for
427 documents and 2,579 local links.

These checks use direct Cargo commands. They do not add runner certification
rows. The full workspace feature union, web build, and mobile device runs are
outside this verification lane.

The next desktop profile must measure the shipped profiling build with the
same population and quality. The VM results predict lower targeting and
publication cost; they do not establish a new desktop FPS. No new windowed
capture or Tracy memory claim is part of this work.

The camera assertion remains open. Its failure with both implementations needs
a separate camera-policy investigation.

Target storage uses the verified ext4 bind. Several builds needed a pause for
cache cleanup, and two attempts reached the disk limit. Only compiler sessions
and completed executables created by this task were removed. The checks above
describe the completed runs after cleanup. Older or unowned target artifacts
were kept. Cargo job concurrency was not limited.
