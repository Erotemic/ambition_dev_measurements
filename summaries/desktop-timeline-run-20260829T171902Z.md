# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `8552328d3a32` on `main` |
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

Observed span of the game's own log: **113.0s**.

## Frame time

107 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.9       2   334.7   365.3   365.3   365.3    365.3
      3.9      44    22.6    16.6    18.4   266.8    266.8
     72.3      99    10.0     8.5    13.5    57.1     78.4
     59.3     126     8.0     7.0    10.2    27.8     74.2
     47.2     113     8.9     7.4    13.2    41.4     64.9
     82.4     124     8.1     7.5     9.5    13.8     46.5
     15.0      61    16.6    16.6    18.3    20.9     23.5
     71.3     144     7.1     7.0     8.0     8.6     22.5
```

Full series: `frame_times.csv`.

10 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    5.188s     365.2 ms
    4.823s     304.1 ms
    5.455s     266.9 ms
   73.867s      78.4 ms
   61.046s      74.2 ms
   48.981s      64.9 ms
   73.748s      57.0 ms
    5.518s      55.9 ms
   83.787s      46.5 ms
   49.030s      41.3 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.5        4       3      1       0      1
      6.9        4       3      1       0      1
     11.9        4       3      1       0      1
     17.0        4       3      1       0      1
     22.0        4       3      1       0      1
     27.1        4       3      1       0      1
     32.1        4       3      1       0      1
     37.2        4       3      1       0      1
     42.2        4       3      1       0      1
     47.2        4       3      1       0      1
     52.3        4       3      1       0      1
     57.3        4       3      1       0      1
     62.3        4       3      1       0      1
     67.3        4       3      1       0      1
     72.3        4       4      1       0      1
     77.3        4       3      1       0      1
     82.4        4       3      1       0      1
     87.4        4       4      1       0      1
     92.4        4       3      1       0      1
     97.4        4       3      1       0      1
    102.4        4       3      1       0      1
    107.4        4       3      1       0      1
```

Peak world-rendering cameras: **1** at t=1.5s.

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

Peak active portal capture rigs: **0** of 0 at t=1.5s.

```text
        t  rigs  active  budget
      1.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     10.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     20.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     29.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     38.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     47.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     56.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     65.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     74.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     83.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     92.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    101.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=1.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         110        0        0
       end       8192         855        2        1
      peak       8192         855       18        1
```

⚠ Entity count rose by 6144 over the session and was **still
climbing in the second half** (+6144 after t=56.3s). Growth that never falls across room
transitions is the shape of a lifecycle leak; check `runtime_census.csv`
against the room markers in `timeline.md`.

