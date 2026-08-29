# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `4bd4a329e7b0` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.97.1 (8bab26f4f 2026-07-14)` |
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
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, driver: "NVIDIA", driver_info: "595.84", backend: Vulkan }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **224.9s**.

## Frame time

219 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
    114.4      70    20.5     8.1    63.3   197.8    516.4
    115.5       4   253.1   393.2   467.0   467.0    467.0
      2.6       2   225.9   295.9   295.9   295.9    295.9
      3.6     104     9.6     6.9     9.6    51.4    230.4
     22.6      85    11.8     9.5    13.3    34.3    198.3
     50.8     116     8.6     6.5     9.4    27.1    162.3
     49.8      98    11.0     7.1    15.5   130.5    131.6
     21.6     109     9.3     7.6    16.6    25.9     69.8
```

Full series: `frame_times.csv`.

24 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
  116.271s     516.3 ms
  117.131s     467.0 ms
  116.664s     393.2 ms
    4.355s     295.9 ms
    4.586s     230.4 ms
   23.933s     198.3 ms
  115.754s     197.8 ms
   51.775s     162.4 ms
    4.059s     155.8 ms
  117.267s     135.9 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.6        4       3      1       0      1
     12.6        4       3      1       0      1
     23.7        4       3      1       0      1
     34.7        4       3      1       0      1
     45.7        4       3      1       0      1
     56.8        4       3      1       0      1
     67.9        4       3      1       0      1
     78.9        4       3      1       0      1
     90.0        4       3      1       0      1
    101.0        4       3      1       0      1
    112.0        4       3      1       0      1
    123.5        4       3      1       0      1
    134.6        4       3      1       0      1
    145.6        4       3      1       0      1
    156.7        4       3      1       0      1
    167.7        4       3      1       0      1
    178.7       18       4      2       1      1
    189.8        4       3      1       0      1
    200.8        4       3      1       0      1
    211.9        4       3      1       0      1
```

Peak world-rendering cameras: **3** at t=169.7s.

⭐ **The world was drawn 3 times in one frame at peak.** Only
cameras that draw the SIMULATED WORLD are counted — main gameplay, a
split-screen local view, a portal capture rig — so the HUD is not one of
these. Each is a full pass over the visible population. Check
`camera_views.csv` for which roles were live together and at what
resolution each drew.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
           other  Cube pause camera, Cube scrim display camera
  portal_capture  Portal view capture (c128), Portal view capture (c129), Portal view capture (c130), Portal view capt
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **2** of 14 at t=169.7s.

```text
        t  rigs  active  budget
      1.6     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     19.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     37.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     55.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     73.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     92.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    110.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    128.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    146.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    164.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    182.8    14       1  res<=2048 depth=1 captures<=4 updates/frame<=4
    200.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    218.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **14** (largest dimension 2048px) at t=168.7s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         110        0        0
       end       8192        1110        7        1
      peak       8192        1110      130        1
```

⚠ Entity count rose by 6144 over the session and was **still
climbing in the second half** (+4096 after t=112.0s). Growth that never falls across room
transitions is the shape of a lifecycle leak; check `runtime_census.csv`
against the room markers in `timeline.md`.

