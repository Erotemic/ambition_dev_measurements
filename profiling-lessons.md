# Profiling lessons, paid for in wrong answers

Every rule here was bought with a published number that turned out to be wrong.
They are written as rules because the reasoning that produced each mistake felt
completely sound at the time — that is the point. Read this before designing a
probe, not after a surprising result.

Host context for every number quoted below: see `hardware.md`.

---

## 1. ⭐⭐⭐ A probe needs TWO checks, and the second one is what catches you

**Every one of six lying instruments in this campaign was caught by a SECONDARY
number in the same row, never by the primary.**

| the primary looked fine | the number that caught it |
|---|---|
| frame unchanged after "removing" falling sand | `ChunkRegion=1024` still in the population census |
| a clean probe result | `max=40.66ms` and a missing driver row — 44 frames, not the 300 that row needs |
| a flat sprite-scaling curve | `sprites_visible` stuck at 6 |
| `system_conditions=0` | `systems=886` printed beside it |
| `StateTransition systems=0` | `[census] schedules` saying 8 |
| `0 occurrences in GgrsSchedule` | that census **emits no GgrsSchedule row at all** |

⇒ **A CENSUS MUST REPORT POPULATION BESIDE TIMING.** A timing is exactly as
convincing when it is wrong.

⇒ **A COUNT OF ZERO FROM AN INSTRUMENT THAT NEVER REPORTS THAT CATEGORY IS NOT A
MEASUREMENT — it is the instrument's silence wearing a number.** Before believing
a zero, ask the instrument for a case you KNOW is present.

## 2. ⭐⭐⭐ Run a new instrument where it should report NOTHING, first

Two instruments written in one day both reported plausible garbage on their first
real invocation, and neither defect was visible from reading the code:

- a late-decode flag scoped to "was gameplay live" **fired on 53 of 53 decodes**,
  because in a play-through gameplay is live almost always. True and useless.
- a cache-hit counter using `map_or(0, ..)` printed `hits=0 loads=0` both when the
  cache was **absent** and when it was present and never asked.

⇒ **A new instrument's first output is its first test, and the useful test case
is the EMPTY one.** Print `unavailable=<reason>` rather than a plausible zero.

⇒ And scope it to something that can actually be FALSE. "During gameplay" is not
a scope in a game. "A rostered fighter still resolving after the opening bell" is.

## 3. ⛔⛔ The noise floor is not a constant, and an assumed one propagates into rules

A floor of "~15%" was **assumed**. From it came a derived rule — *"no group of
fewer than ~500 systems can produce a measurable win"* — which was then quoted to
dismiss work and hardened into three documents.

Measured, five back-to-back runs of the unchanged binary: **4.4%**. The threshold
became ~30 systems, not 500. Wrong by more than an order of magnitude, in the
direction that dismisses opportunities.

**Then re-running it gave 22.6%, after the 4.4% was already published.** Three
blocks within one hour, same binary, same host: **4.4%** (5 reps) · **22.6%**
(3 reps) · **7.4%** (8 reps).

⇒ **Take the MEDIAN of >=5 reps and quote the looser typical spread (~7% here),
not the best block you happened to get.** Measure it BEFORE designing a probe.

⭐ The tell that should have prompted it earlier: the same quantity was stated
three different ways in one document (~15%, 13%, ~10-15%). **A constant nobody
measured drifts, because there is nothing to anchor it.**

## 4. ⛔⛔ Interleave the arms — the block mean drifts

Two blocks minutes apart, nothing changed: **4.508ms and 4.305ms, 4.7% apart** —
as large as most effects worth finding.

⇒ **Never compare an arm measured in one block against an arm from another, even
with reps each. Interleave them (A,B,A,B)** so the drift lands on both.

⇒ **An absolute measurement needs one careful run; a DELTA needs at least three
per arm.** Subtracting two noisy quantities amplifies relative error: a ±0.04ms
wobble is 8% of a 0.53ms phase and 50% of the 0.08ms difference between two.

### The within-run A/B that looked free and was not

A 4-player match loses players to knockouts, so the population changes inside one
process — an apparently free within-run comparison. **It is invalid, because the
sequence is MONOTONIC**: `4 → 3 → 2 → 1`, each value one contiguous block. Every
high-count interval preceded every low-count one, so the comparison is
time-ordered and any drift is confounded with the variable.

