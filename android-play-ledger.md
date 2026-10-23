# Android play ledger — does the game play better on the phone?

One row per `scripts/profile_android.sh` run, in **`android_play_cost.jsonl`**.
Each row says how fast the game played on the phone in that run: frames per
second, frame-time percentiles, the population at the end, and where the CPU
time went. Rows compare only with rows of the same **`comparable_key`** (same
phone, Android release, game, boot route, autoplay, cargo profile, hub games
and profiler setup). The table below has one section per key, oldest run first.

Writer: `scripts/lib/android_profile_ledger.py` (its docstring states the
contracts). Raw bundles: `profiles/<record_id>/` (UNTRACKED, local to the
machine that recorded them). Readable half: `summaries/<record_id>.md`
(TRACKED, copied from the bundle's `android-profile-summary.md` on append).

## Add a run

`scripts/profile_android.sh` adds its own run at the end (not fatal). To add a
bundle by hand, from the main repo:

```bash
python3 scripts/lib/android_profile_ledger.py append dev/ambition_dev_measurements/profiles/android-<name>
```

It skips a `record_id` that is already in the ledger. It refuses (exit 3) a
bundle with no `[frame-census]` window that starts at t >= 30 s: that run never
reached gameplay. Then refresh the table on this page:

```bash
python3 scripts/lib/android_profile_ledger.py table
```

A run comparable with the series uses the same capture as the rows below:

```bash
scripts/profile_android.sh all --profile-build --launch \
    --launch-env AMBITION_BOOT_ROUTE=mary_o_gameplay --launch-env AMBITION_AUTOPLAY=mary_o \
    --warmup 25 --duration 30
```

(`ambition_gameplay` / `AMBITION_AUTOPLAY=ambition` for Ambition.) Keep
`--duration 30`: a longer capture covers more of the route (see below).

## How to read a row

- **FPS median** — the median over windows of (frames / window seconds). The
  game prints one `[frame-census]` window every 5 s. Only windows that start at
  t >= 30 s (app clock) count; the earlier windows are boot and load.
- **FPS 30–90 s** — the same, over the windows that start in [30 s, 90 s) only.
  A `--duration 30` capture covers that span. Use it when two rows have a
  different window count.
- **p50 / p95 / p99 ms** — the median over windows of each window's percentile.
  **worst ms** — the slowest single frame in any counted window.
- **our code %** — the share of CPU samples whose leaf frame is in
  `libambition_app.so` (the game, Bevy, every Rust crate), counted exactly from
  `simpleperf-device-samples.txt`. The rest is the kernel, the GPU driver and
  libc. **CPU cores busy** — `simpleperf stat` cpu-clock / wall time.
- **Δ FPS** — against the previous row in the same section only.

⚠ Every row has simpleperf at 99 Hz attached during the window span (the
profiler setup is in the key). `gfxinfo` is not read: it reports 0 frames for
this app. The GPU clock is not readable on this phone without root
(`gpufreq_after` is null). Every row has `Thermal Status: 0` before and after,
and no CPU cluster capped below its hardware max (`cpufreq_*.any_capped`).

## The 24 h system-consolidation run, 10-22 → 10-23

The 10-22 runs and the 10-23 runs bracket it: start `d9cf597cf`/`315d36bf0`,
end `6f7be4ffe` (Mary-O) / `a4a22629e` (Ambition), 116 commits from
`315d36bf0` to `a4a22629e`. Same phone, same capture, same key. MEASURED from
the rows below:

| game | before | after | change |
|---|---|---|---|
| Mary-O (autoplay) | **40.7 FPS** (`315d36bf0`; `d9cf597cf` gave 42.1) | **49.6 FPS** (`6f7be4ffe`) | **+22%** (+18% against 42.1) |
| Ambition (autoplay) | **39.1 FPS** (`d9cf597cf`) | **49.6 FPS** (`a4a22629e`) | **+27%** |
| Ambition, 30–90 s only | 39.4 FPS | 47.9 FPS | **+22%** |

Also MEASURED: the slowest window improved more than the median (Mary-O
31.9 → 40.9 FPS, Ambition 13.8 → 22.7), and the worst frame fell (Mary-O
91.9 → 78.8 ms, Ambition 240.4 → 93.1 ms).

⛔ **The two Ambition rows do not cover the same route.** The 10-22 run is a
60 s capture: **28 windows**, t = 30–171 s. The 10-23 run is a 30 s capture:
**13 windows**, t = 30–95 s. In the 10-22 run the autoplay route goes around
twice: the windows with ~7,100 entities (the heavy stretch) come at 30 s and
again at 105–121 s, at 14–23 FPS. The 30–90 s line above is the like-for-like
comparison; it gives +22%, not +27%.

⚠ **One run per arm, and the phone has no measured noise floor.** The only
repeat in the series is the two 10-22 Mary-O runs, 7 commits apart: 42.1 and
40.7 FPS, 3.4% apart. That is one pair, not a floor (`profiling-lessons.md`
§3 asks for >= 5 reps). The +22% is ~6x that pair's spread, so the direction
is safe; the exact size is not. REASONED: the 10-23 runs started hotter
(dumpsys cached CPU max 66–68 °C against 61 °C), so heat did not make them
faster.

⚠ The workload also changed: the Ambition run ends with 2,908 entities against
1,955, and the Mary-O run with 2,695 against 3,822 (one sample at the end of
each run). Population is not held constant across commits.

## Older rows

- `android-all-maryo-*` (10-16, 10-17): early runs of the same Mary-O route.
  `maryo-dormant` and `maryo-terrain` used `--warmup 50 --duration 60` (32
  windows); compare them on **FPS 30–90 s**.
- `game=unknown` sections: the build printed no `[census] config` row and the
  script recorded no boot route, so the route is not known. The script name
  says Mary-O 1-1, but a name is not evidence. `maryo-1-1-morning` (58.5 FPS)
  and `maryo-1-1-morning3` (25.9 FPS) are the same commit 11 minutes apart:
  they did not run the same thing, so do not read the delta.
- `hub=mary_o` (`android-maryo-only-*`): a build with only Mary-O composed.
  A different composition, so a different key.
- Refused (never reached a window at t >= 30 s): `android-all-20261017T225305Z`,
  `android-all-ambition-auto-20261017T230735Z`, `…T231814Z`,
  `android-all-mary_o-auto-20261022T202425Z`, `…T202500Z`.
- The older tarball-only bundles (`android-all-202610{11,14,15}*`,
  `android-fast*`, `android-slow`, `android-prepare-*`) have no frame census.

## Current table

Generated by `python3 scripts/lib/android_profile_ledger.py table` on 2026-10-23.

### Pixel 5 (Android 14) · game=ambition · route=ambition_gameplay · autoplay=ambition · android-profile · hub=all · all@99Hz

`comparable_key` `ec43c26fdf60` · 2 run(s)

| run (UTC) | git | windows | FPS median | Δ FPS | FPS min window | FPS 30–90 s | p50 ms | p95 ms | p99 ms | worst ms | entities at end | our code % | CPU cores busy |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 2026-10-22 20:56<br>`android-all-ambition-auto-20261022T205617Z` | `d9cf597cf` | 28 | 39.1 | — | 13.8 | 39.4 | 25.2 | 40.4 | 61.1 | 240.4 | 1955 | 72 | 1.58 |
| 2026-10-23 20:37<br>`android-all-ambition-auto-20261023T203742Z` | `a4a22629e` | 13 | 49.6 | +10.5 (+27%) | 22.7 | 47.9 | 19.8 | 34.0 | 39.4 | 93.1 | 2908 | 69 | 1.99 |

### Pixel 5 (Android 14) · game=mary_o · route=mary_o_gameplay · autoplay=mary_o · android-profile · hub=all · all@99Hz

`comparable_key` `2ba13c09cb82` · 6 run(s)

| run (UTC) | git | windows | FPS median | Δ FPS | FPS min window | FPS 30–90 s | p50 ms | p95 ms | p99 ms | worst ms | entities at end | our code % | CPU cores busy |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 2026-10-16 22:57<br>`android-all-maryo-auto` | `c4e82f09f` | 13 | 21.5 | — | 13.7 | 20.3 | 37.6 | 60.1 | 80.6 | 2053.7 | 5771 | 79 | 2.86 |
| 2026-10-17 02:08<br>`android-all-maryo-dormant` | `09b25738b` | 32 | 36.9 | +15.3 (+71%) | 28.1 | 32.6 | 25.9 | 38.9 | 44.7 | 113.6 | 3250 | 76 | 1.74 |
| 2026-10-17 03:40<br>`android-all-maryo-terrain` | `049a9081e` | 32 | 34.0 | -2.9 (-8%) | 14.9 | 29.7 | 29.1 | 43.3 | 50.1 | 881.3 | 2682 | 78 | 1.69 |
| 2026-10-22 20:52<br>`android-all-mary_o-auto-20261022T205211Z` | `d9cf597cf` | 13 | 42.1 | +8.1 (+24%) | 21.0 | 41.1 | 22.4 | 34.7 | 40.6 | 1300.5 | 2732 | 82 | 1.96 |
| 2026-10-22 21:37<br>`android-all-mary_o-auto-20261022T213742Z` | `315d36bf0` | 13 | 40.7 | -1.4 (-3%) | 31.9 | 40.7 | 23.7 | 37.6 | 43.9 | 91.9 | 3822 | 75 | 1.90 |
| 2026-10-23 20:27<br>`android-all-mary_o-auto-20261023T202721Z` | `6f7be4ffe` | 13 | 49.6 | +8.9 (+22%) | 40.9 | 49.6 | 19.9 | 30.2 | 36.4 | 78.8 | 2695 | 73 | 2.10 |

### Pixel 5 (Android 14) · game=mary_o · route=mary_o_gameplay · autoplay=off · android-profile · hub=all · all@99Hz

`comparable_key` `41a67051f0aa` · 1 run(s)

| run (UTC) | git | windows | FPS median | Δ FPS | FPS min window | FPS 30–90 s | p50 ms | p95 ms | p99 ms | worst ms | entities at end | our code % | CPU cores busy |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 2026-10-16 15:29<br>`android-all-maryo-boot` | `1d7c34b9b` | 14 | 33.9 | — | 24.1 | 32.2 | 28.2 | 48.5 | 64.5 | 129.5 | 3234 | 72 | 1.73 |

### Pixel 5 (Android 14) · game=unknown · route=unknown · autoplay=unknown · android-profile · hub=all · all@99Hz

`comparable_key` `35281b324f2f` · 3 run(s)

| run (UTC) | git | windows | FPS median | Δ FPS | FPS min window | FPS 30–90 s | p50 ms | p95 ms | p99 ms | worst ms | entities at end | our code % | CPU cores busy |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 2026-10-16 04:21<br>`android-all-maryo-1-1` | `97ff2541e` | 9 | 27.6 | — | 20.6 | 25.2 | 34.8 | 52.1 | 69.0 | 185.6 | — | 73 | 1.66 |
| 2026-10-16 13:19<br>`android-all-maryo-1-1-morning` | `f66b3a8aa` | 12 | 58.5 | +31.0 (+112%) | 33.9 | 58.5 | 17.0 | 24.4 | 27.8 | 398.1 | — | 62 | 1.76 |
| 2026-10-16 13:30<br>`android-all-maryo-1-1-morning3` | `f66b3a8aa` | 13 | 25.9 | -32.6 (-56%) | 19.8 | 26.4 | 35.9 | 60.5 | 78.7 | 344.5 | — | 72 | 1.48 |

### Pixel 5 (Android 14) · game=unknown · route=unknown · autoplay=unknown · android-profile · hub=mary_o · all@99Hz

`comparable_key` `04f65434376c` · 2 run(s)

| run (UTC) | git | windows | FPS median | Δ FPS | FPS min window | FPS 30–90 s | p50 ms | p95 ms | p99 ms | worst ms | entities at end | our code % | CPU cores busy |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 2026-10-16 04:29<br>`android-maryo-only-1-1` | `97ff2541e` | 6 | 18.6 | — | 16.1 | 18.6 | 51.5 | 74.5 | 87.5 | 135.2 | — | — | — |
| 2026-10-16 04:47<br>`android-maryo-only-1-1b` | `11130fb49` | 13 | 19.8 | +1.2 (+7%) | 13.8 | 19.9 | 49.7 | 76.7 | 89.5 | 176.3 | — | 74 | 1.39 |

