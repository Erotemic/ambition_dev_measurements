# Native portal motion cost and source-cell reuse

## Scope

The current runtime is `05f00a25cc0c0d9005d3982023c9e25afd16cec7`.
The baseline adds only the headless census patch. The measured change builds
one immutable source material program per loose-item flight leg and shares
it across hurt recipients. Each recipient keeps its exact material query.
Refusals still reject the transaction before movement and hit writes.
The first accepted contact per recipient and final contact sort stay the same.

Both variants use rustc 1.95.0 (59807616e, LLVM 22.1.2), the repository
`profiling` profile, retained symbols and the same dependency features.
The VM machine ID is `ec9af5ee73e34e07a46bddf870f96f2e`. It reports 12 CPUs, an Intel Core i9-11900K at 3.50 GHz and Linux 6.8
under KVM. No build or test ran during measurements. The sprite watcher is
off. These are simulation CPU samples. They do not measure displayed frames,
GPU capture cost or mobile cost.

## Matched current-runtime instruction counts

Each room has three pairs of independent processes. Each pair measures 60
and 360 idle-input ticks. Pair order alternates. The estimate is
`(instructions_at_360 - instructions_at_60) / 300`, which removes a shared
startup estimate. It is not a direct counter window around an individual tick.
Both variants use equal recorded RON/LDtk input hashes and benchmark census code.
Counters are `instructions:u` and `task-clock`, with no counter multiplexing.
The baseline and reuse arms ran in separate blocks. The low/high order
alternates within an arm; the two binaries are not interleaved. Small
differences can include changes in background work. Do not treat the 2.99%
Primeval estimate as a demonstrated general speedup.

| Room | Baseline million instructions/tick | Reuse | Change |
| --- | ---: | ---: | ---: |
| hall_of_characters | 113.700 | 113.715 | +0.01% |
| primeval_trailhead | 2.381 | 2.310 | -2.99% |
| portal_lab | 348.729 | 283.680 | -18.65% |

The census matches across all repeats and both variants. The Hall has 524
bodies and zero portals. Primeval has 11 bodies and zero portals. The lab has
11 bodies and a peak of 34 portals. The Hall and lab run all requested active
steps. Primeval has 60 active steps in the short run and 351 in the long run;
nine steps have normal combat impact hitstop. All actual simulation tick
counts equal the requested count. The Hall data shows no material change
from this optimization. It does not establish the cost of the earlier
movement architecture change.

## Native portal-lab sample

Each saved executable runs 720 ticks. Sampling starts two seconds after the
session publication marker and stops at the benchmark census marker. The
event is `cpu-clock:u` at 99 Hz with a 32,768-byte DWARF stack. Reports disable
inline expansion and include child samples. Both captures report zero lost
samples. The baseline window lasts 16.860 seconds; reuse lasts 13.730 seconds.
The faster executable covers a different tick interval after the same wall
delay. Use the controlled instruction counts for the measured gain.

| Function | Baseline inclusive CPU sample share | Reuse |
| --- | ---: | ---: |
| MaterialCast::cells | 91.16% | 90.01% |
| arcs::build | 86.23% | 83.88% |
| ground_item_physics | 47.62% | 36.91% |
| carry_or_wake_settled_items | 35.18% | 42.97% |

These shares overlap. Do not add them or treat them as wall-time fractions.
Native cell construction remains the main sampled cost. This change removes
repeated source work without adding a second collision authority.

## Live four-fighter sample

The assembled NoWindow host uses GGRS, four CPU fighters at level 3, 40 stocks
and debris off. It has no render app or adapter. Each row reaches 1,800 actual
simulation ticks in 1,800 host updates with 100% sampled live-cast coverage.
Mirror uses four performers; mixed-v1 uses performer, robot, Sanic and
projectile polygon. The timing window starts after the opening ceremony.

| Run | Baseline mean ms/tick | Reuse mean ms/tick | Baseline P95 host ms | Reuse P95 host ms |
| --- | ---: | ---: | ---: | ---: |
| mirror-1 | 3.9851 | 3.7327 | 5.6068 | 5.4204 |
| mirror-2 | 3.7704 | 3.7277 | 5.7466 | 4.9320 |
| mixed-1 | 4.0176 | 3.8718 | 5.5875 | 5.4806 |

These few wall-time runs do not establish a general speedup.

## Historical engine control

