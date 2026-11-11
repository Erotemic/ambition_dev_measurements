# Native query reuse and conservative support candidates

## Source and scope

The baseline executable is built from `aa1e82b82`. Its runtime includes the
room-clock merge. Intermediate `32a268334` caches checked aperture steps,
uses native material envelopes for line support and normalizes zero source
turns. Final `d31127c19` also corrects two accepted-miss paths for non-finite
collider input. Its geometry and movement APIs keep typed refusal.

All executable copies and perf data are local, ignored artifacts. This
receipt and its companion JSON contain text only. The compiler, build
profile, binary/source hashes, recorded content hashes and commands are in
the JSON. No build or test runs beside a measurement.

## Implementation

A curved cast owns an immutable link snapshot. Its checked aperture steps
share geometry validation within that scene. Every sample checks its clock.
A failed preparation uses full sampling to keep the original refusal order.
The sampled geometry, map, scale, reflection and payload math stay shared.

Line support uses the room's existing swept index. Its query bounds include
all admitted physical cells, including far-side pieces. Exact native contact
still decides support. Direct minimum and maximum endpoints keep thin lines
inside their bounds at large coordinates. Unknown motion retains a candidate.
A zero-turn line can have a finite envelope without using its pivot.

Native collider vertices and displacement pass validation before a carve can
certify separation. The translation query uses that checked input. The two
new witnesses first returned `Ok(None)`: one for a carved part, and one for an
unknown block sweep outside the body's ordinary region. Both now require a
typed non-finite refusal. Finite, remote geometry can still be excluded.

## Measurement protocol

Each room has three pairs per arm. Arms are interleaved; low/high order
alternates. Each arm first runs a 60-tick warmup for that room. Measured
processes run 60 or 360 idle-input ticks. The estimate is
`(instructions_at_360 - instructions_at_60) / 300`.
Counters are `instructions:u` and `task-clock`; coverage is recorded.
The driver checks recorded content hashes before and after every process.
It also requires matching simulation ticks, positive shared-clock steps,
body counts and peak portal counts across arms and repeats. Shared-clock
steps do not measure per-room pace.

The Hall has 524 bodies and zero portals. The lab has 11 bodies and a peak
of 34 portals. These are simulation CPU workloads, not displayed-frame or
mobile measurements. The historical control has different runtime and
content; see the earlier native motion cost receipt for that comparison.

## Matched instruction estimates

| Room | Baseline million instructions/tick | Final | Change |
| --- | ---: | ---: | ---: |
| hall_of_characters | 113.203 | 78.424 | -30.72% |
| portal_lab | 283.791 | 152.896 | -46.12% |

All 24 measured processes and four warmups finish. Counter coverage is 100%.
All recorded content hashes and census fields match. The final comparison
includes the collider refusal correction. These measurements precede the
boss-height and dialer content changes merged in `eabde9a66`. Do not apply
the reported values to that later authored workload without another sample.

## Native sample

The saved baseline and final executables are sampled with `cpu-clock:u` at
99 Hz and a 32,768-byte DWARF stack. Sampling starts two seconds after session
publication and stops at the benchmark census. Reports disable inline
expansion and include child samples. All four captures report zero lost
samples. The same wall delay covers different tick intervals in the two arms;
use the interleaved counters for the cost estimate.

| Workload and function | Baseline inclusive CPU share | Final |
| --- | ---: | ---: |
| Hall: material_lines | 22.17% | 6.96% |
| Hall: MaterialCast::cells | 10.88% | 16.05% |
| Lab: MaterialCast::cells | 88.83% | 80.36% |
| Lab: arcs::build | 84.60% | 70.79% |
| Lab: Scene::pose | 72.85% | 54.59% |
| Lab: raw ApertureStep::sample_wide | 61.25% | 2.81% |
| Lab: checked PreparedApertureStep::sample_wide | absent | 32.02% |

Shares overlap and are not wall-time fractions. Cell construction remains
an important cost. The larger relative Hall cell share does not mean that
cell construction became slower.

