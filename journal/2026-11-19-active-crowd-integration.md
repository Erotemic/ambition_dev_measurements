# Active crowd integration

## Source and scope

The baseline is `19599c7d37f1` plus the crowd measurement fixture from
`7a79333f52`. The changed body phase is `7a79333f52`. The measured fixture is the
same in both binaries. The new effect-order test is not part of the timing loop.

The host witness remains `desktop-timeline-run-20261119T140515Z`, at
`0c4ba616483d`. Native Hall integration costs 6.391 ms per tick in the steady
entry+23 to entry+35 second interval. This task adds no new desktop trace.

The VM has six vCPUs, an Intel Core i7-7700HQ identity, and about 17 GiB RAM.
Bevy uses two compute workers under its default allocation. Cargo uses its
normal job concurrency. Timing runs have no concurrent build or export.

## Workloads and state comparison

The fixture uses real ECS target selection, real patrol brains, the shared
contact snapshot, collision composition, and the production body phase. Each
sample has 30 warm-up ticks and 120 measured ticks at a fixed 1/60 second step.
There are three samples per workload at 32, 128, and 512 actors.

The settled cast is a control. Roaming actors have separate patrol lanes.
Converging actors share a central patrol lane and have body contact. Moving-wall
actors patrol while a solid overlay wall moves. The patrol talk radius is zero
in these fixtures so nearby actors keep their movement goals.

All 36 before/after samples in each build configuration have equal endpoint
hashes, movement counts, and travel distance. The hash includes every actor
cluster field, movement model, brain, accepted control, movement output, combat
state, target, bounds, and movement facts, sorted by SimId. At 512 actors, 512
roaming actors, 480 converging actors, and 512 moving-wall actors travel more
than 50 pixels. Settled actors only fall to the floor.

The fixture excludes the full tactical perception and decision schedule,
navigation graph construction, damage application, and rendering. These results
do not establish desktop or laptop frame rate.

## Measured cost

Values are medians of three samples, in ms per fixture update. Body-phase time
includes room preparation, body integration, and effect publication. Complete
update time also includes targeting, brain calls, contact snapshots, and the
fixture schedule.

The normal development profile uses workspace opt-level 0 and dependency
opt-level 3. A second controlled comparison sets only
`ambition_platformer2d_actor_monolith`, `ambition_platformer2d_core`,
`ambition_characters`, and `ambition_combat` to opt-level 3. Other workspace
crates and development assertions keep their normal settings. This second build
is not the release or profiling profile.

| Build / 512-actor workload | Complete before | Complete after | Body before | Body after |
| --- | ---: | ---: | ---: | ---: |
| Normal dev / settled | 67.200 | 39.792 | 60.062 | 32.744 |
| Normal dev / roaming | 72.011 | 43.482 | 64.712 | 36.212 |
| Normal dev / converging | 85.190 | 51.936 | 76.201 | 43.172 |
| Normal dev / moving wall | 72.194 | 43.109 | 64.982 | 36.019 |
| Four hot crates at 3 / settled | 7.555 | 4.349 | 6.373 | 3.317 |
| Four hot crates at 3 / roaming | 7.673 | 4.814 | 6.524 | 3.762 |
| Four hot crates at 3 / converging | 16.621 | 10.469 | 15.194 | 9.095 |
| Four hot crates at 3 / moving wall | 7.818 | 5.094 | 6.517 | 3.949 |

The active workloads improve complete update time by 1.64 to 1.67 times under
normal dev, and by 1.53 to 1.59 times with the four hot crates at 3. This is a
latency gain from concurrent body steps, not evidence of less total CPU work.
The dense contact case still has more than linear population cost. Spatial
interaction indexing remains separate work that needs exact contact semantics.

Raw samples, state hashes, travel, and all population medians are in
[normal dev](../summaries/active-crowd-integration-20261119-normal-dev.json) and
[four hot crates at 3](../summaries/active-crowd-integration-20261119-hot-crates-opt3.json).

## Architecture

Room collision inputs are prepared before the body batch. Both controller
adapters use independent Bevy mutable queries and reusable worker buffers. Each
body reads the same entry contact snapshot and its own room pace, movement
frame, tuning, envelope, and portal admission. A build without threads uses the
same query path and body kernel.

Actor self-hits and optional causal movement operations are collected per body.
After the workers finish, the phase orders effects by session, room, and SimId,
then uses the native writers. Bodies without SimId use query order
within the same session and room. Each body's own effect order is preserved. The buffers are derived
scratch data, not new rollback state. The Hall content and simulation rate do
not change.

## Reproduction

Build the fixture at each source state, then preserve the test executable before
building the other state. Do not compile during the timing run.

```bash
cargo test -p ambition_platformer2d_actor_monolith --lib active_crowd_tests --no-run
```

Run the printed test executable with:

```bash
/path/to/test-binary active_crowd_tests::measure_active_crowds --ignored --nocapture --test-threads=1
```

For the second configuration, add these arguments to the build command:

```text
--config profile.dev.package.ambition_platformer2d_actor_monolith.opt-level=3
--config profile.dev.package.ambition_platformer2d_core.opt-level=3
--config profile.dev.package.ambition_characters.opt-level=3
--config profile.dev.package.ambition_combat.opt-level=3
```

## Verification

The complete owner suites pass: 606 actor tests and 181 world tests. The active
movement and effect-order tests also pass with causal enabled. All 53 absence
contracts hold. The new collision test compares prepared and lazy walls across
rooms, gate passes, body barriers, and an overlay change. The new effect test
changes archetypes and checks stable hit order and native hit-origin data.

The owner suites and `cargo check -p ambition_app` also pass after the merge at
`8fa3b3e9e6`. The later `240d002da7` merge changes a boss sprite test and planning
receipts, with no production-code change. The final absence check passes all 53
contracts at wire schema v509. The documentation check passes with 426 documents
and 2,571 local links.

`cargo test -p ambition_workspace_policy` has 34 passing policy tests and one
failure, `engine_policies`, with nine reported violations. All nine concern
production files that are unchanged from upstream `359c93ad1d`: dependency
allowlists in abilities, portals, and runtime; an optional character catalog;
pose and velocity writes in hosted-world restore; a render read of the controlled
subject; and a hit-feel velocity write. This patch changes none of those paths or
policy definitions.

The tests above were run directly with Cargo. The runner's certification ledger
does not record those runs, so `required_checks.py` still reports missing lane
receipts. The full workspace feature union, web build, mobile device runs, and
broad repo-tooling suite were not run. The causal smoke check was at the isolated
optimization commit.

The final `cargo test -p ambition_demo_smash_app -- --test-threads=4` run passes
18 unit tests and 56 integration tests, with six existing ignored cases and no
doctests. The integration suite takes 748.98 seconds. It covers portal recovery,
authored attack routes, charging, landing tech, body contact, elimination,
selection flow, and match restart. No build or export runs beside these tests.

## Target storage

Target storage is the verified ext4 bind on the VM disk. Two cleanups removed
405 completed incremental compiler sessions created by this task. Ownership was matched to this task's compiler artifacts, Cargo logs,
and creation interval. The cleanups recovered 6.20 and 9.23 GiB, or 15.42 GiB in
total. Older build sessions and binaries were kept.