The control is `3c64902e259897f1a1840908a2bcc3e1c860b268`, the parent of
`fddf6084a`, with the same headless census patch. It uses the same compiler,
profile, flags and machine. It runs from assigned slot 3 with its historical
submodule pins and content. Its runtime and authored input hashes differ
from the current snapshots. These are whole-engine snapshot measurements.
They do not isolate the material movement change from later work.

The first Hall pair is invalid: its 60-tick process costs 107.941 billion
instructions and its 360-tick process costs 47.744 billion. Its subtraction
is negative. The next two pairs are 55.359 and 55.361 million instructions
per tick. After the room pass, three new warmed Hall pairs measure 55.405,
55.288 and 55.622 million, with a median of 55.405. Keep the invalid pair in
the companion JSON. Do not use it as a gain or regression estimate.

| Room | Warm historical million instructions/tick | Current with reuse | Historical bodies/peak portals | Current bodies/peak portals |
| --- | ---: | ---: | --- | --- |
| hall_of_characters | 55.405 | 113.715 | 524 / 0 | 524 / 0 |
| primeval_trailhead | 1.772 | 2.310 | 11 / 0 | 11 / 0 |
| portal_lab | 33.085 | 283.680 | 8 / 24 | 11 / 34 |

The Hall has the same body and portal counts but costs about twice as many
instructions in the current snapshot. This is a measured performance concern.
The lab has different content and capabilities; its rows are not a matched
workload. The instruction counts do not establish one function's causal cost.
All historical tick and active-step counts match their current counterparts.

| Historical live match | Mean ms/tick | P95 host update ms | Live-cast coverage |
| --- | ---: | ---: | ---: |
| mirror-1 | 3.4839 | 5.2784 | 100% |
| mirror-2 | 3.7501 | 5.7679 | 100% |
| mixed-1 | 3.7131 | 5.4240 | 100% |

Each historical match reaches 1,800 actual simulation ticks in 1,800 host
updates. The cast and command match the current live-host runs. These wall
samples vary and do not establish a broad regression or an optimization gain.
R5 remains open. Native cell construction, portal-free simulation cost,
displayed-host cost and hardware/mobile cost still need work.

## Reproduction

Build each source revision with its required target bind active. Save a copy
of each executable before the next build. Do not run a build beside counters.
The historical source needs the same small census patch.
Desktop builds retain their source content path. A saved executable is not
a content snapshot. Copying it or changing the working directory does not
change that path. Rebuild from the recorded source and content revisions;
do not run an old executable against a later content schema.

```bash
cargo build -j6 --profile profiling -p ambition_app_tools --bin headless --bin smash_match_profile
AMBITION_SPRITE_WATCH=0 perf stat -x, -e instructions:u,task-clock -- PATH/TO/headless 360 --bench --start-room portal_lab
AMBITION_SPRITE_WATCH=0 PATH/TO/smash_match_profile --fighters 4 --cast mirror --character performer --ticks 1800 --stocks 40 --debris off
AMBITION_SPRITE_WATCH=0 PATH/TO/smash_match_profile --fighters 4 --cast mixed-v1 --ticks 1800 --stocks 40 --debris off
```

Repeat the room command at 60 and 360 ticks, three pairs per room, in the
source checkout that owns its content. The companion JSON records exact
commands, binary/source/input hashes, counters, census and all repeats.
Executable copies, perf data and images remain in ignored target storage.
No binary artifact is part of this receipt.

## Verification and status

Source change `a87a5936e` passes held-item libraries with no default features
(58) and with portals (60), all four real-app portal slice checks, 73 active
Smash checks, the assembled app check and all-target warning checks for app,
held items and app tools under their default features. Six Smash probes are
ignored. The slice checks include stationary partial support, reverse travel,
persistent material, item release, far-chart shot birth and real rewinds with
byte-identical replay.

Room-clock merge `938a3c686` passes the same lane and three room-freeze app
checks. Local pull `758590c14` arrived during the lane. Its runtime Rust is
unchanged; the map semantic diff changes cedar props in the village and leaves
the portal lab unchanged. Room-freeze and warning checks finish on that tree.
The added networking assertions are compiled, not executed by these filters.

This lane does not cover the full workspace, non-default warning paths,
displayed-host performance, hardware GPU or mobile acceptance. R5 and full
PSIM acceptance remain open. Do not add a second collision authority to hide
native material cost.
