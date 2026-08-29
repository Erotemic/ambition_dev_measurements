# ambition_dev_measurements
This is a repo that helps keep measurement bloat out of the main repo

## goal_check_cost.jsonl

The goal guard used to run shell checks — in this repo, cargo builds and test
suites — at the end of every turn, and released the run when they all passed.
This ledger timed them from 2026-08-08.

Read back on 2026-08-25 it retired the feature: **972 runs, 66.0 hours of cargo,
median 192s rising to 395s over the last 40 runs, and not one run ever passed.**
In 972 of 972 the verdict was already sealed by a sub-second `docs/planning`
grep before the build started. Concurrency roughly doubled the cost (186s with
no foreign build in flight, ~320-370s with one or more), while machine load
alone explained little — contention for the target directory, not for CPU.

`scripts/goal_guard.py` runs no commands now, so nothing appends to this file.
Schema 2 rows: `total_seconds`, `load_before`/`load_after`, `build_processes`,
`foreign_builds` (null = the repo never declared what a build looks like), and
per-check `name`/`ok`/`seconds`. Incidents: `docs/tools/goal-guard.md`.

## runtime_frame_cost.jsonl

**Runtime, not compile.** One row per profiling bundle, so a frame time survives
the bundle: `scripts/profile_desktop.sh` writes hundreds of megabytes of trace
that gets deleted, and until this ledger existed nothing could answer *did this
commit make the frame slower than last week's*.

Written by `scripts/lib/profile_bundle_to_history.py <bundle-dir>`, which reads
the small artifacts a bundle already derived (`metadata.json`, the census CSVs,
the Tracy zone export, the perf thread report) rather than re-parsing traces.
Read back with `scripts/perf_history.py` — `list`, `compare A B`,
`latest --against`, `scenario <id>`, `report`.

Schema 1 rows carry the shared envelope (`schema`, `kind: "runtime_frame"`,
`recorded_at`, `commit`, `dirty`, `run_id`, `label` — see
`dev/compile_telemetry_schema.md` §1) plus `measured_at` (the RUN's clock, which
is what orders the series; a backfilled row is written months later), and these
blocks: `scenario` · `build` · `host` · `gpu` · `display` · `instruments` ·
`run` · `frame_ms` · `spikes` · `phases_ms` · `scheduler` · `scene` · `bundle` ·
`provenance`.

⛔ **`comparable_key` decides what may be subtracted from what.** A frame time is
a property of a commit *on a scenario, on a machine, at a renderer, under a set
of instruments*. The key is a hash of `comparable_fields` — scenario id and
content version, headless, cargo profile and features, package/binary, machine
id, CPU model, logical CPUs, renderer (hardware / software / headless), adapter,
resolution, and whether Tracy / perf / the census were on. A lavapipe run never
groups with a hardware-GPU run, and a Tracy run never groups with an unprofiled
one — on Ambition those two are ~9x apart on the same commit. `perf_history.py`
refuses across a mismatch, names the field, and exits 2 (a real regression exits
1). Kernel, rustc and tick count are *advisory*: real but too coarse to split the
series on, so they are printed beside a comparison instead of forbidding it.

⚠ Nulls mean *nothing measured this*, never zero. A `--no-tracy` run has no zone
counts; a headless run has no adapter; `[census] frame` emits no p90 at all.

The first two rows are `backfilled: true` — transcribed from
`docs/planning/engine/performance-and-iteration.md`, not extracted from a bundle,
because the bundle they describe was deleted before this ledger existed. They
record the same commit and scenario under Tracy (13.6ms) and without it (1.52ms),
in two deliberately different comparability groups. Their `provenance.caveats`
list what the prose could not supply and where it contradicts itself.
