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

Observed span of the game's own log: **22.0s**.

## Frame time

19 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.6      27    24.0     2.5    89.0   428.1    428.1
     12.6      22    45.6    12.9   162.4   252.4    252.4
     14.6      88    11.3     5.5    28.7   109.8    154.7
     13.6      81    12.5     9.1    24.9    95.2     96.9
      9.6     274     3.7     3.0     4.2    22.3     79.3
     11.6     230     4.4     3.9     7.8    19.1     24.8
      3.6     345     2.9     2.6     4.5     9.5     21.9
     19.7     165     6.1     5.3    10.8    14.4     19.5
```

Full series: `frame_times.csv`.

19 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    3.163s     428.1 ms
   13.827s     252.4 ms
   14.256s     162.4 ms
   15.677s     154.7 ms
   13.473s     136.6 ms
   14.030s     113.2 ms
   15.787s     109.9 ms
   15.027s      96.9 ms
   15.522s      96.0 ms
   14.564s      95.1 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.6        4       3      1       0      1
      1.6        4       3      1       0      1
      2.6        4       3      1       0      1
      3.6        4       3      1       0      1
      4.6        4       3      1       0      1
      5.6        4       3      1       0      1
      6.6        4       3      1       0      1
      7.6        4       3      1       0      1
      8.6        4       3      1       0      1
      9.6        4       3      1       0      1
     10.6        4       3      1       0      1
     11.6        4       3      1       0      1
     12.6        4       3      1       0      1
     13.6        4       3      1       0      1
     14.6        4       3      1       0      1
     15.6        4       3      1       0      1
     16.7        4       3      1       0      1
     17.7        4       3      1       0      1
     18.7        4       3      1       0      1
     19.7        4       3      1       0      1
```

Peak world-rendering cameras: **1** at t=0.6s.

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

Peak active portal capture rigs: **0** of 0 at t=0.6s.

