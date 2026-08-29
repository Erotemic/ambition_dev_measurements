# The machines these measurements were taken on

⛔⛔ **EVERY NUMBER IN THIS REPO BELONGS TO A MACHINE. A ROW WITH NO HOST IS NOT A
MEASUREMENT.** The bundle summaries record `host`, CPU model, logical CPU count
and RAM automatically (`scripts/lib/profile_bundle_summary.py`), so the host is
always recoverable from `summaries/<run>.md`. This file says what those names
MEAN, and — the part that does not fit in a table — **which conclusions travel to
other hardware and which do not.**

## The registry

⛔⛔ **`aivm-2404` IS A GUEST NAME, NOT A MACHINE.** It has denoted at least two
different physical hosts, on two different CPU generations, and the hostname did
not change when the silicon did. **Identify a VM row by `host.machine_id`, never
by the name.** The ledger's `comparable_key` already includes `machine_id`,
`cpu_model` and `logical_cpus`, so the tooling will refuse to compare across
them — but PROSE does not, and this file said "the same CPU" for a day after it
had stopped being true.

| name | `machine_id` | what it is | CPU | logical CPUs | RAM | GPU | role |
|---|---|---|---|---|---|---|---|
| `toothbrush` | `5776eb09…` | bare metal, Jon's desk | i9-11900K @ 3.50GHz | 16 | 131.7 GB | RTX 3090, driver 595.84, Vulkan | **windowed** profiling; the only host that has produced a real frame |
| `aivm-2404` **@toothbrush** | `ec9af5ee…` | a VM guest on `toothbrush` | i9-11900K @ 3.50GHz (same silicon) | 12 | 65.8 GB | none | ⚠ **RETIRED / not currently reachable.** Produced the overnight headless series |
| `calculex` | *(pending a run)* | bare metal, Jon's **laptop** | **i7-7700HQ @ 2.80GHz** (Kaby Lake, 2017) | **8** | 32.4 GB | **Intel HD Graphics 630 (KBL GT2)**, Mesa 23.2.1, Vulkan 1.3 — ⚠ **integrated**; an NVIDIA ICD is listed but `nvidia-smi` fails, so there is no working discrete GPU | **windowed** profiling on slow hardware. Ubuntu 22.04, Wayland |
| `aivm-2404` **@calculex** | `716a275c…` | a KVM guest **on `calculex`** | i7-7700HQ @ 2.80GHz (same silicon) | **6** of its 8 | **15.6 GB** of its 32.4 | none | agent work as of 2026-08-29; **headless** benchmarks only |

⛔ **THE HEADLESS VM IS NO LONGER THE SAME SILICON AS THE DESK.** The claim this
file used to lead with — that CPU-bound headless conclusions from the VM held up
on `toothbrush` because it was the same chip — was true of `ec9af5ee…` and is
**false of `716a275c…`**. A 6-thread i7-7700HQ at 2.8GHz is roughly half the
threads and a slower core than an i9-11900K. Nothing measured on `@kaby`
inherits the desk's numbers, in either direction.

⚠ **The overnight headless series (3.185 → 2.866 → 2.816 ms) belongs to
`ec9af5ee…`.** It is still a valid record of that machine. It is **not** a
baseline the current VM can continue: re-running that scenario here and putting
the result next to those three numbers would be a machine-crossing comparison
wearing the shape of a trend. Start a new series, and say which host it is on.

⭐⭐ **`calculex` IS THE MACHINE THIS FILE HAS BEEN ASKING FOR.** The caveat
below — that a budget comfortable on a 3090 and an i9 is not evidence about a
laptop — has never had a laptop to check against. It does now, and the laptop is
a 2017 quad-core on **integrated Intel graphics**.

⛔ **That makes one inherited conclusion actively untrustworthy here, and the
file already says which one:** *"the GPU is not the problem — the transparent 2D
pass is ~0.047ms."* That was a 3090. On HD 630 the CPU/GPU balance has to be
re-established from scratch before any part of the desktop-hitch model is
carried over. The decode-and-extract story may well hold; the reason it was safe
to ignore the GPU does not.

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
- ⛔ **Anything measured under a VM's core count.** The current agent VM
  (`716a275c…`) has **6** threads against `toothbrush`'s 16 — the retired one had
  12. Parallel decode and the Bevy task pools scale with that, so the same
  workload has three different amounts of parallelism across the three rows
  above.

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