⭐ **The data convicted itself:** the `2` bucket cost MORE than the `3` bucket.
When a monotonic quantity produces a non-monotonic response, the noise is at
least as large as the effect.

⇒ The test is not "same run", it is **"do the two states ALTERNATE, with the
baseline on BOTH sides?"** A countdown, a warm-up, a fill or a leak never does.

## 5. ⛔⛔ Tracy is not free, and Bevy 0.18 has no per-system span

Tracy accounted for **13.5% of cycles in one run and 18.7% in another**, emitting
4,018 zones/frame for a 1.1GB trace.

⇒ **Relative attribution from a Tracy-on run is fine. NEVER quote a timing claim
from one** — and never compare a Tracy-on mean against a Tracy-off one, which is
how two runs here became falsely comparable.

⛔ **Tracy cannot split a phase per system.** `bevy_ecs`'s executors emit exactly
one `info_span!` (`check_conditions`) plus an executor-wide span. The routes to
per-system cost are: the `check_conditions` proxy (which times a system's
CONDITION, not its body), hand-rolled boundary systems around a set you already
suspect, or a patched Bevy.

⭐ What Tracy CAN find is self-instrumented libraries — egui was the only named
per-system cost in the whole frame, because egui instruments itself.

## 6. ⛔⛔ `[census] phases` attributes wall time, so it measures the GPU

Same room, same warmup, 16x the pixels:

| | 320x240 | 1280x960 |
|---|---|---|
| `StateTransition` | 0.169ms | **1.822ms** |
| frame mean | 7.33ms | 16.19ms |

A phase containing nothing but state machinery, scaling with PIXELS — because
wall time between schedule markers absorbs whatever blocks inside them. A whole
"StateTransition is 14% of a real room's frame" finding was built on that and
**retracted**.

⇒ **Phase splits are valid ONLY from a run with no rendering** (`NoWindow`).

⚠ `fragment_shader_invocations = 0` does NOT make it safe. Submission and
upscaling cost real time even when the opaque pass shades nothing. That zero was
the reassuring secondary number that made a wrong conclusion feel verified.

## 7. ⭐⭐ Put the qualifier IN the row, not on its own line

A profiling harness printed `WARNING: no seats remain` and `seats_at_end=0`: the
measured window had outlived the match, and post-match frames cost roughly HALF
what match frames cost. **It fired exactly as designed.** Then the run was
analysed with `grep '\[census\] frame'`, which kept the numbers and dropped the
warning — and a mean 27% low was published.

⇒ **If a number is only valid under a condition, the condition belongs in the
ROW.** A separate warning line survives only until someone filters for the
values, which is the first thing anyone does with a long instrument output. The
fix here was a `measured_window_live_cast=36%` token beside the numbers.

## 8. ⛔⛔ Vary the input YOU chose before investigating the subject

A capture tool produced an image that was nothing but sky. It was filed as
"writes a BLANK image and reports success" — a real-sounding bug of exactly the
class this codebase warns about — and four eliminations were spent on the
SUBJECT: not timing, not the engine, not the route, not the spawn point.

⭐ **The bug was the argument that had been typed.** `320x240`, a size copied from
an unrelated command. At the tool's default the level renders fine.

⇒ **Before investigating what you are measuring, re-run with the knobs YOU set at
their defaults and at a second value.** Anything you typed is a hypothesis too,
and it is the cheapest one to test. ⛔ A wrong bug report is worse than none.

## 9. ⛔ A headless conclusion does not answer "the desktop feels slow"

The overnight headless series (3.185 → 2.866 → 2.816 ms) was real and it still
stands. It was never an answer to the desktop question, because it measured a
**single-room match whose cast loads once, before the window opens.**

⇒ **Anything about hitches needs PLAY ACROSS ROOMS, windowed.** The 516ms frame
that started this whole campaign turned out to be the **character gallery**, not
a match — match entry was 162ms. A headless single-room benchmark could not have
contained it at all.

## 10. ⭐ Read the row you already have before building a new one

Four of six open rows in one review were closed by `grep` alone, and the
**headline count at the top of a ledger is reliably its stalest part**. A recorded
blocker is a note somebody left, not a fact: re-measure it. "Reachable" is not
"reached".