## Live host sample

Each match reaches 1,800 simulation ticks in 1,800 host updates with 100%
sampled live-cast coverage. The assembled NoWindow host uses GGRS, four CPU
fighters at level 3, 40 stocks and debris off. It has no render app or adapter.
The arms are interleaved. Mirror uses four performers; mixed-v1 uses performer,
robot, Sanic and projectile polygon. Few wall-time runs do not establish a
general speedup.

| Run | Baseline mean ms/tick | Final mean ms/tick | Baseline P95 host ms | Final P95 host ms |
| --- | ---: | ---: | ---: | ---: |
| mirror-1 | 4.3136 | 3.7978 | 6.5082 | 5.6297 |
| mirror-2 | 3.9424 | 3.6427 | 5.4990 | 5.2066 |
| mixed-1 | 4.0286 | 3.9015 | 5.6532 | 5.5488 |

## Verification

The final core library passes 891 active tests; one existing probe is ignored.
Four new guards cover a thin line endpoint, unknown line motion and recovery,
carved collider input, and an unknown world block sweep.
Merge `eabde9a66` passes the assembled app check, 8 declared-art cases,
3 registered-character-art cases, 60 portal-enabled held-item cases, the
projectile refusal/recovery case, all 4 slice cases, both chain-lab cases and
all 3 room-freeze cases. The slice includes stationary partial contact,
persistent scale/reflection, reverse travel, real rewinds and byte-identical
replay. The real renderer, item release and far-chart shot birth also pass.

Rollback coverage first reports the newly merged `ScopePace` as unclassified.
`6050392c3` documents its composition-only contract and accounts for its ECS
access cache beside installed port codecs. Clock values remain canonical.
All 21 active coverage cases then pass; 2 probes remain ignored. This change
adds no canonical state or schema field.

All 73 active Smash cases pass; 6 probes are ignored. The app, core and app
tools all-target warning check passes under their default features. These
results are recorded in `gates-prepared/status.json` and
`gates-prepared-finish/status.json` in the local experiment directory.
Full workspace, device, displayed-host and full PSIM acceptance remain open.
Do not replace native material contact with root-AABB collision or repair.

## Reproduction

Use the recorded source and content revisions. Desktop executables retain
an absolute source content path. Copying an executable does not copy its
content or change that path. Rebuild in a matching checkout.

```bash
cargo test -j12 -p ambition_platformer2d_core --lib
cargo build -j12 --profile profiling -p ambition_app_tools --bin headless --bin smash_match_profile
AMBITION_SPRITE_WATCH=0 perf stat -x, -e instructions:u,task-clock -- ARM/headless 360 --bench --start-room portal_lab
AMBITION_SPRITE_WATCH=0 ARM/smash_match_profile --fighters 4 --cast mirror --character performer --ticks 1800 --stocks 40 --debris off
AMBITION_SPRITE_WATCH=0 ARM/smash_match_profile --fighters 4 --cast mixed-v1 --ticks 1800 --stocks 40 --debris off
```

For counters, warm each arm at 60 ticks. Run three 60/360 pairs for each arm
and room. Interleave arms and reverse low/high order on the second pair.
Keep recorded content hashes and census equal across all processes. Sampling
uses `perf record -e cpu-clock:u -F 99 --call-graph dwarf,32768` with a control
FIFO; enable and disable at the stated session/census boundaries.

The prior first-pass source-cell receipt remains a separate experiment.
Obsolete copied executables from that pass were retired after their text
metrics were published. This comparison's baseline, final and historical
control executables remain in ignored target storage.

## Later main integration

Merge `803247457` retains the nearest-interaction change and conductor health
floor. The assembled app check, 235 boss-owner tests, 4 boss-port tests and
all 4 schema checks pass with schema 460. The interaction library compiles;
it contains no tests. This narrow lane does not repeat the earlier portal
suite. The recorded cost samples still use their frozen source and content.