Peak sprites: **320** (178 visible), text2d 30, per-view projections 10 at t=202.8s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3299** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.047          0.104      218  render/main_transparent_pass_2d/elapsed_gpu
         0.030          0.079      218  render/main_transparent_pass_2d/elapsed_cpu
         0.022          0.054       43  render/main_transparent_pass_3d/elapsed_cpu
         0.017          0.017       43  render/early_mesh_preprocessing/elapsed_gpu
         0.017          0.087      218  render/ui/elapsed_gpu
         0.016          0.017       43  render/main_indirect_parameters_building/elapsed_gpu
         0.013          0.031      218  render/upscaling/elapsed_gpu
         0.012          0.061      218  render/msaa_writeback/elapsed_gpu
         0.010          0.046      218  render/ui/elapsed_cpu
         0.010          0.013       43  render/main_opaque_pass_3d/elapsed_cpu
         0.010          0.036       43  render/main_opaque_pass_3d/elapsed_gpu
         0.009          0.010       43  render/late_mesh_preprocessing/elapsed_gpu
         0.006          0.009       43  render/main_transparent_pass_3d/elapsed_gpu
         0.004          0.007      218  render/main_opaque_pass_2d/elapsed_gpu
         0.002          0.003       43  render/early_mesh_preprocessing/elapsed_cpu
         0.002          0.019      218  render/main_opaque_pass_2d/elapsed_cpu
         0.001          0.002       43  render/main_indirect_parameters_building/elapsed_cpu
         0.001          0.008      218  render/upscaling/elapsed_cpu
         0.000          0.004      218  render/msaa_writeback/elapsed_cpu
         0.000          0.001       43  render/late_mesh_preprocessing/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   5120643.138    8927007.000      218  render/main_transparent_pass_2d/fragment_shader_invocations
   1440000.000    1440000.000      218  render/msaa_writeback/fragment_shader_invocations
   1440000.000    1440000.000      218  render/upscaling/fragment_shader_invocations
    812545.385    5574328.000      218  render/ui/fragment_shader_invocations
    307953.395    1605996.000       43  render/main_opaque_pass_3d/fragment_shader_invocations
      8927.757     387676.000      218  render/main_opaque_pass_2d/fragment_shader_invocations
      6618.023      27979.000       43  render/main_transparent_pass_3d/fragment_shader_invocations
       894.092       3976.000      218  render/main_transparent_pass_2d/vertex_shader_invocations
       785.872       2900.000      218  render/ui/vertex_shader_invocations
       738.209       1341.000       43  render/main_transparent_pass_3d/vertex_shader_invocations
       447.087       1988.000      218  render/main_transparent_pass_2d/clipper_invocations
       392.936       1450.000      218  render/ui/clipper_invocations
       367.047        663.000       43  render/main_transparent_pass_3d/clipper_invocations
       365.385       1422.000      218  render/ui/clipper_primitives_out
       354.872       1884.000      218  render/main_transparent_pass_2d/clipper_primitives_out
       351.558        479.000       43  render/main_transparent_pass_3d/clipper_primitives_out
       256.000        256.000       43  render/main_indirect_parameters_building/compute_shader_invocations
       128.000        128.000       43  render/early_mesh_preprocessing/compute_shader_invocations
       123.628        164.000       43  render/main_opaque_pass_3d/vertex_shader_invocations
        64.907         86.000       43  render/main_opaque_pass_3d/clipper_primitives_out
        61.814         82.000       43  render/main_opaque_pass_3d/clipper_invocations
         3.000          3.000      218  render/msaa_writeback/vertex_shader_invocations
         3.000          3.000      218  render/upscaling/vertex_shader_invocations
         1.000          1.000      218  render/msaa_writeback/clipper_primitives_out
         1.000          1.000      218  render/upscaling/clipper_primitives_out
         1.000          1.000      218  render/msaa_writeback/clipper_invocations
         1.000          1.000      218  render/upscaling/clipper_invocations
         0.243         11.000      218  render/main_opaque_pass_2d/vertex_shader_invocations
         0.151          7.000      218  render/main_opaque_pass_2d/clipper_invocations
         0.142          7.000      218  render/main_opaque_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (10 spans).
