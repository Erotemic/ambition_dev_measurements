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

## room_population.jsonl

**All 72 shipped rooms, booted and counted** — one row per room, written
2026-08-29 from `capture_scene <room> player <out> 320x240 --warmup 180` with the
runtime census on. Counts only, deliberately: this host has no GPU and software-
rasterizes, so every wall-time number it produces is suspect, but **a COUNT cannot
be contaminated by rasterization**. That is what made the question answerable here
at all.

⭐ **What it settled.** A runtime-efficiency campaign had recorded, from a
four-room sample, that *"no shipped room exceeds 46-87 visible sprites"* and
concluded the game's founding performance premise — *a room with hundreds of
sprites chugs* — was false. Sweeping all 72 rooms shows the room exists:
`mockingbird_arena` reaches **283 sprites / 277 visible**, 3x the next room.
Four rooms were not the population.

⭐⭐ **But the peak is an EVENT, not a room, and that is the transferable part.**
Sampled at 20Hz, `mockingbird_arena` sits at 34-35 visible sprites, ramps to
**295 visible**, and collapses back to 35 — the whole excursion lasts about one
second. Every earlier sample missed it because they all photographed rooms AT
REST. A steady-state census cannot see a one-second barrage, and the rows in this
file (warmup 180) catch some rooms mid-spawn for the same reason: **treat the
sprite columns as an upper bound sampled at one moment, not as a steady state.**

**What the burst costs** (same run, same resolution, arms straddling the event,
intervals with `frames>=5` only):

| | visible sprites | mean | p95 | worst frame |
|---|---|---|---|---|
| baseline | 35 | 7.42ms | 8.46ms | 11.66ms |
| burst | 277-295 | 7.78ms | 9.50ms | **17.30ms** |

8x the sprites for **+0.36ms of mean (4.9%)** — 1.40us per visible sprite, which
independently matches a +36-sprite probe's 3.89us. ⇒ **the sprites are real and
cheap, and the damage is in the TAIL** (worst frame past the 16.67ms budget at
60Hz), not in throughput.

⭐ **The one room that is genuinely expensive is expensive in SIMULATION.**
Measured on the sim-tick census, which the GPU-contamination caveat does not
reach:

| room | bodies | live entities | WorldPrep |
|---|---|---|---|
| `goblin_encounter` | 1 | 1919 | 0.269ms |
| `basement_npcs` | 4 | 2238 | 0.353ms |
| `duel_arena` | 4 | **1732** | 0.354ms |
| `basement_enemies` | 9 | 2524 | 0.431ms |
| `hall_of_characters` | **130** | 3858 | **2.373ms** |

**`WorldPrep` scales with BODIES, not entities, and the matched pair proves it:**
`duel_arena` and `basement_npcs` both have 4 bodies, differ 30% in live entity
count, and agree to **one microsecond**. ⇒ **budget ~16us of simulation per
body.** `hall_of_characters` is a gallery showing every character at once; no
gameplay room exceeds 9 bodies, so at 16us/body this funds no optimisation work.
It is a design constant, not a defect.

⛔ **A caution about the `entities_allocated` column.** It is Bevy's
`Entities::len()`, which counts ALLOCATED slots and lands on exact powers of two
— 2048 where the live population is 1599. The census now prints `live=` beside
it. Do not read this column as a population.

⭐⭐ **And the finding most likely to redirect architecture work: Smash's frame
spikes are not in the simulation.** A 4000-tick match, 340 intervals split into
quartiles by worst frame, gives a sim total of 0.837ms (calm) vs 0.849ms (spiky)
— **+1.4%** — while the frame max moves **4.64 -> 8.28ms**. Every individual
gameplay phase moves by at most 4 MICROseconds. ⇒ making the simulation cheaper
cannot remove the spikes. It remains correct for throughput (the sim is 0.84ms of
a 4.2ms frame) and is worth approximately nothing for responsiveness. What the
spike actually is stays open, and needs a host with a GPU to answer.
