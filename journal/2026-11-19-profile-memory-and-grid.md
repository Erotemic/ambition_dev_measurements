# Paired desktop follow-up: memory limits and grid queries

## Desktop measurements

The baseline is `desktop-timeline-run-20261119T025214Z` at `b18365198531`.
The new pair is `desktop-timeline-run-20261119T045310Z` at `ea02b9a4e5b3`.
Both use 100-second autoplay launches on calculex. The newer source also
contains upstream changes, so this is not an isolated comparison of one patch.

The new Tracy launch saved 100.5 seconds, 9,175,390 zones, and a 185.43 MB
trace. Export completed. Its status 137 is recorded after the timed shutdown;
the saved data is usable. The new native route reaches the tyrant Hall before
the capture ends. Much of that progress comes from earlier startup, so route
completion alone is not a throughput measure.

The Hall graph's `Building` to `Going` interval changes from 21.953 to 21.115
seconds, with the same 81 surfaces, 1,932 links, and 23,828 rollouts. Tracy's
whole-capture body integration average changes from 6.996 to 6.626 ms per tick.
Those numbers are encouraging but mix different startup and room intervals.

Matched native Hall census windows tell a more cautious story. The windows are
about 22 to 37 seconds after Hall entry, after graph construction, with one
second excluded at each boundary. They use wall intervals 69.669–83.987 seconds
in the baseline and 56.175–71.234 seconds in the new run.

| Measure | Baseline | New |
| --- | ---: | ---: |
| Integration, weighted by simulation ticks | 6.986 ms | 7.141 ms |
| Targeting, weighted by simulation ticks | 1.022 ms | 1.406 ms |
| Perception, weighted by simulation ticks | 0.927 ms | 0.949 ms |
| Frame mean, weighted by sampled frame count | 76.37 ms | 144.28 ms |

This pair does not establish a desktop FPS gain from the earlier optimization.
The named GPU-pass medians are similar: opaque pass 0.704 to 0.684 ms, MSAA
writeback 1.736 to 1.693 ms, and UI 0.571 to 0.528 ms. These are not a complete
GPU budget and do not identify the frame-time regression's cause. Host resource
pressure remains a hypothesis.

## VM loss and profiler memory

The maintainer reports that a host profile can crash the VM. The guest's prior
journal has no host OOM record. It cannot prove that the host killed QEMU.
The profile had no collector memory cap and used Cargo's full default job
count. Those are concrete ways to compete with a running VM for host RAM.

`ad6880e800` adds per-profile memory control. On Linux with cgroup v2 and a user
systemd manager, the command enters a transient memory scope. The default
limit is the smaller of half available RAM and a quarter of total RAM.
It applies to builds, launched games, collectors, and reports. An attached
existing game stays outside that process group. The command uses one build
job by default and delegates explicit `CARGO_BUILD_JOBS` values to Cargo.
The Tracy collector now has a default limit of 10% of system RAM.

`--memory-limit-mib N`, `--jobs N`, and `--tracy-memory-percent N` select other
budgets. A memory limit of 0 disables the scope. Auto mode records when the
user manager is unavailable; an explicit nonzero scope budget then fails.
The profiler does not change VM settings or reserve RAM for it. Other host
consumers can still exhaust global memory.

The report records the limits. `memory-*.txt` records available RAM, the cgroup
path, current memory, peak memory where the kernel supplies it, the limit, and
memory events at stage boundaries. A cap can end the profiling attempt. The
outer wrapper stays outside the scope and reports a terminated limited run.

Two real checks exercise containment. A 64 MiB scope receives a 256 MiB
allocation; the kernel reports `CONSTRAINT_MEMCG` and kills that scope's
allocator. The outer process and VM remain alive. A second check runs the
actual profiler with a 128 MiB limit and a deliberately oversized fake build.
It returns status 143 and prints the memory-budget diagnostic. These tests
prove local containment on the VM, not the cause of the earlier host failure.

The saved-trace report path also stops creating `tracy.skipped` solely because
a game exits with status 137 when a trace is present. The raw exit status stays
in the bundle. Export failures still retain their explicit failure marker.

## Next engine change

The latest native samples put `Grid::near` at 3.91% on the async compute thread
and 0.78% on the main thread. The sorted grid performed a binary search for
every possible cell coordinate, including empty rows.

`55c62e3f4f` searches once for the first row in each column, then scans the
occupied rows until the query ends. A single-row query keeps its direct cell
lookup. Source-index sorting, deduplication, hidden entries, added entries,
swept bounds, and the global fallback retain their rules.

Raw evidence is in `profiles/profile-memory-grid-vm-20261119T091933Z/`.
The production core library is built with opt-level 3, then linked into
optimized Rust drivers. The system allocator is used. Both arms run on VM
CPU 0, in balanced order, with nine samples per case. Ordered candidate hashes
and body-state hashes agree. No renderer or ECS host is in these benchmarks.

For 128-pixel rectangular queries, measured throughput is 1.09–1.21 times the
baseline. For 512-pixel rectangles, it is 1.18–1.88 times the baseline. The
refined single-row path removes the earlier roughly 10% small-query loss.
Small queries are near baseline; the largest remaining observed loss there is
about 1.8% in a layered case. Global-query controls vary despite an unchanged
fallback; one small layered control is about 8% slower.

Body-step timings and unchanged swept-cell controls also vary by several
percent. They do not establish a whole-body or desktop frame-time gain from
this grid change. The direct rectangular-query improvement is the supported
claim. No Hall content, actor population, or quality setting was changed.

## Verification

The existing spatial-query property tests compare visible list order with a
linear scan for local, global, point, hidden, added, and swept queries. The
full core suite passes 907 unit tests, with one existing unit test ignored.
The allocation measurement and one doctest remain explicitly ignored.

The profiler, version, and streaming lane passes 31 tests. The saved-trace
case now covers a forced game exit without losing usable diagnostic data.
The real scope OOM checks are retained as measurement artifacts.

The assembled-app check passes with `cargo check -p ambition_app --features
profile -j 1`. Rust checks use
`--config profile.dev.package.ambition_platformer2d_core.opt-level=3`, the same
core setting as the benchmark. The Rust tree under test is `55c62e3f4f`.
`004201e322` keeps the profiler monitor outside its memory scope and delegates
explicit Cargo job settings. The final 31-test profiler lane covers that tree.

These checks do not cover the full workspace matrix, web, Android, a hardware
GPU run, or a host-and-VM memory stress test. No new host run is required to
retain the measured grid change or the profiler containment fix.

The full `cargo test -p ambition_demo_smash_app -j 1` lane passes: 18 unit
tests and 56 integration tests, with six existing integration tests ignored.
The integration run takes 620.86 seconds. It runs the real headless simulation.
All three Cargo lanes finish with status 0.