## 11. ⭐ Reproducible is not deterministic

A median over a bimodal outcome is stable at 51% and tells you nothing. **Print
`n/N`.** A sample is not the population, and a peak may be a transient — a COUNT
survives what a TIMING cannot.

---

## 12. ⛔⛔ The summary statistic can be the bug

`[census] sim_phases` emitted a `ticks=1` startup window whose every phase read
`0.000` — not because the phases were free, but because almost nothing had run
between the census opening and its first due time.

The obvious summary of a run is "the last few windows". A 1200-tick capture
produces about three:

```text
(0.000 + 0.341 + 0.332) / 3 = 0.224
```

**0.224 was published as the hall's `Decide` cost against a true 0.341**, and the
same bias sat under a population curve, a density sweep and three A/B
decompositions — worst at low populations, where runs are shortest and the zero
is the largest share of the mean, so every slope came out too STEEP.

⛔⛔ **AND A CORRECTION WENT THE WRONG WAY FIRST.** Seeing `tail -1` report 0.341
against a three-window mean of 0.234, I concluded the single window was the
anomaly and wrote it up as a lesson about trusting the stated method. The stated
method was the bug.

⭐ Fixed at the source (`90c896564`): a window under two ticks is no longer
emitted, and its time folds into the next one rather than being discarded.
`ticks=1` was always in the row and three analyses failed to filter on it — **a
row that cannot be read correctly is worse than a row that is absent, because
absence is visible.**

⚠ Scope it before applying it: `[census] frame` never had this. Its first window
already holds hundreds of frames, so boot frames are diluted inside it. Every
`frame p50` in this repo stands; only the per-phase column moved.

## 13. ⛔⛔ Compare arms in ONE binary, not two builds

Twice in one campaign a conclusion was withdrawn because a baseline came from a
binary two fixes older than the number subtracted from it. Read an environment
variable at the call site, branch to the old path or the new one, interleave the
reps, and delete the switch before committing.

It also removes: different compiler decisions between builds, a different machine
load an hour apart, and a warm-versus-cold page cache. `Integrate` drifted 4%
between two runs of the SAME build — a 5% "win" across builds is noise wearing a
result's clothes.

⚠ Discard the first pair. Early reps ran slow in both arms until the machine
settled; only reps 3-5 separated cleanly.

⚠ And pick a statistic that holds still. Worst-frame over a burst is BIMODAL —
the same bound produced 49 ms and 275 ms on consecutive runs — while total spike
time separated the same arms at 0.3% spread.

## 14. ⛔ Check the UNITS before dividing

`sprite_area` is world units squared. Dividing it by a 1280x720 pixel count and
reporting "15.8x coverage" is dimensionally invalid, and it was published in a
journal entry, a queue row and a tool's usage text within twenty minutes.

The census's own doc comment said so: *"WORLD UNITS, NOT PIXELS, AND THE NAME
SAYS SO. Turning these into screen pixels needs each sprite's view and that
view's projection."* The ratio survives — *"a ratio does not care about the
unit"* — the multiple does not.

⚠ Second time that day an instrument's existing comment said the right thing and
was read past; the first was sizing unsized sprites from their image, which
`rendering/parallax.rs` explains at the spawn site is wrong.

## The shape of the desktop hitch, as established on `toothbrush`

Kept here because it is the conclusion the next campaign starts from, and because
every number in it is host-bound.

- Steady state is **healthy**: mean 7.77ms, p50 7.54, p95 9.89, p99 12.50 over
  28,291 frames.
- **24 frames over 33.4ms, worst 516ms**, arriving in five clusters 1-2s wide.
- Every cluster lands on an image-decode burst, **monotone in megapixels**:
  +72MP → 296ms, +128MP → 162ms, +307MP → 516ms.
- The GPU is **not** the problem: the transparent 2D pass is ~0.047ms.
- The cost is **asset preparation**, and specifically the EXTRACT step —
  `extract_render_asset<GpuImage>`, 454.9ms max against a 0.1ms mean. Decode
  itself is already async on the IO pool. **"Decoded" is upstream of "ready to
  draw", and only the second one lands on a frame.**
- ⚠ The 516ms frame was the **character gallery**, not a match. Match entry was
  162ms. The headline "entering a match hitches" was imprecise from the start.