```text
        t  rigs  active  budget
      0.6     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      1.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      2.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      3.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      4.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      5.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      6.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      7.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      8.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     11.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     13.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     14.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     15.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     16.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     17.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     19.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.6s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1915      130        1
      peak       8192        1915      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **280** (68 visible), text2d 35, per-view projections 11 at t=11.6s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3235** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.032          0.065       19  render/ui/elapsed_gpu
         0.025          0.031       19  render/msaa_writeback/elapsed_gpu
         0.015          0.024       19  render/ui/elapsed_cpu
         0.012          0.015       19  render/upscaling/elapsed_gpu
         0.003          0.004       19  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.007       19  render/msaa_writeback/elapsed_cpu
         0.002          0.005       19  render/upscaling/elapsed_cpu
         0.001          0.001       19  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1367328.316    4746529.000       19  render/ui/fragment_shader_invocations
       584.947        698.000       19  render/ui/vertex_shader_invocations
       291.474        348.000       19  render/ui/clipper_primitives_out
       291.474        348.000       19  render/ui/clipper_invocations
         0.000          0.000       19  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       19  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       19  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       19  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (4 spans).
- GPU pass timings: **measured** (4 spans).
- Pipeline statistics: **measured** (8 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
--no-tracy was passed; no per-system or per-render-pass timings were collected
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 4184 frames, summing to 4.56ms:

```text
    1.08 ms   23.8%  Update
    0.99 ms   21.8%  outside
    0.95 ms   20.8%  PreUpdate
    0.82 ms   18.1%  PostUpdate
    0.37 ms    8.2%  RunFixedMainLoop
    0.10 ms    2.3%  StateTransition
    0.09 ms    2.0%  Last
    0.08 ms    1.7%  SpawnScene
    0.06 ms    1.4%  First
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    1.08     2.39    -1.31   Update
    0.99     2.51    -1.52   outside
    0.95     2.74    -1.79   PreUpdate
    0.82     1.43    -0.60   PostUpdate
    0.37     1.11    -0.74   RunFixedMainLoop
    0.10     0.34    -0.23   StateTransition
    0.09     0.12    -0.03   Last
    0.08     0.13    -0.06   SpawnScene
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
  99.0%  the game itself
   0.8%  build launcher (cargo, shell)
   0.2%  audio
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.8%   (cargo and shell; NOT a compile)
the game itself           : 99.0%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  65.0%  game binary + its Rust/C deps
  30.1%  kernel
   4.8%  GPU driver / graphics stack
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
     1.48%  Compute Task Po  libc.so.6                    [.] __memmove_evex_unaligned_erms                                                                                                             
     1.27%  ambition_game_b  ambition_game_bin            [.] _mi_page_malloc_zero                                                                                                                      
     1.05%  ambition_game_b  ambition_game_bin            [.] _RNvXNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run               
     1.04%  ambition_game_b  ambition_game_bin            [.] _RNvMs2_NtCs2DYmA60ysKy_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCsfCZ9IGOsnvc_14ambition_input7actions31Platformer2dInputActi
     0.91%  Compute Task Po  ambition_game_bin            [.] _RNvMs1_NtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                       
     0.90%  IO Task Pool (2  [kernel.kallsyms]            [k] __reset_isolation_pfn                                                                                                                     
     0.87%  ambition_game_b  libc.so.6                    [.] __memmove_evex_unaligned_erms                                                                                                             
     0.76%  Compute Task Po  ambition_game_bin            [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCshswzoL9RzC8_10bevy_tasks9task_poolNtB19_8TaskPool12new_inter
     0.71%  IO Task Pool (1  [kernel.kallsyms]            [k] __reset_isolation_pfn                                                                                                                     
     0.69%  Compute Task Po  ambition_game_bin            [.] _mi_page_malloc_zero                                                                                                                      
     0.68%  IO Task Pool (0  [kernel.kallsyms]            [k] isolate_freepages_block                                                                                                                   
     0.67%  ambition_game_b  ambition_game_bin            [.] mi_free                                                                                                                                   
     0.65%  IO Task Pool (1  ambition_game_bin            [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                      
     0.59%  IO Task Pool (3  ambition_game_bin            [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                      
     0.58%  Compute Task Po  [kernel.kallsyms]            [k] __reset_isolation_pfn                                                                                                                     
     0.57%  IO Task Pool (3  [kernel.kallsyms]            [k] __reset_isolation_pfn                                                                                                                     
     0.57%  Compute Task Po  ambition_game_bin            [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC8_10bevy_t
     0.56%  IO Task Pool (0  [kernel.kallsyms]            [k] __reset_isolation_pfn                                                                                                                     
     0.55%  IO Task Pool (2  ambition_game_bin            [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                      
     0.47%  ambition_game_b  [nvidia]                     [k] _nv046188rm                                                                                                                               
     0.47%  ambition_game_b  ambition_game_bin            [.] _RNvMs_NtCs2DYmA60ysKy_22leafwing_input_manager15clashing_inputsNtB4_11BasicInputs12clashes_with                                          
     0.43%  ambition_game_b  ambition_game_bin            [.] mi_theap_malloc_aligned                                                                                                                   
     0.43%  Compute Task Po  ambition_game_bin            [.] _mi_page_free_collect                                                                                                                     
     0.39%  Compute Task Po  ambition_game_bin            [.] _RNvNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                            
     0.37%  ambition_game_b  libc.so.6                    [.] __memcmp_evex_movbe                                                                                                                       
     0.36%  IO Task Pool (2  ambition_game_bin            [.] _RNvNtNtCsiOYQA2dFZzS_3png6filter5paeth8unfilter                                                                                          
     0.36%  Compute Task Po  [kernel.kallsyms]            [k] isolate_freepages_block                                                                                                                   
     0.36%  IO Task Pool (3  ambition_game_bin            [.] _RNvNtNtCsiOYQA2dFZzS_3png6filter5paeth8unfilter                                                                                          
     0.35%  Compute Task Po  [kernel.kallsyms]            [k] psi_group_change                                                                                                                          
     0.34%  Compute Task Po  [kernel.kallsyms]            [k] native_queued_spin_lock_slowpath                                                                                                          
     0.33%  IO Task Pool (1  ambition_game_bin            [.] _RNvNtNtCsiOYQA2dFZzS_3png6filter5paeth8unfilter                                                                                          
     0.33%  IO Task Pool (1  [kernel.kallsyms]            [k] isolate_freepages_block                                                                                                                   
     0.33%  IO Task Pool (3  [kernel.kallsyms]            [k] isolate_freepages_block                                                                                                                   
     0.32%  Compute Task Po  ambition_game_bin            [.] _RNvMNtNtNtNtCs6ZjlLoI6YmX_3std3sys4sync5mutex5futexNtB2_5Mutex14lock_contended                                                           
     0.32%  Compute Task Po  ambition_game_bin            [.] _RNvMs0_NtCsjIIPkFW7fiI_16concurrent_queue9unboundedINtB5_9UnboundedNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC8_10b
```

## Assets and render resources

- Decoded images: 0 → 271 (534.9 MP, 2139.5 MB of decode work).
- Images resident at end: 269.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **127 images (88.8 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 72 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

93 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 339244 bytes

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

