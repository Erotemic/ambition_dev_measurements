# ambition_dev_measurements
This is a repo that helps keep measurement bloat out of the main repo

## Start here

- **`hardware.md`** — the machines these numbers were taken on, and **which
  conclusions travel to other hardware and which do not**. ⛔ Every number in this
  repo belongs to a machine; read this before quoting one.
- **`profiling-lessons.md`** — the measurement rules, each one bought with a
  published number that turned out to be wrong. Read before designing a probe.
- **`journal/`** — one file per campaign: what was chased, what was retracted, and
  what is still open. `journal/README.md` says how to keep one.

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

## `summaries/` and `profiles/` — how to read a row back

⭐ **A ROW NAMING A BUNDLE NOBODY CAN FIND IS HALF A RECORD.** Every
`runtime_frame_cost.jsonl` row carries a `record_id` and a `bundle.path`:

- **`profiles/<record_id>/`** — the raw bundle: `perf.data`, `tracy.trace`, every
  CSV. **UNTRACKED** (gigabytes; see `.gitignore`) and local to whichever machine
  recorded it.
- **`summaries/<record_id>.md`** — the readable half, a few KB, **TRACKED**. This
  is what a reader on another machine actually gets, and it is written by
  `scripts/profile_desktop.sh` on every run.

⚠ **`tracy_zone_instances.csv` IS PRUNED AFTER INGEST** — it reached **90.6 GB** in
one bundle and filled a 484 GB disk. Nothing reads it once
`tracy_zone_report.py` has reduced it to per-window tables; regenerate with
`tracy-csvexport --unwrap tracy.trace`. A `.removed` note in the bundle says so.

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

⭐⭐⭐ **THE HEADLINE, FOR ANYONE PRICING WORK AGAINST THIS DATA: SMASH IS NOT SLOW, ⛔⛔ **QUALIFIED 2026-08-29 AFTER THE FIRST HARDWARE RUN, AND THE ORIGINAL WORDING OVERSOLD IT.** Everything behind this headline was measured HEADLESS (`NoWindow`, software rasterizer, no GPU). The honest claim is **"the headless CPU side of a Smash match is not slow on this host"** — it does NOT explain a desktop that feels slow, because it never measured real rendering, presentation, VSync or frame pacing. ⭐ The first windowed run on an RTX 3090 (`desktop-timeline-run-20260829T143608Z`) shows exactly the gap: mean **7.77ms**, p99 **12.50ms**, and **24 spikes over 33.4ms with a worst frame of 516ms** across 28,291 frames. ⚠ Read it with three caveats IN the row: Tracy was on and the profiler is **13.5% of cycles**; the tree was dirty; and it is `windowed:default` with ONE player, **not a Smash match**, so it is not comparable to the 4.31ms headless Smash figure. ⇒ what the headless campaign legitimately ruled out stands (sim, system count, rollback, sprite population, entity population); what it could never see is now the whole remaining question. ⛔ AND ONE 'RULED OUT' ITEM IS OVERTURNED BY THAT RUN: *"Smash has exactly one world-rendering camera"* was a HEADLESS fact. The windowed run reports `world_rendering_peak: 3`, `offscreen_peak: 2` and portal capture targets at **2048x512 and 512x2048**.
AND ITS FRAME SPIKES ARE NOT THE ENGINE.** On a quiet machine a 2-fighter match
runs a **4.31ms mean against a 16.67ms 60Hz budget** — nearly 4x headroom — with
**ZERO of 5,164 match frames over budget** (worst 10.56ms). The tail is CONTENTION:
six busy loops on the box take frames over 8ms from **0.9% to 11.8% (13x)** while
the median moves 6.8%. ⛔ Several "dropped frame" readings taken during this
campaign turned out to be the measuring session's own concurrent builds.

⇒ **no fundable frame-time lever was found, and that is a measurement rather than a
shrug** — a dozen-plus hypotheses were tested and the levers that exist sit below
the noise floor or inside code this repo does not author. What the campaign
produced instead is the COST MODEL, the OWNERSHIP MAP and the instrument rules
below.

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
4.42 / 4.52 / 4.55 / 4.62 / 4.43ms — mean **4.508ms**, range **4.4%**.

⛔⛔ **THAT SINGLE BLOCK WAS NOT ENOUGH, AND RE-RUNNING SAID SO.** Two more blocks
the same hour, same binary: **22.6%** over 3 reps (one run at 5.24) and **7.4%**
over 8. Typical spread is **4–7%** with occasional single runs ~20% above the
median. ⇒ **use the MEDIAN of ≥5 reps; the defensible bar is ~7%, about 0.3ms**,
which at 6.9us/system is roughly **45 systems' worth of work**.

