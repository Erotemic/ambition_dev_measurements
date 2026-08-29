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

⭐ **The frame, fully attributed** (4.45ms, 2 fighters, windowless, same host).
Worth having because it prices each direction BEFORE anyone funds one:

| block | ms | share |
|---|---|---|
| marked gameplay sim (17 phases) | 0.83 | 19% |
| GGRS rollback driver overhead | **0.21** | 5% |
| `PreUpdate` outside the driver | **0.93** | 21% |
| `Update` | 1.23 | 28% |
| `PostUpdate` | 0.51 | 11% |
| `RunFixedMainLoop` | 0.31 | 7% |
| First / StateTransition / SpawnScene / Last | 0.31 | 7% |

Two things fall out. **The rollback driver is cheap** — 0.21ms, because
`check_distance: 0` skips saving entirely; do not go hunting rollback cost here.
And **`PreUpdate` is not "the simulation tick"**: the sim is 0.83 of its 1.98ms.
But the 0.93ms remainder is BREADTH, not a hot spot — 0.93ms over ~135 systems is
**6.9us each**, and `Update` is 1.23ms over 521 systems (**2.4us each**), both
inside the independently measured 2.9-15.6us per-system band. ⇒ **the only lever
that moves a frame shaped like this is retiring a WHOLE CLASS at once**, which is
why the campaign's one measured win hoisted a condition that gated a whole set
(83 evaluations per run to 1) rather than optimising any system.

⭐⭐ **AND THE ONE THING THAT SCALES WITH A PLAYER'S CHOICE: FIGHTER COUNT.** Every
other number in this campaign came back flat. 1200-tick arms, both keeping their
cast, frame **4.58 → 4.83ms** for 2 → 4 fighters:

| phase | delta | share of the delta |
|---|---|---|
| `PreUpdate` | +0.104ms | 42% |
| `PostUpdate` | +0.078ms | 31% |
| `Update` | +0.066ms | 26% |

The gameplay sim accounts for +0.086ms of it. ⇒ **a fighter costs about a third in
simulation, a third in presentation, a quarter in `Update`** — which is what a
fighter is: a sprite rig plus a brain plus combat state. ⛔ **Optimising only the
sim addresses a third of a fighter.** And a fighter is NOT "a body": at ~125-240us it
is several times the ~16us/body constant, so do not price one with the other.

⭐ **AND A FIGHTER IS ALREADY LEAN: 8 ENTITIES AND 1 SPRITE.** Measured at the
instant the round goes live, before any combat — `entities_at_go_live` 1297 at two
fighters vs 1313 at four, and `sprites_at_go_live` 25 vs 27, both with ZERO
variance across reps. (Later in the same run combat VFX swing the entity count by
±40, which is larger than the whole fighter delta; ⭐ **when the noise is an
ACTIVITY, sample before the activity starts.**)

⇒ that closes the obvious lever before anyone funds it: **"give the pipeline fewer
entities" has almost no room at 8, and "draw fewer sprites" has none at 1.** The
~39-80us a fighter adds to `PostUpdate` is ~5-10us per fighter entity — an ordinary
per-entity cost — so fighter entities are EXPENSIVE, not NUMEROUS, and the only
remaining presentation lever is making each one cheaper (shallower hierarchy,
fewer components, less change-detection churn) inside a pipeline this repo does
not author.

⇒ **the cost sheet, for pricing a feature before building it:** ~4.5ms baseline
(2-fighter match, ~630 systems, no hot one) · **~125-240us per fighter** · ~16us per
body · ~1.4us per visible sprite · a tail spike of +3.6ms that is **not** the sim.

⭐⭐ **WHO OWNS THE SIMULATION — the datum for a decomposition.** The sim schedule
(`GgrsSchedule`) could not be enumerated at all until 2026-08-29, because the
schedule census ran at `PreStartup` and the sim schedule does not exist until a
session activates. Once sampled after activation: **545 systems across 29
crates.**

| crate | sim systems | share |
|---|---|---|
| **`ambition_platformer2d_actor_monolith`** | **162** | **30%** |
| `ambition_content` | 62 | 11% |
| `ambition_combat` | 52 | 10% |
| `ambition_demo_mary_o` | 33 | 6% |
| `ambition_sim_view` | 30 | 6% |
| `ambition_demo_sanic` | 25 | 5% |
| `ambition_platformer2d_runtime` | 25 | 5% |
| `ambition_dev_tools` | 22 | 4% |

**The monolith is 30% of the simulation** — "it is big" as a number a plan can be
built against. For contrast `Update` spreads 497 systems over 46 crates, led by
`ambition_render` at 99, so the sim is far more concentrated than the frame.

⛔ **AND A WARNING FOR WHOEVER READS THIS AS A COST TABLE: IT IS NOT ONE.** 63 sim
systems (12%) belong to experiences a Smash match is not — `mary_o`, `sanic`,
`twintrack`. Removing FOUR whole experiences was measured directly in this
campaign and moved **neither frame time nor startup registration**. ⇒ they already
pay proportionally to what they do. A system COUNT is an ownership fact; this
campaign's repeated result is that counts do not predict cost.

⭐⭐ **WHAT EFFECT SIZE IS EVEN DETECTABLE HERE — measure this before designing a
probe.** Five back-to-back 2000-tick 2-fighter runs, same binary, same host:
4.42 / 4.52 / 4.55 / 4.62 / 4.43ms — mean **4.508ms**, range **4.4% of the mean**.

⇒ **the smallest defensible single-arm win on this host is ~0.2ms**, which at the
measured 6.9us/system is about **30 systems' worth of work**. ⛔ A two-arm A/B
carries roughly DOUBLE that uncertainty, because subtracting two noisy quantities
amplifies relative error — a ±0.04ms wobble on a 0.53ms phase is 8%, and on the
0.08ms DIFFERENCE of two such phases it is 50%. **An absolute measurement here
needs one careful run; a DELTA needs at least three per arm.**

⚠ This corrected a rule the campaign had been applying: a "~15% noise floor" had
been used to derive "no group of fewer than ~500 systems can produce a measurable
win". At the measured floor that threshold is ~30 systems — too pessimistic by
more than an order of magnitude. ⭐ Re-checking every candidate group against the
tighter bar resurrected NONE of them (falling sand's plugin removal moved nothing;
asset trackers are <=0.145ms; ui focus + picking is 0.00) — but the rule itself
would have wrongly dismissed a 30-system gate proposed later.

⚠ **A caveat that cost a retracted finding:** these splits are valid ONLY from a
run with no render backend. `[census] phases` attributes wall time between
schedule markers, so GPU blocking lands in whichever phase brackets it — raising
a render target 320x240 to 1280x960 once took `StateTransition` from 0.169 to
1.822ms, a phase full of state machinery scaling with PIXELS.

⭐⭐ **And the finding most likely to redirect architecture work: Smash's frame
spikes are not in the simulation.** A 4000-tick match, 340 intervals split into
quartiles by worst frame, gives a sim total of 0.837ms (calm) vs 0.849ms (spiky)
— **+1.4%** — while the frame max moves **4.64 -> 8.28ms**. Every individual
gameplay phase moves by at most 4 MICROseconds. ⇒ making the simulation cheaper
cannot remove the spikes. It remains correct for throughput (the sim is 0.84ms of
a 4.2ms frame) and is worth approximately nothing for responsiveness. What the
spike actually is stays open, and needs a host with a GPU to answer.
