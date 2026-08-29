# The machines these measurements were taken on

⛔⛔ **EVERY NUMBER IN THIS REPO BELONGS TO A MACHINE. A ROW WITH NO HOST IS NOT A
MEASUREMENT.** The bundle summaries record `host`, CPU model, logical CPU count
and RAM automatically (`scripts/lib/profile_bundle_summary.py`), so the host is
always recoverable from `summaries/<run>.md`. This file says what those names
MEAN, and — the part that does not fit in a table — **which conclusions travel to
other hardware and which do not.**

## The registry

| name | what it is | CPU | logical CPUs | RAM | GPU | role |
|---|---|---|---|---|---|---|
| `toothbrush` | bare metal, Jon's desk | i9-11900K @ 3.50GHz | 16 | 131.7 GB | RTX 3090, driver 595.84, Vulkan | **windowed** profiling; the only host that has produced a real frame |
| `aivm-2404` | a VM **guest on `toothbrush`**| i9-11900K @ 3.50GHz (same silicon) | 12 | 65.8 GB | none | agent work; **headless** benchmarks only |

⭐ **THE TWO ARE THE SAME CPU.** `aivm-2404` is a guest on `toothbrush`, so it
sees the same cores at the same clock, just twelve of the sixteen threads and
half the RAM. That is why CPU-bound headless conclusions from the VM have held up
on the desk — and it is exactly why they must stop being trusted the moment
somebody runs this on a different chip.

⚠ **`toothbrush` IS A FAST DESKTOP CPU.** An i9-11900K at 3.5GHz with a 3090 is
near the top of what this game will ever run on. **A frame budget that looks
comfortable here is not evidence about a laptop, a Steam Deck, or a phone.** Jon
said this himself while the first hardware profile was still being written, and
it is the single most load-bearing caveat in this file.

## What transfers to other hardware, and what does not

**Transfers — these are counts and structure, not timings:**

- **How many images arrive, and how big.** 424 images / 655.9 MP / 2623.7 MB in
  one four-minute session; ~7 sheets of 4096x4096 per character, ~117 MP and
  ~470 MB of decoded RGBA for a single character. A slower machine decodes the
  same pixels; it just takes longer.
- **When work arrives relative to gameplay.** "This character was demanded when
  its body spawned rather than at match preparation" is a structural fact. It is
  wrong on every machine and worse on a slow one.
- **Population counts** — `live=` entity counts, system counts, schedule
  membership, resident image counts.
- **The direction of an A/B** on the same host, if the arms were interleaved.

**Does NOT transfer — re-measure before believing any of it:**

- ⛔ **Every absolute millisecond.** mean 7.77ms / p50 7.54 / p95 9.89 / p99 12.50,
  the 516ms worst frame, `prepare_assets<HitFlashMaterial>` at 80.1µs — all of
  these are `toothbrush` numbers.
- ⛔ **The noise floor.** Measured on this host at 4.4%, 22.6% and 7.4% in three
  blocks **within one hour**. It belongs to the machine AND the moment.
  Re-measure it per host, from >=5 reps, before designing any probe.
- ⛔ **Whether a thing is "fast enough".** The whole reason the desktop hitch was
  found late is that the mean frame was healthy here.
- ⛔ **The CPU/GPU balance.** The transparent 2D pass measured ~0.047ms on a 3090.
  On integrated graphics the bottleneck may not be the CPU decode at all, and the
  conclusion "the GPU is not the problem" would have to be re-established.
- ⛔ **Anything measured under a VM's core count.** `aivm-2404` has 12 threads
  against `toothbrush`'s 16; parallel decode and the Bevy task pools scale with
  that.

## If you are profiling on a NEW machine

1. Add a row to the registry above **before** you publish a number from it.
2. Measure the noise floor first: >=5 back-to-back runs of the unchanged binary,
   take the median, and quote the spread you actually saw. It costs a minute and
   it calibrates every comparison after it.
3. Keep the arms of any A/B **interleaved** (A,B,A,B). The block mean on
   `toothbrush` drifts ~5% between blocks minutes apart, which is as large as
   most effects worth finding.
4. Write a journal entry (`journal/`) naming the host in its first line.
5. ⭐ **State plainly which of the conclusions above you re-established and which
   you inherited.** An inherited number that silently becomes a local one is how
   a fast machine's comfort gets published as a fact about the game.
