# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `029dbebfd53c` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile ` |
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

Observed span of the game's own log: **63.6s**.

## Frame time

58 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     19.7      15    77.9    21.7   367.1   468.8    468.8
      2.5       3   218.9   103.8   466.1   466.1    466.1
     17.5      24    42.1    20.3   134.8   373.5    373.5
     10.5     104     9.6     7.2    17.8    37.0    124.0
     16.5      68    14.8     9.8    43.3    82.7     98.6
     18.6      65    16.1    12.7    31.3    60.5     82.4
     20.7      91    10.2    10.3    13.3    19.2     73.5
     49.8      96    10.5    10.8    13.3    21.3     27.1
```

Full series: `frame_times.csv`.

24 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   21.789s     468.7 ms
    4.415s     466.2 ms
   19.101s     373.5 ms
   21.320s     367.0 ms
   19.236s     134.8 ms
   12.128s     124.0 ms
    3.949s     104.3 ms
   18.493s      98.6 ms
    4.502s      86.6 ms
   18.014s      82.7 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.4        4       3      1       0      1
      3.5        4       3      1       0      1
      5.5        4       3      1       0      1
      7.5        4       3      1       0      1
      9.5        4       3      1       0      1
     11.5        4       3      1       0      1
     13.5        4       3      1       0      1
     15.5        4       3      1       0      1
     17.5        4       3      1       0      1
     19.7        4       3      1       0      1
     21.7        4       3      1       0      1
     23.7        4       3      1       0      1
     25.7        4       3      1       0      1
     27.7        4       3      1       0      1
     29.7        4       3      1       0      1
     31.7        4       3      1       0      1
     33.7        4       3      1       0      1
     35.7        4       3      1       0      1
     37.8        4       3      1       0      1
     39.8        4       3      1       0      1
     41.8        4       3      1       0      1
     43.8        4       3      1       0      1
     45.8        4       3      1       0      1
     47.8        4       3      1       0      1
     49.8        4       4      1       0      1
     51.8        4       3      1       0      1
     53.8        4       3      1       0      1
     55.8        4       3      1       0      1
     57.9        4       4      1       0      1
     59.9        4       4      1       0      1
```

Peak world-rendering cameras: **1** at t=1.4s.

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

Peak active portal capture rigs: **0** of 0 at t=1.4s.

```text
        t  rigs  active  budget
      1.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      5.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     13.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     17.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     21.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     25.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     29.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     33.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     37.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     41.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     45.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     49.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     53.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     57.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=1.4s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1984      130        1
      peak       8192        1984      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **287** (86 visible), text2d 330, per-view projections 70 at t=28.7s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3235** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.129          0.134       11  render/clustering/elapsed_gpu
         0.053          0.062       57  render/msaa_writeback/elapsed_gpu
         0.051          0.127       57  render/ui/elapsed_gpu
         0.038          0.076       11  render/main_opaque_pass_3d/elapsed_gpu
         0.026          0.054       57  render/upscaling/elapsed_gpu
         0.023          0.024       11  render/early_mesh_preprocessing/elapsed_gpu
         0.018          0.020       11  render/clustering/elapsed_cpu
         0.018          0.018       11  render/bin_unpacking/elapsed_gpu
         0.016          0.018       11  render/main_opaque_pass_3d/elapsed_cpu
         0.015          0.036       57  render/ui/elapsed_cpu
         0.010          0.012       11  render/main_transparent_pass_3d/elapsed_gpu
         0.009          0.014       11  render/main_transparent_pass_3d/elapsed_cpu
         0.004          0.021       11  render/early_mesh_preprocessing/elapsed_cpu
         0.004          0.004       11  render/bin_unpacking/elapsed_cpu
         0.004          0.004       57  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.008       57  render/msaa_writeback/elapsed_cpu
         0.002          0.009       57  render/upscaling/elapsed_cpu
         0.001          0.002       57  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    770114.545    1601061.000       11  render/main_opaque_pass_3d/fragment_shader_invocations
    602583.018    4756380.000       57  render/ui/fragment_shader_invocations
     14788.727      27681.000       11  render/main_transparent_pass_3d/fragment_shader_invocations
       916.273       1381.000       11  render/main_transparent_pass_3d/vertex_shader_invocations
       645.930        986.000       57  render/ui/vertex_shader_invocations
       454.455        683.000       11  render/main_transparent_pass_3d/clipper_invocations
       391.182        495.000       11  render/main_transparent_pass_3d/clipper_primitives_out
       321.965        492.000       57  render/ui/clipper_primitives_out
       321.965        492.000       57  render/ui/clipper_invocations
       135.273        164.000       11  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000       11  render/early_mesh_preprocessing/compute_shader_invocations
        70.818         86.000       11  render/main_opaque_pass_3d/clipper_primitives_out
        67.636         82.000       11  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000       11  render/bin_unpacking/compute_shader_invocations
         0.000          0.000       57  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       57  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       57  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       57  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (9 spans).
- GPU pass timings: **measured** (9 spans).
- Pipeline statistics: **measured** (18 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
tracy-capture produced no trace (the game never connected)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 6031 frames, summing to 9.70ms:

```text
    2.85 ms   29.4%  PreUpdate
    2.82 ms   29.1%  Update
    1.74 ms   17.9%  PostUpdate
    1.17 ms   12.0%  outside
    0.55 ms    5.6%  RunFixedMainLoop
    0.21 ms    2.2%  Last
    0.14 ms    1.4%  StateTransition
    0.14 ms    1.4%  First
    0.09 ms    0.9%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  98.3%  the game itself
   1.1%  profiler (Tracy)
   0.3%  build launcher (cargo, shell)
   0.3%  audio
