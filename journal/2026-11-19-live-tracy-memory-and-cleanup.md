# Live Tracy memory and build-cache cleanup

## New desktop pair

`desktop-timeline-run-20261119T140515Z` measures `0c4ba616483d` on calculex.
The Tracy launch and native launch each request 100 seconds. The native pass
has no Tracy client. The Tracy trace saves 100.2 seconds, 765 frames, and
6,590,449 zones in 149.42 MB. Both exporter scopes finish with status 0 under
7,908 MiB limits.

Compare native Hall windows 23 to 35 seconds after room entry. Both intervals
are after graph completion and before departure. Phase means are weighted by
simulation ticks; frame means are weighted by sampled frame count.

| Measure | Previous 105306Z pair | New 140515Z pair |
| --- | ---: | ---: |
| Sampled frames | 127 | 185 |
| Simulation ticks | 686 | 745 |
| Frame mean, ms | 90.259 | 67.158 |
| Integration, ms per tick | 6.934 | 6.391 |
| Targeting, ms per tick | 1.107 | 1.317 |
| Perception, ms per tick | 0.940 | 0.944 |
| Hall graph build, seconds | 20.172 | 19.099 |

The graph still has 81 surfaces, 1,932 links, and 23,828 rollouts. Integration
is about 7.8% lower and the sampled frame mean about 25.6% lower. The source
pair also includes sprite animation, behavior delivery, lighting, and map
changes. This is not an isolated measurement of the polygon-contact patch.
Startup is slower: native Hall entry moves from 29.977 to 40.815 seconds.

Remaining native sample hotspots include `Grid::near` at 3.40% on async
compute, the ledge probe at 2.05%, targeting at 1.52% on the main thread, and
material-cast cell construction at 1.48%. These are relative sample shares,
not absolute CPU-time comparisons. No Rust gameplay code changes in this task.

## VM loss interval

The maintainer reports another VM loss. This pair has different timing from
the preceding pair:

| Tracy stage | UTC time | Available host RAM |
| --- | --- | ---: |
| Before build | 14:05:18 | 10.12 GiB |
| After build, before capture | 14:20:48 | 10.15 GiB |
| After capture | 14:22:38 | 27.19 GiB |
| After export | 14:23:02 | 28.04 GiB |

This is consistent with losing the VM during capture or shutdown, before
export. It does not prove an OOM kill or identify the victim. A host kernel or
OOM-manager record is still needed for that claim. The game status 137 also
fits the existing 100-second timeout and five-second forced shutdown.

The exporter limits work on this pair, but the live game and collector still
have no hard memory limits. The collector's 10% recording threshold uses
total host RAM and does not account for RAM already occupied by the VM.

## Runtime protection

`2cbb4d50fc` gives the launched Tracy game and collector separate user memory
scopes after the warm build. The combined default budget is the smaller of
75% of available RAM and 37.5% of total RAM. The game gets two thirds; the
collector gets one third. With about 10.15 GiB available, this gives roughly
5.1 GiB to the game and 2.5 GiB to the collector.

The collector's internal recording threshold is lowered when needed to leave
space below its hard limit for saving. A game OOM leaves the collector alive
to save. The native pass in a paired command still runs. Compiler concurrency
and compiler RAM remain unchanged. The whole-profile memory limit remains
opt-in; the exporter limits remain separate.

`--tracy-runtime-memory-mib N` sets another combined budget. `0` disables these
runtime scopes. If a user memory manager is unavailable, the Tracy launch is
skipped and the paired native pass continues. `tracy-runtime-memory.txt`
records effective limits, the collector threshold, scope names, and results.
It is exposed at the top of the paired bundle and shown in the summary.
A scope limit does not reserve RAM against growth in other jobs or control
all host graphics allocations. This is not a guarantee against every host OOM.

## Verification

A small C++ client links the cached Tracy 0.14.0 library. It emits 200 zones,
then either exits, ignores SIGINT until the timer kills it, or commits 1 GiB
inside a 512 MiB game scope. The real collector has a separate 256 MiB scope.
The actual profiler command runs each case. Cargo and the native recorder are
stubs in this probe; the Tracy client, collector, exporters, and kernel limits
are real. The resulting trace and exported zones are checked.

| Case | Game status | Game scope result | Valid trace/export | Native pass |
| --- | ---: | --- | --- | --- |
| Normal exit | 0 | success | yes | 0 |
| Timed forced shutdown | 137 | success | yes | 0 |
| Game memory exhaustion | 137 | oom-kill | yes | 0 |

The caller and VM remain alive in all three cases. The scope result separates
a timed status 137 from a recorded scope OOM. This is a synthetic containment
check, not another desktop Ambition measurement.

The CLI and Tracy tooling population has 31 tests. An initial full run passes.
After the test fixtures disable OS memory scopes for portability, 30 pass and
one exposes a racy assertion about which independent process logs first.
That assertion is removed; its focused rerun passes. Build-before-capture,
Tracy/native separation, exports, and retained-data checks remain in place.
No Rust build or broad simulation suite is needed for this script-only change.

## Target storage

The restart removed the guest target bind. It is restored to the prescribed
ext4 store before cleanup. The shared-mount target underneath it is untouched.
No Cargo or Rust compiler process is active during deletion.

The cleanup removes 239 incremental-cache sessions identified from this
agent's earlier build intervals and logs. Their directory birth times are
checked against the saved manifest. It reclaims 8.19 GiB and leaves 14.38 GiB
free on the guest filesystem. Older sessions outside those intervals, current
libraries, executables, assets, and other build stores remain.

Raw comparisons, cleanup manifests, probe sources, logs, and synthetic traces
are in `profiles/tracy-runtime-memory-vm-20261119T153334Z/`.

The later upstream merge is `7546c758f2`, through `07ab11c4bb`. It leaves
the verified profiler files unchanged. Its independent gameplay and networking
changes are outside this script verification lane.
