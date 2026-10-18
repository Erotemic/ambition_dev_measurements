# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `135a5077cbab` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.95.0 (59807616e 2026-04-14)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --jobs 6 --autoplay ` |
| host | `aivm-2404` |
| kernel | `Linux aivm-2404 6.8.0-110-generic #110-Ubuntu SMP PREEMPT_DYNAMIC Thu Mar 19 15:09:20 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `ambition-autoplay` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=12
- MemTotal:       65841160 kB

## Renderer

```text
AdapterInfo { name: "llvmpipe (LLVM 20.1.2, 256 bits)", vendor: 65541, device: 0, device_type: Cpu, device_pci_bus_id: "", driver: "llvmpipe", driver_info: "Mesa 25.2.8-0ubuntu0.24.04.2 (LLVM 20.1.2)", backend: Vulkan, subgroup_min_size: 8, subgroup_max_size: 8, transient_saves_memory: false }
```

**SOFTWARE RENDERING — READ THIS BEFORE THE NUMBERS BELOW.**

This run had no GPU: every pixel was rasterized on the CPU. Expect the
bulk of samples in llvmpipe/lavapipe threads and in unsymbolized `[JIT]`
frames, which are the rasterizer's runtime-compiled shaders -- perf can
never attribute those to a pass, a material, or a draw call. Game-code
symbols in this profile describe only the few percent left over.

This is NOT a measurement of GPU rendering performance, and it must not
be reported as one. Check `host-environment.txt` for why no GPU adapter
was selected (missing ICD, `VK_*`/`WGPU_*` override, no DRM render node,
headless session).

## Session

Observed span of the game's own log: **121.4s**.

## Frame time

109 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     14.2      15   105.6    55.9   147.7   702.2    702.2
     16.4       5   224.8   209.0   414.2   414.2    414.2
     19.6       6   187.7   170.0   283.8   283.8    283.8
     17.5       6   186.0   207.8   270.3   270.3    270.3
     69.5       7   165.3   148.9   265.1   265.1    265.1
     44.8      10   126.7   108.1   258.4   258.4    258.4
     48.9       6   179.1   181.3   253.1   253.1    253.1
     15.3       5   213.5   220.6   250.2   250.2    250.2
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    4.052s     146.3 ms
    4.316s     141.2 ms
    5.249s     133.0 ms
    5.115s      81.4 ms
    4.986s      80.7 ms
    5.629s      77.1 ms
    5.313s      65.4 ms
    4.175s      65.1 ms
    4.110s      58.1 ms
    4.531s      57.8 ms
```

Full list: `frame_spikes.csv`.

## Room reveal

Placeholder rectangles drawn (an actor resolved no sprite), counted over the whole run. The hall reveal fired one hundred and eleven of these on 2026-09-01 and they were the campaign's main evidence:

```text
  never materialized      0   nothing decoded its sheet
  retired                 0   decoded, then dropped by a quality change — a re-realization owed, not art nobody asked for
  undeclared              0   no loaded content declares the name (typo, or art not published)
  total                   0
```

Room transitions, and how long the cover held for art:

```text
  seq  wait_ms  covered  move
    1      139     True  central_hub_complex -> hall_of_characters
    2       89     True  hall_of_characters -> central_hub_complex
```

Frames over 33.4 ms AFTER the last transition was logged (t=47.536s): **0**.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

⛔ **THE SPIKE LOG HIT ITS 60-LINE CAP, so this count is a FLOOR, not a total.** Percentile summaries continued; the per-frame lines did not.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 660 images / 102.7 MP in total; 14 were notable enough to print.

```text
ordered by: frame
before central_hub_complex: 4 image(s), 20.5 MP
  unknown                  2    16.4 MP
  vanity-card              1     3.0 MP
  character-sheet          1     1.1 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.5        5       3      1       0      1
      5.6        5       3      1       0      1
     10.7        5       3      1       0      1
     16.4        5       3      1       0      1
     21.7       11       7      1       4      1
     27.0       11       5      1       2      1
     32.2       11       7      1       4      1
     37.4       11       9      1       6      1
     42.5       11       7      1       4      1
     47.9        7       5      1       2      1
     53.2        7       5      1       2      1
     58.5        5       3      1       0      1
     64.0        7       5      1       2      1
     69.5        7       5      1       2      1
     75.2        5       3      1       0      1
     80.5        7       5      1       2      1
     85.9        7       5      1       2      1
     91.1        7       5      1       2      1
     96.5        7       5      1       2      1
    101.8        5       3      1       0      1
    107.0        7       5      1       2      1
    112.6        7       5      1       2      1
```

Peak world-rendering cameras: **1** at t=0.5s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
       offscreen  rigged impostor camera, rigged impostor unpremultiply camera
           other  Cube pause camera, Cube scrim display camera, Developer Panel Camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=0.5s.

