# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `1341e8cb7154` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling ` |
| host | `toothbrush` |
| kernel | `Linux toothbrush 6.8.0-138-generic #138-Ubuntu SMP PREEMPT_DYNAMIC Fri Jul 31 22:41:49 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `n/a` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=16
- MemTotal:       131727480 kB

## Renderer

```text
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.84", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **15.4s**.

## Frame time

12 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.7       8    82.4    11.8   441.4   441.4    441.4
      7.8      69    14.3     6.4    49.7   122.4    251.0
      8.8     110     9.1     3.8    10.0   161.1    204.5
      6.8     111     9.5     4.8    37.9    99.7    114.5
      4.7     267     3.8     3.1     4.9    19.9     84.3
      9.8     241     4.2     3.8     5.6     6.7     15.9
      2.7     318     3.1     3.0     4.3     8.0     13.4
     11.8     238     4.2     3.8     5.8     9.2     10.7
```

Full series: `frame_times.csv`.

17 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    3.308s     441.4 ms
    8.847s     251.0 ms
    9.759s     204.4 ms
    9.921s     161.2 ms
   10.047s     126.2 ms
    8.596s     122.4 ms
    2.867s     115.2 ms
    8.410s     114.5 ms
    8.296s      99.7 ms
    5.801s      84.3 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.7        4       3      1       0      1
      1.7        4       3      1       0      1
      2.7        4       3      1       0      1
      3.7        4       3      1       0      1
      4.7        4       3      1       0      1
      5.7        4       3      1       0      1
      6.8        4       3      1       0      1
      7.8        4       3      1       0      1
      8.8        4       3      1       0      1
      9.8        4       3      1       0      1
     10.8        4       3      1       0      1
     11.8        4       4      1       0      1
     12.8        4       4      1       0      1
```

Peak world-rendering cameras: **1** at t=0.7s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
           other  Cube pause camera, Cube scrim display camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=0.7s.