⛔⛔ **AND THE BLOCK MEAN ITSELF DRIFTS — 4.508ms vs 4.305ms minutes apart,
nothing changed.** That 4.7% is as large as most effects worth finding, so **two
arms measured in different blocks cannot be compared even with reps each.
INTERLEAVE them.** ⛔ A two-arm A/B
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

⭐⭐⭐ **A FIGHT COSTS PHYSICS, NOT COMBAT LOGIC — and this is the cleanest
measurement in the set.** It uses the only strong design available on a noisy host:
an INTERLEAVED within-run A/B. Combat VFX make the entity population rise and fall
repeatedly (137 rises, 166 falls over 433 samples), so quiet and busy frames can be
compared inside ONE process — immune to both the ±20% single-run outliers and the
~5% drift between measurement blocks.

Quartile-split by population, n=401 intervals:

| phase | quiet | combat | delta |
|---|---|---|---|
| **`RunFixedMainLoop`** (fixed-step / physics) | 0.321 | 0.409 | **+0.088 — 58%** |
| `PostUpdate` | 0.519 | 0.546 | +0.027 |
| `Update` | 1.244 | 1.269 | +0.025 |
| `PreUpdate` | 1.964 | 1.974 | +0.010 |
| **whole gameplay sim** | 0.837 | 0.840 | **+0.003** |
| of which `Combat` | 0.191 | 0.195 | **+0.004** |

⇒ **combat costs ~4 MICROSECONDS in the phase named after it, while costing ~150us
in the frame.** What a fight actually costs is collision and physics work.

⛔ **AND DO NOT DIVIDE THE FRAME DELTA BY THE ENTITY COUNT.** +251us over +24
entities gives "10.3us per VFX entity" — 3x the whole-frame average — because the
entities are a SYMPTOM of combat, not its cause. Entity count is a proxy for
combat activity here. (Fourth time in this campaign that dividing by an entity
count produced a wrong answer.)

⭐ This is the third independent route to the same conclusion — the spikes are not
in the sim, a fighter is only a third sim, and now a fight moves the sim by 0.4%.
**Optimising gameplay systems is not where this engine's frame lives, even when
gameplay is the busiest thing on screen.**

⭐⭐⭐ **AND THE SPIKES THEMSELVES ARE ANSWERED: THEY ARE CONTENTION, NOT THE
ENGINE.** ~15,800 frames per arm, absolute thresholds (a "3x median" threshold is
useless here — it MOVES when load raises the median):

| arm | median | >8ms | >12ms | >16.67ms | worst |
|---|---|---|---|---|---|
| idle | 3.20ms | 9 (**1.1%**) | 2 (0.2%) | **0 (0.0%)** | 13.66ms |
| 6x busy loop | 3.35ms | 87 (**9.5%**) | 7 (0.8%) | 1 (0.1%) | **34.86ms** |

Frames over 8ms become **8.6x more common under CPU contention while the median
moves 5%** — the signature of scheduling, since engine work would raise the median
along with the tail. ⭐⭐ **On an idle machine, ZERO of 15,747 frames exceeded the
16.67ms budget.**

⛔⛔ **AND THE CAUTIONARY HALF: the "dropped frames" recorded earlier in this
campaign were the measuring session's OWN concurrent builds and probes.** A 22.95ms
worst frame and a noise-floor block that read 22.6% instead of ~5% both dissolve
once the machine is quiet. ⇒ **RECORD MACHINE LOAD BESIDE EVERY FRAME NUMBER** —
without it, a tail measurement is partly a measurement of whoever else is on the
box.

⭐⭐ **And the finding most likely to redirect architecture work: Smash's frame
spikes are not in the simulation.** A 4000-tick match, 340 intervals split into
quartiles by worst frame, gives a sim total of 0.837ms (calm) vs 0.849ms (spiky)
— **+1.4%** — while the frame max moves **4.64 -> 8.28ms**. Every individual
gameplay phase moves by at most 4 MICROseconds. ⇒ making the simulation cheaper
cannot remove the spikes. It remains correct for throughput (the sim is 0.84ms of
a 4.2ms frame) and is worth approximately nothing for responsiveness. What the
spike actually is stays open, and needs a host with a GPU to answer.

## `ladder_scenarios.jsonl` — the CPU ladder's placement fixtures, 45 seeds