```text
        t  rigs  active  budget
      0.5     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
      9.7     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     19.6     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     29.1     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     38.4     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     47.9     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     57.4     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     67.3     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     77.2     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     86.9     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     96.5     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
    106.0     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
    115.6     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 576px) at t=19.6s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1645        0        0
       end      16384        2232        3        1
      peak      16384        2232      159        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **4692** (479 visible), text2d 165, per-view projections 35 at t=37.4s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3755** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         4.334         26.347      109  render/ui/elapsed_gpu
         4.138         11.242      109  render/upscaling/elapsed_gpu
         1.322          5.828       91  render/main_opaque_pass_2d/elapsed_gpu
         0.299          3.093      109  render/main_transparent_pass_2d/elapsed_gpu
         0.038          0.082       91  render/main_opaque_pass_2d/elapsed_cpu
         0.037          0.088      109  render/ui/elapsed_cpu
         0.004          0.024      109  render/upscaling/elapsed_cpu
         0.002          0.022      109  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    827448.119    4750926.000      109  render/ui/fragment_shader_invocations
    118491.429     331776.000       91  render/main_opaque_pass_2d/fragment_shader_invocations
       751.486       1492.000      109  render/ui/vertex_shader_invocations
       374.716        744.000      109  render/ui/clipper_primitives_out
       374.716        744.000      109  render/ui/clipper_invocations
         4.000          4.000       91  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       91  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       91  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      109  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      109  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      109  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      109  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (4 spans).
- GPU pass timings: **measured** (4 spans).
- Pipeline statistics: **measured** (12 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
--no-tracy was passed; no per-system or per-render-pass timings were collected
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 1013 frames, summing to 114.86ms:

```text
   54.92 ms   47.8%  outside
   38.32 ms   33.4%  PreUpdate
    9.67 ms    8.4%  Update
    6.15 ms    5.4%  PostUpdate
    2.43 ms    2.1%  Last
    1.44 ms    1.3%  StateTransition
    0.77 ms    0.7%  RunFixedMainLoop
    0.61 ms    0.5%  SpawnScene
    0.56 ms    0.5%  First
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
   54.92   247.21     4.50   outside
   38.32   152.85     3.99   PreUpdate
    9.67    47.77     4.94   Update
    6.15    31.71     5.16   PostUpdate
    2.43    12.18     5.02   Last
    1.44     8.28     5.76   StateTransition
    0.77     3.62     4.68   RunFixedMainLoop
    0.61     3.16     5.14   SpawnScene
    0.56     1.02     1.83   First
```

⛔⛔ **THIS SPLIT IS NOT CPU WORK.** The game emitted `untrustworthy=render_blocking` for this run: the census attributes wall time between markers, so time spent BLOCKED on the GPU lands in whichever phase brackets it. A phase at 30% here may be waiting, not working, and nothing in the numbers distinguishes the two.

Use it for the frame TOTAL and for comparing one run against another taken the same way. To attribute CPU cost to a phase, take a run with no rendering — or get per-system zones, which need Tracy to actually connect.
```text
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  99.8%  the game itself
   0.2%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.2%   (cargo and shell; NOT a compile)
the game itself           : 99.8%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  84.6%  software rasterizer (CPU emulating a GPU)
  10.2%  game binary + its Rust/C deps
   5.2%  kernel
   0.0%  GPU driver / graphics stack
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     0.43%  llvmpipe-0       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.37%  llvmpipe-2       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.35%  llvmpipe-4       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.33%  llvmpipe-1       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.32%  llvmpipe-3       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.31%  llvmpipe-10      [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.30%  llvmpipe-5       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.30%  llvmpipe-7       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.28%  llvmpipe-8       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.27%  llvmpipe-11      [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.26%  llvmpipe-9       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
     0.26%  llvmpipe-6       [JIT] tid 541291      [.] 0x00007a057aa9107e                                                                                                                               
```

## Assets and render resources

- Decoded images: 105 → 660 (102.7 MP, 411.0 MB of decode work).
- Images resident at end: 499.
- **Resident image BYTES at end: 411.0 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **333 images (56.4 MP)** at 20.8s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 3 GENERATED image(s) were allocated during gameplay (10.7 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

✔ FIRST ROOM: no notable decode landed in the second after the first `room-loaded` (frame 241) with a LATER frame stamp — the first room's art, the player's sheet included, was in before the route activated.

⚠ 6 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **1 of 14 notable decodes landed during SETTLED play** (2.2 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
   2.2MP  at 18.009s  sprites_0_5x/perfect_cellular_automaton_parts.png
```

Textures decoded more than once:

```text
   4x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 124
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 7008372 bytes

## Files in this bundle

| file | contents | present |
| --- | --- | --- |
| `summary.md` | this file | yes |
| `metadata.txt / metadata.json` | build, commit, host, and capture settings | yes |
| `host-environment.txt` | CPU, GPU, DRM nodes, Vulkan ICDs, graphics env overrides | yes |
| `timeline.md` | per-window perf symbols labelled with the game's own log markers | yes |
| `frame_times.csv` | per-census-window frame-time percentiles | yes |
| `frame_spikes.csv` | every frame over 33.4ms, with its wall-clock second | yes |
| `frame_windows.csv` | the always-on 5s frame census | yes |
| `camera_views.csv` | one row per camera per sample: role, target, size, layers | yes |
| `view_totals.csv` | camera/active/world-rendering/offscreen counts per sample | yes |
| `runtime_census.csv` | entity, archetype, component, body, and player counts | yes |
| `draw_census.csv` | sprite/text/projection population and visibility | yes |
| `render_target_census.csv` | offscreen image targets and their bytes | yes |
| `render_diagnostics.csv` | Bevy per-pass CPU/GPU times and pipeline statistics | yes |
| `portal_activity.csv` | portal capture rigs and the budget bounding them | yes |
| `asset_activity.csv` | cumulative decode work and resident images | yes |
| `image_decodes.csv` | every notable texture decode, with its path | yes |
| `image_arrivals.csv` | images reaching Assets<Image> per census window | yes |
| `world_events.csv` | room loads and session starts/ends, with game time | yes |
| `schedule_census.csv` | registered system counts per sample | yes |
| `schedule_phases.csv` | per-frame milliseconds in each main-schedule phase | yes |
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | no |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | no |
| `tracy.trace` | the raw Tracy trace, for the GUI | no |
| `alloc_by_span.csv` | perf_probe: allocations per Bevy system / tracing span | no |
| `churn_by_site.csv` | perf_probe: spawns, despawns, inserts, removes per source line | no |
| `changes_by_site.csv` | perf_probe: component and resource values marked changed, per source line | no |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

