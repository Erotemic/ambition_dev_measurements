# Paired desktop follow-up: static contact and exporter memory

## Scope and evidence

The new pair is `desktop-timeline-run-20261119T105306Z` on calculex, at
`53e4f694dc07`. Its recorded dirty path is the measurements submodule.
The Tracy launch precedes a separate native build and launch. Both use
100-second autoplay windows, uncapped frame pacing, and the hardware GPU.

The Tracy trace saves 100.4 seconds, 1,120 frames, and 10,687,071 zones in
257.42 MB. Its recorded game status is 137. The launch command is
`timeout --signal=INT --kill-after=5s 100s ...`, and the game log reaches
100 seconds. The timing fits forced shutdown after the capture window.
That game status alone is not evidence of an OOM kill or the VM's loss.

Raw VM evidence is in `profiles/contact-export-vm-20261119T114543Z/`.
The source change for exporter limits is `ad392294e5`; the contact change is
`2d7f223718`.

## Native Hall comparison

These windows are 23 to 35 seconds after each Hall entry. All are after the
Hall graph is ready and before departure. They have 12, 11, and 11 sampled
census windows. Phase means are weighted by simulation ticks; frame means
are weighted by sampled frame count.

| Native pair | 025214Z | 045310Z | 105306Z |
| --- | ---: | ---: | ---: |
| Integration, ms per tick | 7.005 | 7.160 | 6.934 |
| Targeting, ms per tick | 1.016 | 1.407 | 1.107 |
| Perception, ms per tick | 0.928 | 0.948 | 0.940 |
| Frame mean, ms | 76.46 | 142.40 | 90.26 |
| Sampled frames | 164 | 84 | 127 |
| Simulation ticks | 752 | 706 | 686 |

The new frame mean is lower than the preceding pair, but still higher than
the older baseline. Integration improves only slightly against the preceding
pair. Source and host conditions differ between runs. This does not isolate
the previous grid change as the cause of the frame-time change.

The Hall graph's `Building` to `Going` interval is 20.172 seconds, against
21.115 seconds in the preceding pair and 21.953 in the older pair. Each graph
has 81 surfaces, 1,932 links, and 23,828 rollouts. These are single observed
intervals, not repeated isolated navigation benchmarks.

The new native samples still place `Grid::near` at 3.06% on async compute,
the ledge probe at 1.80%, and `polygon_axis_contact` at 1.40%. Relative sample
shares are not absolute CPU-time improvements.

## Tracy overhead

Within the new pair, a second comparison uses 25 to 35 seconds after Hall
entry, after graph completion in both launches. The native windows contain
107 frames and 559 simulation ticks; the Tracy windows contain 27 frames and
405 ticks. Weighted frame means are 87.65 and 370.54 ms. Integration means are
6.918 and 7.772 ms per tick. Actor positions and tick counts are not identical.

The instrumented run has substantial observer cost. Use its named zones to
locate work. Use the separate native run for runtime frame cost. Do not use
the whole-capture Tracy integration mean as an isolated Hall comparison.

## VM loss: what the snapshots establish

The maintainer reports another VM loss and restart. The phase is uncertain.
The host snapshots show:

| Tracy stage | UTC time | Available host RAM |
| --- | --- | ---: |
| Before build | 10:53:06 | 10.18 GiB |
| After build, before capture | 11:06:55 | 28.13 GiB |
| After capture | 11:08:43 | 26.98 GiB |
| After export | 11:09:26 | 28.23 GiB |

This is consistent with the VM disappearing during compilation, before the
game starts. It does not prove an OOM kill or identify its victim. Another
large memory release could change the same numbers. The guest reports
17.31 GiB of RAM; the host reports 30.89 GiB. Guest capacity is not a measured
host RSS. There is no host per-process peak record or host OOM verdict here.

Build concurrency remains Cargo's normal default. The whole-command RAM
limit remains opt-in. No VM allocation, host setting, or compiler job limit
was changed. A limit on an exporter cannot resolve a compiler/VM memory
shortage. Read the host kernel or OOM-manager record before assigning that
failure to Tracy capture or export.

## Exporter memory containment

The collector's percentage limit does not apply to CSV export. Each of the
two exporter invocations constructs a new Tracy worker from the full trace.
The Python instance reducer already streams rows; it does not retain all
zone instances.

