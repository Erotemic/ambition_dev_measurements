# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `7cfee82449d1` on `main` |
| working tree | clean |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.1 (48a229cea 2026-09-01)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --jobs 6 -- --start-room hall_of_characters ` |
| host | `aivm-2404` |
| kernel | `Linux aivm-2404 6.8.0-147-generic #147-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep 24 11:33:16 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `n/a` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: Intel(R) Xeon(R) CPU E5-2620 v4 @ 2.10GHz
- logical_cpus=30
- MemTotal:       156604168 kB

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

Observed span of the game's own log: **123.7s**.

## Frame time

109 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.2       2   359.2   479.0   479.0   479.0    479.0
      3.3       4   219.4   194.8   456.5   456.5    456.5
      4.3       6   166.0   222.7   341.9   341.9    341.9
      5.3      12    83.7    82.6   131.0   149.8    149.8
     97.1      15    66.5    50.6   109.3   144.7    144.7
     21.9      14    74.2    77.8   132.9   142.2    142.2
     32.2      15    73.8    57.2   127.6   140.6    140.6
     62.1      14    73.6    89.9   119.5   139.7    139.7
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    7.929s     477.1 ms
    8.710s     456.5 ms
    9.151s     349.0 ms
    7.452s     241.8 ms
    9.471s     228.8 ms
    9.749s     222.7 ms
    8.254s     194.7 ms
    9.954s     152.4 ms
   10.449s     131.2 ms
   10.125s     131.0 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 417 images / 28.6 MP in total; 2 were notable enough to print.

```text
ordered by: frame
before hall_of_characters: 2 image(s), 17.2 MP
  unknown                  2    17.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.2        5       3      1       0      1
      6.3       11       7      1       4      1
     11.5       11       3      1       0      1
     16.7       11       7      1       4      1
     21.9       11       5      1       2      1
     27.0       11       3      1       0      1
     32.2       11       9      1       6      1
     37.3       11       5      1       2      1
     42.5       11       7      1       4      1
     47.7       11       3      1       0      1
     52.8       11       5      1       2      1
     57.9       11       7      1       4      1
     63.1       11       3      1       0      1
     68.4       11       7      1       4      1
     73.5       11       3      1       0      1
     78.7       11       7      1       4      1
     83.8       11       9      1       6      1
     88.9       11       5      1       2      1
     94.0       11       7      1       4      1
     99.2       11       7      1       4      1
    104.3       11       3      1       0      1
    109.4       11       9      1       6      1
```

Peak world-rendering cameras: **1** at t=1.2s.

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

Peak active portal capture rigs: **0** of 0 at t=1.2s.

```text
        t  rigs  active  budget
      1.2     0       0  captures<=1
     10.4     0       0  captures<=1
     19.8     0       0  captures<=1
     29.1     0       0  captures<=1
     38.4     0       0  captures<=1
     47.7     0       0  captures<=1
     56.9     0       0  captures<=1
     66.3     0       0  captures<=1
     75.5     0       0  captures<=1
     84.8     0       0  captures<=1
     94.0     0       0  captures<=1
    103.2     0       0  captures<=1
    112.5     0       0  captures<=1
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 576px) at t=4.3s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1672        0        0
       end      16384        2117      262        1
      peak      16384        2117      262        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **6535** (238 visible), text2d 35, per-view projections 8 at t=4.3s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3789** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         2.916          9.289      107  render/upscaling/elapsed_gpu
         2.126         17.969      108  render/ui/elapsed_gpu
         1.512          2.148      107  render/main_opaque_pass_2d/elapsed_gpu
         0.446          1.113      108  render/main_transparent_pass_2d/elapsed_gpu
         0.030          0.089      108  render/ui/elapsed_cpu
         0.023          0.090      107  render/main_opaque_pass_2d/elapsed_cpu
         0.006          0.041      107  render/upscaling/elapsed_cpu
         0.002          0.019      108  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    305419.963     331776.000      107  render/main_opaque_pass_2d/fragment_shader_invocations
    231495.935    4418737.000      108  render/ui/fragment_shader_invocations
       699.463        998.000      108  render/ui/vertex_shader_invocations
       348.741        498.000      108  render/ui/clipper_primitives_out
       348.741        498.000      108  render/ui/clipper_invocations
         3.963          4.000      107  render/main_opaque_pass_2d/vertex_shader_invocations
         1.981          2.000      107  render/main_opaque_pass_2d/clipper_invocations
         1.981          2.000      107  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      108  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      108  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      108  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      108  render/main_transparent_pass_2d/clipper_primitives_out
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

Mean milliseconds per frame over 1745 frames, summing to 64.44ms:

```text
   35.02 ms   54.4%  PreUpdate
   17.49 ms   27.1%  outside
    6.82 ms   10.6%  Update
    3.38 ms    5.2%  PostUpdate
    1.20 ms    1.9%  Last
    0.20 ms    0.3%  RunFixedMainLoop
    0.19 ms    0.3%  First
    0.08 ms    0.1%  SpawnScene
    0.06 ms    0.1%  StateTransition
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
   35.02   320.31     9.15   PreUpdate
   17.49   196.85    11.26   outside
    6.82    60.02     8.81   Update
    3.38    32.51     9.63   PostUpdate
    1.20    10.41     8.68   Last
    0.20     1.98     9.89   RunFixedMainLoop
    0.19     0.56     2.91   First
    0.08     0.65     8.32   SpawnScene
    0.06     0.44     7.37   StateTransition
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
   0.1%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           : 99.8%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  65.5%  software rasterizer (CPU emulating a GPU)
  18.1%  game binary + its Rust/C deps
  16.3%  kernel
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     0.42%  ambition_game_b  ambition_game_bin     [.] _RNvMsv_Cs3vm97JqiQgY_23ambition_entity_catalogNtB5_8MoveSpec10frame_data
     0.32%  sprite-sheet-wa  ambition_game_bin     [.] _RNvMs3_NtCsly2ShwrCe8a_10serde_json4readNtB5_9SliceRead14skip_to_escape
     0.29%  ambition_game_b  ambition_game_bin     [.] _mi_page_malloc_zero
     0.26%  sprite-sheet-wa  ambition_game_bin     [.] _RNvNtNtCsgxBkk5gSRhY_4core3str8converts9from_utf8
     0.25%  sprite-sheet-wa  ambition_game_bin     [.] _RNvXsi_NtCs9k3SxhrAWiO_3std4pathNtB5_10ComponentsNtNtNtNtCsgxBkk5gSRhY_4core4iter6traits8iterator8Iterator4next
```

## Assets and render resources

- Decoded images: 83 → 417 (28.6 MP, 114.3 MB of decode work).
- Images resident at end: 417.
- **Resident image BYTES at end: 114.3 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **416 images (28.3 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ No notable texture decoded while gameplay was live.

2 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 124
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 4416220 bytes

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