```

```text
profiler (Tracy) overhead :  1.1%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.3%   (cargo and shell; NOT a compile)
the game itself           : 98.3%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  72.3%  game binary + its Rust/C deps
  24.1%  kernel
   3.6%  GPU driver / graphics stack
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
     2.52%  ambition_game_b  ambition_game_bin                 [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     2.13%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20sort_un
     2.10%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     1.53%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     1.40%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     1.25%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     1.19%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_keyjN
     1.16%  ambition_game_b  ambition_game_bin                 [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_     
     0.85%  Compute Task Po  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.70%  ambition_game_b  ambition_game_bin                 [.] mi_free                                                                                                                              
     0.70%  Compute Task Po  ambition_game_bin                 [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     0.63%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.62%  ambition_game_b  ambition_game_bin                 [.] _RNvXs4_NtNtCshkcqoohQOEC_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5layer
     0.62%  ambition_game_b  ambition_game_bin                 [.] mi_theap_umalloc                                                                                                                     
     0.60%  Compute Task Po  ambition_game_bin                 [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_     
     0.60%  ambition_game_b  ambition_game_bin                 [.] _RNvXs1_NtNtCshkcqoohQOEC_18tracing_subscriber8registry7shardedNtB5_8RegistryNtB7_10LookupSpan9span_data                             
     0.51%  Compute Task Po  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.48%  ambition_game_b  ambition_game_bin                 [.] _RNvMs2_NtCsbH7joPaZV8S_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCs43kaUkgDg0C_14ambition_input7actions31Platformer2dInpu
     0.44%  ambition_game_b  ambition_game_bin                 [.] _RNvMs_NtNtCshkcqoohQOEC_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                            
     0.42%  ambition_game_b  ambition_game_bin                 [.] ___tracy_emit_zone_begin_alloc                                                                                                       
     0.39%  Compute Task Po  [kernel.kallsyms]                 [k] perf_adjust_freq_unthr_context                                                                                                       
     0.39%  ambition_game_b  ambition_game_bin                 [.] _RNvXsh_NtCsc36rpYXAlPq_4core3fmteNtB5_5Debug3fmt                                                                                    
     0.38%  Compute Task Po  ambition_game_bin                 [.] _mi_page_free_collect                                                                                                                
     0.37%  ambition_game_b  ambition_game_bin                 [.] _mi_theap_realloc_zero                                                                                                               
     0.37%  Compute Task Po  ambition_game_bin                 [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCshswzoL9RzC8_10bevy_tasks9task_poolNtB19_8TaskPool12new_
     0.36%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCshA6g2k3PUQh_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithINtNtCsiZqKh2jk5BB_16virtua
     0.36%  Compute Task Po  ambition_game_bin                 [.] tracy::rpmalloc(unsigned long)                                                                                                       
     0.35%  Compute Task Po  ambition_game_bin                 [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC8_10b
     0.34%  Compute Task Po  ambition_game_bin                 [.] _RNvXs4_NtNtCshkcqoohQOEC_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5layer
     0.34%  ambition_game_b  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     0.32%  ambition_game_b  ambition_game_bin                 [.] _RNvNtCsc36rpYXAlPq_4core3fmt5write                                                                                                  
     0.32%  IO Task Pool (2  [kernel.kallsyms]                 [k] __reset_isolation_pfn                                                                                                                
     0.31%  Compute Task Po  ambition_game_bin                 [.] _RNvMs_NtNtCshkcqoohQOEC_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                            
     0.30%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort6shared9smallsort18small_sort_generalNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB1f_2
     0.29%  ambition_game_b  ambition_game_bin                 [.] _RINvMs_NtNtCshkcqoohQOEC_18tracing_subscriber6filter3envNtB5_9EnvFilter8on_enterINtNtNtB9_5layer7layered7LayeredINtNtCsc36rpYXAlPq_4
```

## Assets and render resources

- Decoded images: 0 → 272 (534.6 MP, 2138.6 MB of decode work).
- Images resident at end: 270.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **126 images (85.9 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 59 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **13 of 93 notable decodes landed during SETTLED play** (133.0 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  16.8MP  at 19.291s  sprites/perfect_cellular_automaton_spritesheet.1.png
  16.7MP  at 18.924s  sprites/perfect_cellular_automaton_spritesheet.2.png
  16.7MP  at 19.291s  sprites/perfect_cellular_automaton_spritesheet.5.png
  16.5MP  at 19.291s  sprites/perfect_cellular_automaton_spritesheet.3.png
  15.9MP  at 18.924s  sprites/perfect_cellular_automaton_spritesheet.4.png
  15.7MP  at 19.291s  sprites/perfect_cellular_automaton_spritesheet.png
   9.4MP  at 19.291s  sprites/perfect_cellular_automaton_spritesheet.6.png
   7.2MP  at 19.761s  sprites/patent_clerk_spritesheet.png
   6.5MP  at 19.761s  sprites/player_robot_v2_spritesheet.png
   5.7MP  at 19.291s  sprites/robot_spritesheet.png
```

93 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 658580 bytes

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

