# The laptop frame — four pixels drawn where one was displayed

**Host: `calculex`** (i7-7700HQ, Intel HD 630, no working discrete GPU — see
`hardware.md`). Campaign ran 2026-08-29, immediately after the desktop-hitch
handoff, on different hardware and against a different question.

**The question, in Jon's words:** *"I want to make sure the game runs smoothly
without a GPU if we don't have one. It should not be required."*

That is not the question the previous campaign asked. `toothbrush` has an RTX
3090 and its conclusion — *"the GPU is not the problem; the transparent 2D pass
is ~0.047ms"* — was true there and is the first thing that had to go here.

---

## The result

**n=3 against n=3, matched on build features** (both arms `cargo_features=profile`):

| | baseline | treated | speedup |
|---|---|---|---|
| median p50 | 51.045ms | **20.101ms** | **2.54x** |
| runs | 51.045 / 49.050 / 52.624 | 19.400 / 20.801 | |

**~19.6 FPS to ~49.7 FPS.** In the best run the first 100 seconds of per-second
windows sat at **16.6–17.0ms — vsync-capped 60.**

⚠ A fourth treated run, `…223455Z`, is FASTER at 18.467ms and is quoted nowhere
above: it was `--no-tracy`, so it also dropped Tracy's client from the binary and
is not feature-matched to the baselines. See "what this does not establish".

Baselines `…214154Z` `…215121Z` `…220508Z`; treated `…232350Z` `…233357Z`
(and `…223455Z`, unmatched).

## What was wrong

**The game rasterised four pixels for every one it displayed.** A 1600x900
window on a 2x Wayland session gets a 3200x1800 framebuffer, and nothing capped
it. On top of that, MSAA sat at Bevy's default 4x — antialiasing geometry edges
for a game whose geometry is axis-aligned textured quads — which also adds a
full-screen `msaa_writeback` pass across all of those pixels.

Neither is visible on a discrete GPU. Both are the whole frame on integrated
graphics.

⭐ **The counts proved it before any timing was trusted**, which is why the
diagnosis survived a machine that had never been profiled before:

| instrument | baseline | after |
|---|---|---|
| `render/upscaling/fragment_shader_invocations` | 5,760,000 (= 3200x1800) | **1,440,000** (= 1600x900) |
| `render/msaa_writeback/*` rows | present | **0 — the pass is gone** |

Those are exact integers, not measurements. They say the knobs fired; the
milliseconds only say what it was worth.

## What was changed

`VisualQualityBudget` gained a `RasterBudget` — `max_scale_factor` and
`msaa_samples` — resolved per tier, plus `AMBITION_MAX_SCALE_FACTOR` /
`AMBITION_MSAA` to move either one alone, and `ambition.local.toml` (git-ignored,
with a committed template) so a machine can declare its own answer without
anybody else's checkout changing.

⛔ **High and Ultra are byte-for-byte what they were**: `max_scale_factor: None`
honours the compositor and `msaa_samples: 4` is Bevy's own default. A test exists
whose only job is to fail if that stops being true. `max_scale_factor` is a CAP —
it never raises a scale factor and does nothing at all on a 1x display.

## What this does NOT establish

- ✔ **n=1 against n=3 — CLOSED 2026-08-30 by two more treated runs**
  (`…232350Z` p50 19.400, `…233357Z` p50 20.801). See the correction below.

- ⛔ **THE HEADLINE COMPARISON HAD A CONFOUND, and the two new runs are what
  removed it.** The three baselines were built `cargo_features=profile`; the
  first treated run was `--no-tracy`, i.e. `cargo_features=<none>`. So it also
  dropped Tracy's client from the binary, and the ledger did not catch it: the
  comparability key records whether Tracy CAPTURED, not whether it was COMPILED
  IN, so all four rows landed in one `no-tracy` class and looked directly
  comparable.

  With features matched, the result stands and is barely smaller:

  | | runs | median p50 |
  |---|---|---|
  | baseline, `features=profile` | 51.045 / 49.050 / 52.624 | **51.045ms** |
  | treated, `features=profile` | 19.400 / 20.801 | **20.101ms** |
  | treated, `--no-tracy` | 18.467 | — |

  **2.54x on matched builds, ~19.6 to ~49.7 FPS.** The `--no-tracy` run being
  the fastest of the three is consistent with the Tracy client costing something
  even when nothing is listening, which is its own reason not to link it for a
  measurement.

  ⚠ Treated spread across all three is 12%, wider than the ~7% baseline floor.