`ladder_rig --scenarios --seeds 45`, 60s bouts, 20 rows (5 fixtures x 4 rung
pairs). Recorded 2026-08-29 to settle whether three "vacuous" rows were noise.

**They are not.** At 3x the seed count the same 3 of 16 recovery rows come back:
`recovery_right` 3v1, `recovery_below` 3v1, `recovery_below` 6v5. A vacuous row is
one where BOTH seats end at **0.0% peak damage** — neither fighter was ever
touched, because neither recovered from the fixture's drop.

⭐ **What the fixture measures is not what its name suggests.** It places SELF past
a blastzone and the FOE at mid-height — which is mid-AIR — so every row is a
two-fighter recovery stress test. 13 of 16 rows have both fighters recover and
fight on to 130–160%.

⛔ **Two readings of this data are refuted by the data itself**, and both are worth
not repeating:

- *"a skill effect — weak CPUs fail"*: `recovery_below` is **non-monotonic in
  rung** (vacuous at 3v1, engaging at 5v3, vacuous at 6v5, engaging at 9v6). No
  skill gradient does that.
- *"a left/right asymmetry"*: `recovery_right` dies at 3v1 but reaches 151%/129%
  at 5v3 **from the identical placement**, so geometry is not it either.

⛔⛔ **SCHEMA 2, SAME DAY — AND IT CORRECTS THE PARAGRAPH THIS REPLACED.** The v1
rows carried a `vacuous` flag derived from the rig's MEDIANS, and the outcome is
BIMODAL: a bout either ends untouched or becomes a real fight, so a stable 50/50
split gives a stable median too. `unfought_bouts` (both seats under 1% damage) is
now recorded per row, and it splits one apparent defect into two:

| fixture | 3v1 | 5v3 | 6v5 | 9v6 |
|---|---|---|---|---|
| `recovery_left` | 0/45 | 0/45 | 0/45 | 0/45 |
| `recovery_right` | **23/45** | 0/45 | 0/45 | 0/45 |
| `recovery_below` | **45/45** | 0/45 | **45/45** | 0/45 |
| `recovery_above` | 0/45 | 0/45 | 0/45 | 0/45 |

⇒ `recovery_right` 3v1 is a **coin flip** (51%) whose median fell on the unfought
side by one bout. `recovery_below` is **45/45 or 0/45, never between** — a
deterministic total failure at two rung pairs.

⭐⭐ **REPRODUCIBILITY IS NOT DETERMINISM.** Re-running at 3x the seeds returned the
same three rows and I read that as "not noise". It is not SAMPLING noise — but
more seeds only sharpen the estimate of a 51% rate, and a 51% rate produces the
same median every time. Only the DISTRIBUTION tells a coin flip from a certainty.

⭐ The count also finds unfought bouts hiding inside rows that read as ordinary
fights: `ledge_trap` 3v1 is 3/45 behind a 119%/103% median.

## `ladder_recovery_sweep.jsonl` — rollout causes the l6 recovery regression

`ladder_rig --sweep-below [--no-rollout] --seeds 45`. Only the level placed BELOW
the stage varies; the partner is pinned at l5, so one variable moves.

| level below | l1 | l3 | l5 | l6 | l9 |
|---|---|---|---|---|---|
| rollout ON (shipped) | 45/45 | 0/45 | 0/45 | **45/45** | 0/45 |
| rollout OFF | 45/45 | 0/45 | 0/45 | **0/45** | 0/45 |

⭐⭐ **Turning rollout off rescues l6 completely and changes nothing else.** l1
still fails, which is what makes it a CONTROLLED result rather than a global
perturbation — l1's failure is its reaction time and noise, and the flag does not
touch that.

⇒ `for_level` switches `rollout_depth`/`rollout_k` on at **level ≥ 6**. Rollout is
not universally harmful — l9 carries the same depth-12 rollout and recovers — so
it costs something a fighter can only afford once reaction and noise have
improved, and at l6 it arrives too early.

⛔ **A ladder where rung 6 survives worse than rung 5 is a real quality defect, and
a win-rate ladder cannot see it:** l6 still beats l5 in an ordinary bout. It just
dies to the stage.

⚠ `unfought_bouts` counts bouts where BOTH seats ended under 1% damage. In the
pairwise `--scenarios` table that conflates "both failed to recover" with "one
failed, so there was no fight" — which is why the pairwise rows looked
non-monotonic. Sweeping ONE level removes the ambiguity.

⚠ Only levels **1/3/5/6/9** are published as `duelist_l{n}` policies; asking for
`l2` refuses the seat and the rig's `placed` assert stops the run.
