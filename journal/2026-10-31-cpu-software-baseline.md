# CPU rendering baseline

Host: `aivm-2404`, machine ID `7ae3c588a0604d4f92fb18166d860c86`.
This KVM guest has a Xeon E5-2620 v4 at 2.10 GHz, 30 logical CPUs,
149.35 GiB RAM, and no hardware GPU. See [hardware.md](../hardware.md).

## Result

The Hall ran at a mean **63.09 ms per frame**, about **15.85 FPS**, after
the first 30 seconds of game time. Software rendering used **65.55%** of
sampled CPU cycles across the full capture. The sprite watcher is a separate
cost that continues during gameplay. This is the first baseline for this
machine. It does not establish a regression from recent content changes.

The measured source was clean commit
`7cfee82449d1e359e4da7f1ce9c981ef53012269`. Both captures used the optimized
`profiling` build without the `profile` feature. No compiler ran during either
capture. The selected local art generation was
`45b7b6dba4e1de8be4b5b2c6a4339f9931f2339bccb3760cdb1e307931e9f6a4`.

## Workload and frame cost

The game rendered through llvmpipe under Xvfb at 1600x900. The quality profile
was Potato, with one MSAA sample, no parallax, and FIFO presentation. Direct
entry selected `hall_of_characters`. The player stayed at the entry position.
A screenshot confirmed visible character art. The scene had one session,
262 bodies, one player, and one camera that rendered the world. There were no
active portal captures. This measures the entry view of a large scene, not a
tour of every character or room.

Run: [`desktop-cpu-potato-hall-baseline-20261031`](../summaries/desktop-cpu-potato-hall-baseline-20261031.md).
The raw bundle is local under `profiles/` with the same name.

| interval, game time | frames | mean ms | weighted window p50 ms | weighted window p95 ms | worst ms |
|---|---:|---:|---:|---:|---:|
| full capture | 1744 | 64.1408 | 61.8641 | 103.8957 | 478.99 |
| at least 30 s | 1339 | 63.0893 | 60.5659 | 101.8477 | 144.72 |
| at least 60 s | 850 | 63.0346 | 60.7632 | 101.7634 | 144.72 |

These percentiles are means of per-window percentiles, weighted by frame
count. They are not whole-session percentiles. The settled mean uses the same
weights on `frame_times.csv`, with rows selected by `t >= 30`. One run does not
measure run-to-run variance. Do not use it to claim small performance changes.

The final census reported 11,397 live entities. The value 16,384 in the
generated summary is entity capacity, not the live population. The scene had
6,535 sprites, with up to 238 marked visible. Images reached 417 and then
stayed there. Decode work totaled 28.6 MP, or 114.3 MB of RGBA data; this is
not a measurement of process memory or GPU memory. The largest arrival window
contained 416 images near game time 5 s. Continued decoding does not explain
the settled frame cost. No audio device was available.

Reproduce the baseline from the repository root:

```bash
AMBITION_QUALITY_PROFILE=potato \
VK_DRIVER_FILES=/usr/share/vulkan/icd.d/lvp_icd.json \
xvfb-run -a -s '-screen 0 1600x900x24' \
scripts/profile_desktop.sh --no-tracy --duration 120 --jobs 6 \
  --name cpu-potato-hall-baseline-20261031 \
  -- -- --start-room hall_of_characters
```

Use a new run name for a repeat. The runtime ledger contains one new row, in
comparability group `01012cc5a5adea01`.

## Where the cost is

The baseline contains 108,563 native samples and reports zero lost samples.
Self time partitions the capture: JIT shaders 54.94%, Vulkan software driver
10.61%, game binary 14.33%, kernel 16.28%, and libc 3.69%. These are sampled
cycle shares across all threads, not shares of frame wall time.

A second process ran the same scene and settings. A 30-second `perf-attach`
capture started after gameplay began, with DWARF stacks at 99 Hz. It contains
28,470 samples, zero reported losses, and no frame-history row. Its local
bundle is `profiles/desktop-cpu-potato-hall-native-20261031`. The driver and JIT
share was 65.05%. Selected inclusive stack shares were:

| stack | sampled cycles | interpretation |
|---|---:|---|
| `sprite_reload::Scanner::look` | 5.67% | background sprite watch during settled gameplay |
| `ContentLibrary::selection` / `Selection::read` | 3.11% | publication reads inside the watch |
| `ContentLibrary::files` | 2.80% | file-list construction inside the watch |
| `integrate_sim_bodies` | 1.76% | body integration on the main thread |
| `collect_perception_peers` | 1.00% | perception collection on the main thread |
| `MoveSpec::frame_data` | 0.74% inclusive, 0.41% self | full frame-data construction; reach queries are a caller |

Inclusive rows overlap. Do not add them. Some stacks stop at unknown native
frames. The renderer JIT has no pass names in perf.

The first optimization candidate is the watch. `Scanner::look` reads
`library.selection()` and then `library.files()`, which reads the selection
again and builds the file map. Both parse and path work appear in the sampled
stacks. Check why scans continue with unchanged content, then retain the
accepted selection until a publication change requires a new one. Keep content
reload and the current nonfatal handling of absent products.

The second candidate is perception. `threat_reach_of` calls
`MoveSpec::frame_data()` for each selected move. That function allocates active
spans and cancel windows although this caller only needs reach. Derive stable
move facts when content is prepared, or provide a direct shared reach query.

The largest open target remains rendering. Up to six transient offscreen
cameras render impostor atlases and their unpremultiply passes; their largest
target dimension is 576 pixels. The main world renders once. The engine already
culls direct parts outside the view and reuses unchanged atlas pages. Active
cameras alone do not prove that impostors dominate. Measure the total work of
these passes and the main view before changing the representation. Do not
change the Hall to make the measurement faster.

## Limits and excluded work

The phase census warns `untrustworthy=render_blocking`. Its apparent PreUpdate
share does not establish a simulation hotspot. The render diagnostics do not
give a complete sum across all views, so their last reported pass values cannot
explain the full frame. A complete per-view render breakdown remains open.

The first Tracy attempt never reached a room: it recorded zero bodies and zero
sessions. It is excluded from the runtime ledger and from these gameplay
results. Its local bundle is `desktop-cpu-potato-autoplay-tracy-20261031`.
Tracy CSV export also timed out. Eight absent local character targets were
generated with the existing sprite publisher, then direct Hall entry confirmed
gameplay before the two captures above. The missing-target reports were
nonfatal; they did not establish why the first attempt stayed in startup.

The October 18 desktop and CPU-rendered rows used different hardware, quality
settings, and an autoplay route. Their peak population was 159 bodies. They do
not support a timing regression percentage against this 262-body entry scene.
An earlier verification launch overlapped compilation; its timings are also
excluded. No production optimization was made in this campaign.

Open: measure rendering by view, remove repeated watch work, and reduce repeated
move-data construction. Reuse this machine, scene, settings, and instruments
for each before/after comparison. Add repeats before making a small-delta claim.