Peak sprites: **192** (37 visible), text2d 35, per-view projections 11 at t=95.4s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3301** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.041          0.116      106  render/ui/elapsed_gpu
         0.041          0.159      106  render/main_transparent_pass_2d/elapsed_gpu
         0.023          0.038       38  render/main_transparent_pass_3d/elapsed_cpu
         0.022          0.070      106  render/main_transparent_pass_2d/elapsed_cpu
         0.021          0.022       38  render/early_mesh_preprocessing/elapsed_gpu
         0.020          0.022       38  render/main_indirect_parameters_building/elapsed_gpu
         0.018          0.054      106  render/upscaling/elapsed_gpu
         0.015          0.026      106  render/msaa_writeback/elapsed_gpu
         0.015          0.055       38  render/main_opaque_pass_3d/elapsed_gpu
         0.014          0.017       38  render/main_opaque_pass_3d/elapsed_cpu
         0.010          0.011       38  render/late_mesh_preprocessing/elapsed_gpu
         0.010          0.042      106  render/ui/elapsed_cpu
         0.008          0.010       38  render/main_transparent_pass_3d/elapsed_gpu
         0.003          0.004      106  render/main_opaque_pass_2d/elapsed_gpu
         0.003          0.003       38  render/early_mesh_preprocessing/elapsed_cpu
         0.002          0.003       38  render/main_indirect_parameters_building/elapsed_cpu
         0.002          0.029      106  render/main_opaque_pass_2d/elapsed_cpu
         0.001          0.002      106  render/upscaling/elapsed_cpu
         0.000          0.002      106  render/msaa_writeback/elapsed_cpu
         0.000          0.000       38  render/late_mesh_preprocessing/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   3534069.972    7853951.000      106  render/main_transparent_pass_2d/fragment_shader_invocations
   1753548.226    5569413.000      106  render/ui/fragment_shader_invocations
   1440000.000    1440000.000      106  render/msaa_writeback/fragment_shader_invocations
   1440000.000    1440000.000      106  render/upscaling/fragment_shader_invocations
    312592.763    1606192.000       38  render/main_opaque_pass_3d/fragment_shader_invocations
      6434.500      31884.000       38  render/main_transparent_pass_3d/fragment_shader_invocations
       848.491       2920.000      106  render/ui/vertex_shader_invocations
       715.842       1377.000       38  render/main_transparent_pass_3d/vertex_shader_invocations
       577.009       3040.000      106  render/main_transparent_pass_2d/vertex_shader_invocations
       424.245       1460.000      106  render/ui/clipper_invocations
       396.396       1430.000      106  render/ui/clipper_primitives_out
       356.105        681.000       38  render/main_transparent_pass_3d/clipper_invocations
       348.158        485.000       38  render/main_transparent_pass_3d/clipper_primitives_out
       288.623       1520.000      106  render/main_transparent_pass_2d/clipper_invocations
       256.000        256.000       38  render/main_indirect_parameters_building/compute_shader_invocations
       245.236        987.000      106  render/main_transparent_pass_2d/clipper_primitives_out
       128.000        128.000       38  render/early_mesh_preprocessing/compute_shader_invocations
       121.263        164.000       38  render/main_opaque_pass_3d/vertex_shader_invocations
        63.605         86.000       38  render/main_opaque_pass_3d/clipper_primitives_out
        60.632         82.000       38  render/main_opaque_pass_3d/clipper_invocations
         3.000          3.000      106  render/msaa_writeback/vertex_shader_invocations
         3.000          3.000      106  render/upscaling/vertex_shader_invocations
         1.000          1.000      106  render/msaa_writeback/clipper_primitives_out
         1.000          1.000      106  render/upscaling/clipper_primitives_out
         1.000          1.000      106  render/msaa_writeback/clipper_invocations
         1.000          1.000      106  render/upscaling/clipper_invocations
         0.000          0.000      106  render/main_opaque_pass_2d/clipper_invocations
         0.000          0.000      106  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      106  render/main_opaque_pass_2d/vertex_shader_invocations
         0.000          0.000      106  render/main_opaque_pass_2d/fragment_shader_invocations
