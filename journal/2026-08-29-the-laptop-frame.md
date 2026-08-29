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

| | baseline (median of 3) | after | speedup |
|---|---|---|---|
| p50 | 51.045ms | **18.467ms** | **2.76x** |
| p95 | 82.65ms | **30.31ms** | 2.73x |
| mean | 53.33ms | **20.08ms** | 2.66x |

**About 19.6 FPS to about 54 FPS**, and for the first 100 seconds of the run the
per-second windows sat at **16.6–17.0ms — vsync-capped 60 FPS.**

All four runs share one comparability key,
`windowed:default@v1/profiling/hardware/no-tracy/087907b3…`, so the ledger
itself treats them as one series. The measured run-to-run spread on this host is
~7% end to end; the effect is 176%.

Runs: `desktop-timeline-run-20260829T214154Z`, `…215121Z`, `…220508Z` (baseline)
and `…223455Z` (after).

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

- ⚠ **n=1 against n=3.** One treated run against three baselines. A 176% effect
  against a 7% floor does not need more reps to be believed, but the arms were
  not interleaved and a second run costs a minute.
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
- **The scaled asset variants are ~11 days stale** and
  `backgrounds/parallax_layers_0_5x` did not exist at all, so Medium was not a
  safe tier to test on this machine — the raster knobs were made independent of
  the tier specifically to avoid that confound. Regeneration is in progress.
- **Tracy crashes the game on this host.** Three Tracy-enabled runs segfaulted
  between 25s and 32s (`perf-record.status=139`); the `--no-tracy` runs are
  stable and one ran 12 minutes. Tracy is correctly installed and version-matched
  and the CPU has an invariant TSC, so this is not a setup gap. Undiagnosed.

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