```text
        t  rigs  active  budget
      0.7     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      1.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      2.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      3.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      4.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      5.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      6.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      7.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      8.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     11.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.7s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1924      130        1
      peak       8192        1924      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **261** (67 visible), text2d 50, per-view projections 14 at t=7.8s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3235** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.079          0.080        2  render/clustering/elapsed_gpu
         0.031          0.039        2  render/main_opaque_pass_3d/elapsed_gpu
         0.026          0.065       12  render/ui/elapsed_gpu
         0.024          0.026       11  render/msaa_writeback/elapsed_gpu
         0.016          0.016        2  render/early_mesh_preprocessing/elapsed_gpu
         0.015          0.036       12  render/ui/elapsed_cpu
         0.014          0.014        2  render/bin_unpacking/elapsed_gpu
         0.014          0.017        2  render/clustering/elapsed_cpu
         0.012          0.012       11  render/upscaling/elapsed_gpu
         0.011          0.013        2  render/main_opaque_pass_3d/elapsed_cpu
         0.006          0.007        2  render/main_transparent_pass_3d/elapsed_gpu
         0.005          0.005        2  render/main_transparent_pass_3d/elapsed_cpu
         0.003          0.004        2  render/bin_unpacking/elapsed_cpu
         0.003          0.004       12  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.003        2  render/early_mesh_preprocessing/elapsed_cpu
         0.002          0.006       11  render/msaa_writeback/elapsed_cpu
         0.002          0.003       11  render/upscaling/elapsed_cpu
         0.001          0.001       12  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1182680.000    1339208.000        2  render/main_opaque_pass_3d/fragment_shader_invocations
    860123.750    4653384.000       12  render/ui/fragment_shader_invocations
     18774.000      26927.000        2  render/main_transparent_pass_3d/fragment_shader_invocations
       926.500       1177.000        2  render/main_transparent_pass_3d/vertex_shader_invocations
       579.167        698.000       12  render/ui/vertex_shader_invocations
       459.500        581.000        2  render/main_transparent_pass_3d/clipper_invocations
       376.500        477.000        2  render/main_transparent_pass_3d/clipper_primitives_out
       288.667        348.000       12  render/ui/clipper_primitives_out
       288.667        348.000       12  render/ui/clipper_invocations
       128.000        128.000        2  render/early_mesh_preprocessing/compute_shader_invocations
       108.000        164.000        2  render/main_opaque_pass_3d/vertex_shader_invocations
        64.000         64.000        2  render/bin_unpacking/compute_shader_invocations
        55.500         86.000        2  render/main_opaque_pass_3d/clipper_primitives_out
        54.000         82.000        2  render/main_opaque_pass_3d/clipper_invocations
         0.000          0.000       12  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       12  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       12  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       12  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (9 spans).
- GPU pass timings: **measured** (9 spans).
- Pipeline statistics: **measured** (18 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
--no-tracy was passed; no per-system or per-render-pass timings were collected
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 2430 frames, summing to 4.96ms:

```text
    1.30 ms   26.2%  Update
    1.27 ms   25.5%  outside
    0.93 ms   18.8%  PreUpdate
    0.87 ms   17.5%  PostUpdate
    0.31 ms    6.3%  RunFixedMainLoop
    0.08 ms    1.6%  Last
    0.08 ms    1.5%  StateTransition
    0.07 ms    1.4%  SpawnScene
    0.06 ms    1.2%  First
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    1.30     3.31    -2.01   Update
    1.27     3.25    -1.98   outside
    0.93     2.86    -1.92   PreUpdate
    0.87     1.61    -0.74   PostUpdate
    0.31     1.09    -0.78   RunFixedMainLoop
    0.08     0.12    -0.04   Last
    0.08     0.31    -0.23   StateTransition
    0.07     0.12    -0.06   SpawnScene
    0.06     0.15    -0.09   First
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
  98.5%  the game itself
   1.1%  build launcher (cargo, shell)
   0.3%  audio
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  1.1%   (cargo and shell; NOT a compile)
the game itself           : 98.5%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  63.0%  game binary + its Rust/C deps
  31.6%  kernel
   5.3%  GPU driver / graphics stack
   0.1%  audio
   0.0%  software rasterizer (CPU emulating a GPU)
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     2.17%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     1.16%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.95%  ambition_game_b  ambition_game_bin                 [.] mi_free                                                                                                                              
     0.83%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     0.78%  IO Task Pool (3  ambition_game_bin                 [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                 
     0.74%  Compute Task Po  [kernel.kallsyms]                 [k] __reset_isolation_pfn                                                                                                                
     0.74%  IO Task Pool (1  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.72%  IO Task Pool (1  [kernel.kallsyms]                 [k] isolate_migratepages_block                                                                                                           
     0.68%  Compute Task Po  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.67%  IO Task Pool (2  ambition_game_bin                 [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                 
     0.65%  Compute Task Po  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.60%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.60%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.59%  ambition_game_b  ambition_game_bin                 [.] _RNvMs2_NtCs2DYmA60ysKy_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCsfCZ9IGOsnvc_14ambition_input7actions31Platformer2dInpu
     0.59%  IO Task Pool (1  ambition_game_bin                 [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                 
     0.57%  IO Task Pool (0  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.57%  IO Task Pool (0  ambition_game_bin                 [.] _RNvNtNtCsiOYQA2dFZzS_3png6filter5paeth8unfilter                                                                                     
     0.56%  IO Task Pool (2  ambition_game_bin                 [.] _RNvNtNtCsiOYQA2dFZzS_3png6filter5paeth8unfilter                                                                                     
     0.56%  IO Task Pool (3  ambition_game_bin                 [.] _RNvNtNtCsiOYQA2dFZzS_3png6filter5paeth8unfilter                                                                                     
     0.53%  IO Task Pool (0  ambition_game_bin                 [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                 
     0.49%  IO Task Pool (3  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.47%  IO Task Pool (2  [kernel.kallsyms]                 [k] isolate_migratepages_block                                                                                                           
     0.44%  IO Task Pool (1  [kernel.kallsyms]                 [k] __reset_isolation_pfn                                                                                                                
     0.43%  Compute Task Po  ambition_game_bin                 [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCshswzoL9RzC8_10bevy_tasks9task_poolNtB19_8TaskPool12new_
     0.41%  IO Task Pool (0  [kernel.kallsyms]                 [k] __reset_isolation_pfn                                                                                                                
     0.41%  ambition_game_b  [nvidia]                          [k] _nv046188rm                                                                                                                          
     0.39%  Compute Task Po  ambition_game_bin                 [.] _mi_page_free_collect                                                                                                                
     0.38%  IO Task Pool (1  [kernel.kallsyms]                 [k] copy_page                                                                                                                            
     0.38%  Compute Task Po  ambition_game_bin                 [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC8_10b
     0.38%  IO Task Pool (2  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.37%  ambition_game_b  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     0.37%  IO Task Pool (1  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.35%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCs4WaVACXR4pa_16bevy_ya
     0.35%  Compute Task Po  [kernel.kallsyms]                 [k] isolate_migratepages_block                                                                                                           
     0.34%  Compute Task Po  ambition_game_bin                 [.] _RNvNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                       
```

## Assets and render resources

- Decoded images: 0 → 267 (533.5 MP, 2134.0 MB of decode work).
- Images resident at end: 266.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **149 images (104.4 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 72 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

93 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 260076 bytes

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
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