Each exporter now runs in its own user memory scope. The default budget is
the smaller of half available RAM and a quarter of total RAM, measured at
that export's start. The compiler and game are outside this scope.
`--tracy-export-memory-mib N` selects another limit; `0` disables it. If the
scope is unavailable, export is skipped and the raw trace is retained.
`tracy-export-*-memory.txt` records each effective limit and exit status.

A real export of this trace passes under a 3 GiB limit. Its maximum RSS is
2,044,772 KiB, about 1.95 GiB. All 10,901 aggregate rows exactly match the
original report. The scoped instance export also passes at 3 GiB, with a
maximum RSS of 2,359,616 KiB, about 2.25 GiB. Its 51,010,756-byte window CSV
is byte-for-byte identical to the original. The reducer is observed at about
127 MiB RSS while the exporter exceeds 2 GiB. An intentional 128 MiB limit
kills the exporter with status 137. The user journal records that scope's `oom-kill` result. The caller and
VM remain alive, and the raw trace remains available. These checks establish
local containment, not the cause of the earlier host failure.

## Static polygon contact change

`polygon_axis_contact` projects both shapes onto each candidate edge normal.
For rectangular shapes, opposite cardinal edges repeat the same separation
test. The change tests each cardinal axis once. It retains the first axis, so
penetration ties keep the same contact order and minimum normal. General
axes keep their original path. The regression test checks both correction
axes and both obstacle windings when penetration depths tie.

The VM driver links the production core library at opt-level 3 and uses the
system allocator. It steps 128 bodies for 60 ticks per sample. Two balanced
idle batches give 24 samples per case per arm, pinned to VM CPU 0. Full body
state hashes match in every case. These are body-kernel measurements, with
no ECS host or renderer. A separate batch during compilation is retained as
raw evidence and excluded because its timing controls were unstable.

| Scene blocks | Baseline ns per body tick | New ns | Median reduction |
| --- | ---: | ---: | ---: |
| 8 | 6548.9 | 6395.8 | 2.34% |
| 100 | 7044.2 | 6764.0 | 3.98% |
| 2000 | 7397.4 | 6744.3 | 8.83% |

Unchanged cast-construction controls vary from 2.69% slower to 2.70% faster.
The smallest body change is near that variation. Both idle batches improve
the body medians. A separate guest `perf stat` pair reports 4,524,387,796
user-mode instructions before and 4,328,105,338 after, 4.34% fewer for the
whole probe, including its unchanged cast controls. State hashes also match
in that pair. These measurements do not establish a desktop FPS gain. No actor
count, authored room, quality tier, or gameplay policy was reduced.

## Verification

The final core suite passes 908 unit tests, including the contact-tie case.
One existing unit test, an allocation measurement, and one doctest remain
ignored. The assembled app check passes with `--features profile`.
The single-command profiler lane passes 23 tests. The Tracy version and
streaming lane passes eight tests. The real exporter success and OOM checks
are separate measurement probes.

Rust checks use
`--config profile.dev.package.ambition_platformer2d_core.opt-level=3`.
The work does not include a full workspace matrix, web/Android checks, or a
hardware desktop measurement of the new contact change.

The first full Smash run overlaps the instance-export probe and is killed by
the guest OOM killer. The guest kernel records `global_oom` and 15,158,468 KiB
of anonymous RSS for the test process. There is no assertion-failure verdict.
The full lane is repeated with four test threads and no concurrent exporter.
This changes test concurrency, not Cargo build concurrency. A per-process
export limit does not reserve RAM against growth in other concurrent jobs.

The repeated Smash lane passes 18 unit tests and 56 integration tests, with
six existing integration tests ignored. The integration run takes 650.56
seconds. There are zero doctests in that package. The core and Smash behavior
checks cover the contact implementation at `2d7f223718`, before the upstream
merge. The rerun's observed peak test RSS is 15.10 GiB; its sampled minimum
available guest RAM is 1.43 GiB.

The final upstream merge is `b01f73d3dd`. It also brings sprite animation,
addressed behavior delivery, lighting, and map changes through `2447ee946d`.
Those are independent changes, so a later desktop capture cannot isolate the
contact optimization by commit pair alone.

The merged-tree assembled app check also passes with `--features profile`
in 1 minute 24 seconds. The full Smash behavior lane was not repeated for
the independent upstream changes after that merge.