- GPU pass timings: **measured** (10 spans).
- Pipeline statistics: **measured** (31 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy artifacts in this bundle.

## Which phase of the frame owned the time

Mean milliseconds per frame over 28292 frames, summing to 7.79ms:

```text
    2.40 ms   30.8%  PreUpdate
    2.33 ms   29.9%  Update
    1.41 ms   18.2%  PostUpdate
    0.76 ms    9.8%  outside
    0.45 ms    5.8%  RunFixedMainLoop
    0.15 ms    1.9%  Last
    0.14 ms    1.8%  StateTransition
    0.10 ms    1.3%  First
    0.05 ms    0.7%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  86.3%  the game itself
  13.5%  profiler (Tracy)
   0.1%  audio
   0.1%  build tooling
```

The profiler cost 13% of sampled cycles. Low enough that the
measurements below stand on their own.

## Where the native time went

```text
  82.0%  game binary + its Rust/C deps
  13.8%  kernel
   4.1%  GPU driver / graphics stack
   0.1%  audio
   0.0%  software rasterizer (CPU emulating a GPU)
```

From `perf-report-by-dso.txt`. If the top bucket is not the game binary,
ranking game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     5.52%  Tracy Profiler   ambition_game_bin                      [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                      
     2.71%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_Cs55zrdcupEh6_13tracing_tracyNtB5_10TracyLayerINtNtCs6uRcRnqR4J_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layered
     2.35%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtNtCs4NRVxsYgnAr_4core5slice4sort8unstable9quicksort9quicksortNtNtCscdodAO9FK5_5alloc6string6StringNCINvMB8_SB17_20sor
     2.20%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     1.70%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtCsfWB9K5UC3R_12sharded_slab4poolINtB4_4PoolNtNtNtCs6uRcRnqR4J_18tracing_subscriber8registry7sharded9DataInnerE3getBT_  
     1.56%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCs77wS6V9rGzO_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     1.55%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtCs4NRVxsYgnAr_4core5slice4sort8unstable7ipnsortNtNtCscdodAO9FK5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_k
     1.51%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.88%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.78%  Compute Task Po  ambition_game_bin                      [.] _RNvXs1_Cs55zrdcupEh6_13tracing_tracyNtB5_10TracyLayerINtNtCs6uRcRnqR4J_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layered
     0.76%  ambition_game_b  ambition_game_bin                      [.] mi_free                                                                                                                         
     0.76%  ambition_game_b  ambition_game_bin                      [.] _RNvXs4_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscdodAO9FK5_5alloc5boxed3BoxDINtNtB9_5la
     0.74%  ambition_game_b  ambition_game_bin                      [.] mi_theap_umalloc                                                                                                                
     0.67%  ambition_game_b  ambition_game_bin                      [.] _RNvMs2_NtCskOO3K8vaNOI_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCs5U6yvC0EKwv_14ambition_input7actions31Platformer2
     0.66%  ambition_game_b  ambition_game_bin                      [.] ___tracy_emit_zone_begin_alloc                                                                                                  
     0.64%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                       
     0.63%  Compute Task Po  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.60%  Compute Task Po  ambition_game_bin                      [.] _RNvMs1_NtNtNtCs77wS6V9rGzO_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.56%  Tracy Profiler   libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.52%  ambition_game_b  ambition_game_bin                      [.] _RNvXsh_NtCs4NRVxsYgnAr_4core3fmteNtB5_5Debug3fmt                                                                               
     0.51%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::NormalizePath(char const*)                                                                                               
     0.51%  ambition_game_b  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.48%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_NtNtCs6uRcRnqR4J_18tracing_subscriber8registry7shardedNtB5_8RegistryNtB7_10LookupSpan9span_data                         
     0.47%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                        
     0.46%  ambition_game_b  ambition_game_bin                      [.] _RNvNtCs4NRVxsYgnAr_4core3fmt5write                                                                                             
     0.46%  Compute Task Po  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.43%  ambition_game_b  ambition_game_bin                      [.] _RINvMs_NtCsfWB9K5UC3R_12sharded_slab4poolINtB5_4PoolNtNtNtCs6uRcRnqR4J_18tracing_subscriber8registry7sharded9DataInnerE11create
     0.41%  Tracy Profiler   ambition_game_bin                      [.] tracy::rpfree(void*)                                                                                                            
     0.41%  Compute Task Po  ambition_game_bin                      [.] _RNvMs_NtCsfWB9K5UC3R_12sharded_slab4poolINtB4_4PoolNtNtNtCs6uRcRnqR4J_18tracing_subscriber8registry7sharded9DataInnerE3getBT_  
     0.39%  ambition_game_b  ambition_game_bin                      [.] _mi_theap_realloc_zero                                                                                                          
     0.35%  Compute Task Po  ambition_game_bin                      [.] _RNvXs5_NtCs2DDHhGaqmAk_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCslpdWifUVxKZ_10bevy_tasks9task_poolNtB19_8TaskPool1
     0.35%  Compute Task Po  [kernel.kallsyms]                      [k] perf_adjust_freq_unthr_context                                                                                                  
     0.34%  ambition_game_b  ambition_game_bin                      [.] mi_theap_malloc_aligned                                                                                                         
     0.34%  ambition_game_b  ambition_game_bin                      [.] _RNvMs1_NtNtNtCs77wS6V9rGzO_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.33%  ambition_game_b  ambition_game_bin                      [.] _RINvMs_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter3envNtB5_9EnvFilter8on_enterINtNtNtB9_5layer7layered7LayeredINtNtCs4NRVxsYgn
```

## Assets and render resources

- Decoded images: 77 → 424 (655.9 MP, 2623.7 MB of decode work).
- Images resident at end: 326.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

Textures decoded more than once:

```text
  30x  <runtime-generated>
   4x  sprites/perfect_cellular_automaton_portraits.png
   3x  sprites/officer_portraits.png
   3x  sprites/player_robot_v3_portraits.png
   3x  sprites/oiler_portraits.png
   3x  sprites/patent_clerk_portraits.png
   3x  sprites/carl_stargan_portraits.png
   3x  sprites/author_portraits.png
   3x  sprites/noether_portraits.png
   3x  sprites/actor_portraits.png
   3x  sprites/medic_portraits.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 2167108 bytes

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
| `schedule_census.csv` | registered system counts per sample | yes |
| `schedule_phases.csv` | per-frame milliseconds in each main-schedule phase | yes |
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | no |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | no |
| `tracy.trace` | the raw Tracy trace, for the GUI | yes |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