- ⚠ **The two knobs moved TOGETHER.** The scale cap and MSAA-off were set in the
  same config, so nothing here says how the 2.76x splits between them. Both
  variables are individually settable precisely so that experiment can be run;
  it has not been.
- ⚠ **The window resized mid-run**, 1600x900 to 3840x2036 (7,818,240 fragments —
  Jon maximised it). The later per-second windows are a bigger framebuffer, not
  a slower one, and are not comparable to the earlier ones. The headline numbers
  are the run's own aggregate and include both.
- ⛔ **Nothing here is a claim about a real GPU.** These are HD 630 numbers.

## What is still open

- **Transparent overdraw, still ~5.3x.** `main_transparent_pass_2d` reported
  41,482,624 fragments against a 7,818,240-pixel framebuffer, from roughly 21–37
  visible sprites. That shape — very few sprites, enormous fragment counts —
  means a handful of full-screen stacked transparent layers, and
  `main_opaque_pass_2d` reports **zero** fragments, so nothing is being drawn
  opaque and nothing can be depth-rejected. This is the largest remaining lever
  and the first one that is real engine work rather than a setting.
- **Which knob earned the 2.76x.** One interleaved A/B, three reps each.
- ✔ **The scaled asset variants were ~11 days stale** and the parallax tiers did
  not exist at all. Regenerated 2026-08-29 (backgrounds 3.5s, 998 sprite sheets
  175s); `check_quality_variants_are_fresh.py` now reports current, and
  `run_developer_setup.sh` runs that check rather than assuming.

- ✔⭐ **"Tracy crashes the game on this host" — RETRACTED, and the real cause is
  worth more than the symptom.** Three Tracy-enabled runs segfaulted at 25–32s
  (`perf-record.status=139`) while `--no-tracy` runs were stable, which looked
  like a Tracy problem and was not. Every one of those crashing binaries was
  compiled INCREMENTALLY at `release` optimization; `.cargo/config.toml` sets
  `build.incremental = true` for every profile. The same profile later failed to
  LINK with 164 then 330 `anon.<hash>.llvm.<hash>` undefined symbols, which is
  the same defect louder. Since `run_game.sh` began exporting
  `CARGO_INCREMENTAL=0` for optimized profiles (`143d37a96`), Tracy-compiled runs
  complete — `…232350Z` and `…233357Z` are both `cargo_features=profile` and
  neither crashed.
  ⛔ SO THE EARLIER HYPOTHESIS — that the Tracy client was buffering a trace
  nobody was capturing until it exhausted memory — was wrong, and plausible
  enough to have been chased for a day. A binary that links and then misbehaves,
  and a binary that fails to link, can be the same broken codegen.

## For whoever picks this up

1. `render/upscaling/fragment_shader_invocations` is the framebuffer's pixel
   count, exactly. It is the cheapest true thing in the bundle: it tells you
   whether a resolution change took before you read a single millisecond.
2. ⭐ **A count survives what a timing cannot.** Every load-bearing step of this
   campaign — the 4x pixels, the 5.3x overdraw, the MSAA pass disappearing — was
   an exact integer. The host had never been profiled, the noise floor was
   unknown, and Tracy was crashing; none of that touched the counts.
3. `hardware.md` and `profiling-lessons.md` were written by an agent on other
   hardware. Jon, on this: *"an agent wrote it, so it isn't gospel."* Treat them
   as evidence, not authority.