```

- CPU pass timings: **measured** (10 spans).
- GPU pass timings: **measured** (10 spans).
- Pipeline statistics: **measured** (31 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)


`perf` cannot produce this: a Bevy system is not a native symbol, and a
render pass is a graph node rather than a function. Counts matter as much
as totals -- a cheap zone entered ten thousand times is a scheduling
problem, not a slow function.

## Whole session, ranked by total time

```text
  total_ms     count   mean_us    max_us  worst  zone
  109913.9         1 109913877.2 109913877.2   100%  bevy_app
  109853.1         1 109853138.1 109853138.1   100%  render thread
   85422.1     11761    7263.2 1279770.5     1%  update
   75687.7     11761    6435.5 1061702.9     1%  main app
   75637.5     11761    6431.2 1061698.8     1%  schedule{name=Main}
   43809.3     11761    3725.0  294814.9     1%  sub app{name=RenderApp}
   43749.0     11761    3719.8  294806.6     1%  schedule{name=Render}
   24308.4     11761    2066.9  380130.8     2%  schedule{name=Update}
   22128.0     11761    1881.5   59704.9     0%  schedule{name=PreUpdate}
   15478.5     11761    1316.1  259554.7     2%  schedule{name=PostUpdate}
   14979.8     11761    1273.7    5334.1     0%  system{name="bevy_framepace::framerate_limiter"}
   11919.2     11761    1013.5   42291.9     0%  system{name="bevy_render::renderer::render_system"}
    9677.0     11761     822.8  352678.1     4%  sub app{name=RenderExtractApp}
    7993.5     11761     679.7   58284.6     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
    7812.0      3219    2426.8   58194.1     1%  ggrs{name="HandleRequests"}
    7790.1      3219    2420.0   58187.0     1%  schedule{name="AdvanceWorld"}
    7778.7      3219    2416.5   58182.5     1%  schedule{name=AdvanceWorld}
    7701.6      3219    2392.6   58086.4     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
    7688.2      3219    2388.4   58080.3     1%  schedule{name=GgrsSchedule}
    6337.2   3541945       1.8    1825.4     0%  multithreaded executor
    5362.8     11761     456.0   13456.0     0%  run_graph{name="main_graph"}
    5147.6     11761     437.7    5442.4     0%  schedule{name=RunFixedMainLoop}
    4443.6     11761     377.8  216792.7     5%  schedule{name=ExtractSchedule}
    4271.6     23522     181.6    1779.2     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
    2669.0     11761     226.9    5281.0     0%  system{name="ambition_platformer2d_rollback_ggrs::session::enforce_session_contract"}
    2550.0     11761     216.8   38472.8     2%  submit_graph_commands
    2389.2     11761     203.1    5105.5     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    2331.8      6885     338.7    3622.0     0%  schedule{name=FixedMain}
    2044.4     35280      57.9    1021.1     0%  node{name="bevy_render::render_graph::node::ViewNodeRunner<bevy_core_pipeline::core_2d::ma
    1834.6     11760     156.0     573.5     0%  run_graph{name="Core2d" debug_group="Camera 7 (3v0)"}
    1797.6     11761     152.8    1254.3     0%  system{name="bevy_render::apply_extract_commands"}
    1700.9     11760     144.6     633.6     0%  run_graph{name="Core2d" debug_group="Camera 0 (5v0)"}
    1663.3      6885     241.6    3389.1     0%  schedule{name=FixedPostUpdate}
    1632.6     11761     138.8    2839.1     0%  command_buffer_generation_tasks
    1437.1     11761     122.2    1365.3     0%  schedule{name=Last}
    1409.8     35277      40.0    1012.2     0%  main_opaque_pass_2d
    1406.8     11762     119.6    3368.5     0%  schedule{name=StateTransition}
    1372.1     11760     116.7    1184.1     0%  run_graph{name="Core2d" debug_group="Camera 9 (6v0)"}
    1194.8     11761     101.6    1097.7     0%  system{name="bevy_ui::layout::ui_layout_system"}
    1094.9     11761      93.1    2439.6     0%  schedule{name=ProcessLdtkApi}
```

`worst` is the share of the zone's whole-session total spent in its single
slowest call. A zone at 90% is not a per-frame cost at all -- it is one
hitch that a total cannot distinguish from steady work. Rank those with the
steady-state table below, and find WHEN they hit in `frame_spikes.csv`.

## Steady state, ranked by recurring cost

The same zones with each one's SLOWEST call removed, so a one-time
compile/load/build cannot outrank work that recurs. `per_call_us` is what
the zone costs on an ordinary frame; multiply it by the frame count to see
what removing it would actually buy.

```text
 steady_ms  per_call_us     count    min_us  zone
   84142.4       7155.0     11761    4505.5  update
   74626.0       6345.8     11761    4076.8  main app
   74575.8       6341.5     11761    4073.7  schedule{name=Main}
   43514.5       3700.2     11761    1692.4  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 11692 frames, summing to 9.23ms:

```text
    2.87 ms   31.1%  outside
    2.19 ms   23.7%  Update
    1.91 ms   20.7%  PreUpdate
    1.34 ms   14.5%  PostUpdate
    0.46 ms    5.0%  RunFixedMainLoop
    0.15 ms    1.6%  Last
    0.15 ms    1.6%  StateTransition
    0.10 ms    1.1%  First
    0.05 ms    0.6%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  81.0%  the game itself
  18.7%  profiler (Tracy)
   0.2%  build tooling
   0.1%  audio
```

The profiler cost 19% of sampled cycles. Low enough that the
measurements below stand on their own.

## Where the native time went

```text
  80.5%  game binary + its Rust/C deps
  15.4%  kernel
   4.0%  GPU driver / graphics stack
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
     6.00%  Tracy Profiler   ambition_game_bin                    [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                        
     2.73%  ambition_game_b  ambition_game_bin                    [.] _RNvXs1_Cs55zrdcupEh6_13tracing_tracyNtB5_10TracyLayerINtNtCs6uRcRnqR4J_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredIN
     2.48%  ambition_game_b  ambition_game_bin                    [.] _RINvNtNtNtNtCs4NRVxsYgnAr_4core5slice4sort8unstable9quicksort9quicksortNtNtCscdodAO9FK5_5alloc6string6StringNCINvMB8_SB17_20sort_
     2.19%  ambition_game_b  libc.so.6                            [.] __memmove_evex_unaligned_erms                                                                                                     
     1.72%  ambition_game_b  ambition_game_bin                    [.] _RNvXNtNtNtCs77wS6V9rGzO_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run       
     1.54%  ambition_game_b  ambition_game_bin                    [.] _mi_page_malloc_zero                                                                                                              
     1.52%  ambition_game_b  ambition_game_bin                    [.] _RNvMs_NtCsfWB9K5UC3R_12sharded_slab4poolINtB4_4PoolNtNtNtCs6uRcRnqR4J_18tracing_subscriber8registry7sharded9DataInnerE3getBT_    
     1.44%  ambition_game_b  ambition_game_bin                    [.] _RINvNtNtNtCs4NRVxsYgnAr_4core5slice4sort8unstable7ipnsortNtNtCscdodAO9FK5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_key
     1.07%  Tracy Symbol Wo  ambition_game_bin                    [.] tracy::NormalizePath(char const*)                                                                                                 
     0.93%  Compute Task Po  libc.so.6                            [.] __memmove_evex_unaligned_erms                                                                                                     
     0.81%  Compute Task Po  ambition_game_bin                    [.] _RNvXs1_Cs55zrdcupEh6_13tracing_tracyNtB5_10TracyLayerINtNtCs6uRcRnqR4J_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredIN
     0.69%  ambition_game_b  ambition_game_bin                    [.] _RNvMs2_NtCskOO3K8vaNOI_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCs5U6yvC0EKwv_14ambition_input7actions31Platformer2dI
     0.65%  ambition_game_b  ambition_game_bin                    [.] mi_free                                                                                                                           
     0.64%  ambition_game_b  ambition_game_bin                    [.] mi_theap_umalloc                                                                                                                  
     0.62%  Tracy Profiler   ambition_game_bin                    [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                         
     0.59%  Tracy Profiler   libc.so.6                            [.] __memmove_evex_unaligned_erms                                                                                                     
     0.57%  ambition_game_b  ambition_game_bin                    [.] ___tracy_emit_zone_begin_alloc                                                                                                    
     0.57%  ambition_game_b  ambition_game_bin                    [.] _RNvXs4_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscdodAO9FK5_5alloc5boxed3BoxDINtNtB9_5laye
     0.54%  Compute Task Po  ambition_game_bin                    [.] _RNvMs1_NtNtNtCs77wS6V9rGzO_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                               
     0.50%  ambition_game_b  ambition_game_bin                    [.] tracy::rpmalloc(unsigned long)                                                                                                    
     0.49%  Tracy Symbol Wo  ambition_game_bin                    [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsigned long, ch
     0.49%  Tracy Profiler   ambition_game_bin                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy::Queue
     0.45%  Tracy Symbol Wo  ambition_game_bin                    [.] tracy::report_inlined_functions(unsigned long, tracy::function*, char const*, int (*)(void*, unsigned long, unsigned long, char co
     0.45%  Tracy Symbol Wo  libc.so.6                            [.] __memmove_evex_unaligned_erms                                                                                                     
     0.45%  ambition_game_b  ambition_game_bin                    [.] _RNvXsh_NtCs4NRVxsYgnAr_4core3fmteNtB5_5Debug3fmt                                                                                 
     0.44%  Tracy Profiler   ambition_game_bin                    [.] tracy::rpfree(void*)                                                                                                              
     0.43%  Compute Task Po  ambition_game_bin                    [.] tracy::rpmalloc(unsigned long)                                                                                                    
     0.41%  Compute Task Po  ambition_game_bin                    [.] _mi_page_malloc_zero                                                                                                              
     0.40%  Tracy Profiler   libc.so.6                            [.] __strlen_evex                                                                                                                     
     0.40%  ambition_game_b  ambition_game_bin                    [.] _mi_theap_realloc_zero                                                                                                            
     0.37%  ambition_game_b  [kernel.kallsyms]                    [k] perf_adjust_freq_unthr_context                                                                                                    
     0.36%  Compute Task Po  ambition_game_bin                    [.] _RNvXs5_NtCs2DDHhGaqmAk_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCslpdWifUVxKZ_10bevy_tasks9task_poolNtB19_8TaskPool12n
     0.36%  Tracy Symbol Wo  ambition_game_bin                    [.] tracy::backtrace_qsort(void*, unsigned long, unsigned long, int (*)(void const*, void const*)) [clone .localalias]                
     0.36%  Compute Task Po  ambition_game_bin                    [.] _RNvMs_NtCsfWB9K5UC3R_12sharded_slab4poolINtB4_4PoolNtNtNtCs6uRcRnqR4J_18tracing_subscriber8registry7sharded9DataInnerE3getBT_    
     0.35%  ambition_game_b  ambition_game_bin                    [.] _RNvMs_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                          
```

## Assets and render resources

- Decoded images: 79 → 277 (197.0 MP, 788.2 MB of decode work).
- Images resident at end: 214.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **126 images (93.5 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 31 decode(s) landed before the first `room-loaded` (93.8 MP) — boot. Not a gameplay hitch.

⚠ 7 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **15 of 53 notable decodes landed during SETTLED play** (20.9 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
   2.0MP  at 53.891s  sprites/noether_portraits.png
   2.0MP  at 69.641s  sprites/noether_portraits.png
   1.3MP  at 53.875s  sprites/player_robot_v3_portraits.png
   1.3MP  at 53.875s  sprites/oiler_portraits.png
   1.3MP  at 53.875s  sprites/perfect_cellular_automaton_portraits.png
   1.3MP  at 53.875s  sprites/officer_portraits.png
   1.3MP  at 53.891s  sprites/medic_portraits.png
   1.3MP  at 53.891s  sprites/carl_stargan_portraits.png
   1.3MP  at 53.891s  sprites/patent_clerk_portraits.png
   1.3MP  at 69.616s  sprites/player_robot_v3_portraits.png
```

Textures decoded more than once:

```text
   3x  sprites/player_robot_v3_portraits.png
   3x  sprites/author_portraits.png
   3x  sprites/carl_stargan_portraits.png
   3x  sprites/patent_clerk_portraits.png
   3x  sprites/perfect_cellular_automaton_portraits.png
   3x  sprites/officer_portraits.png
   3x  sprites/oiler_portraits.png
   3x  sprites/medic_portraits.png
   3x  sprites/noether_portraits.png
   2x  <runtime-generated> — allocated during gameplay. No asset path, so this is generated (an atlas or a render target), not content that could have been demanded earlier.
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 964300 bytes

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
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | yes |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | yes |
| `tracy.trace` | the raw Tracy trace, for the GUI | yes |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

