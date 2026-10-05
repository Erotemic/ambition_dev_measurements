# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `43ae232a7445` on `main` |
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
| kernel | `Linux toothbrush 6.8.0-142-generic #142-Ubuntu SMP PREEMPT_DYNAMIC Wed Sep  2 14:24:27 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
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

Observed span of the game's own log: **123.2s**.

## Frame time

118 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.4      76    10.9     7.1    10.5    54.8    234.4
     13.5      33    34.4    23.1   125.1   150.6    150.6
      6.4      92    11.0     7.7    18.2    40.2    116.3
     20.6      35    28.8    26.8    39.8    78.6     78.6
     48.0      28    36.1    34.0    51.8    64.5     64.5
     17.5      26    38.9    37.4    55.8    60.5     60.5
     14.5      35    26.1    24.9    30.8    60.2     60.2
     88.6      28    36.5    35.2    52.5    55.2     55.2
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    2.514s     234.5 ms
   15.274s     147.3 ms
   15.126s     129.1 ms
   14.997s     125.1 ms
    7.809s     116.3 ms
   21.822s      78.6 ms
   14.872s      63.7 ms
   18.698s      60.6 ms
   15.362s      60.3 ms
   18.807s      55.8 ms
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
    1     2841     True  central_hub_complex -> hall_of_characters
```

Frames over 33.4 ms AFTER the last transition was logged (t=14.980s): **52**, worst 147.3 ms.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

⛔ **THE SPIKE LOG HIT ITS 60-LINE CAP, so this count is a FLOOR, not a total.** Percentile summaries continued; the per-frame lines did not.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 306 images / 179.0 MP in total; 34 were notable enough to print.

```text
ordered by: frame
before central_hub_complex: 6 image(s), 24.1 MP
  unknown                  2    16.5 MP
  vanity-card              1     3.0 MP
  boss-sheet               1     2.0 MP
  character-sheet          1     1.4 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.4        4       3      1       0      1
      5.4        4       3      1       0      1
     10.5        6       5      1       2      1
     15.5       18      11      1       8      1
     20.6       18      15      1      12      1
     25.7       18      17      1      14      1
     30.8       18      13      1      10      1
     35.8       18      13      1      10      1
     40.9       18      15      1      12      1
     46.0       18      17      1      14      1
     51.0       18      15      1      12      1
     56.1       18       7      1       4      1
     61.2       18      15      1      12      1
     66.3       18      17      1      14      1
     71.3       18      11      1       8      1
     76.4       18       7      1       4      1
     81.5       18      15      1      12      1
     86.6       18      17      1      14      1
     91.6       18       5      1       2      1
     96.7       18      13      1      10      1
    101.7       18      15      1      12      1
    106.8       18      15      1      12      1
    111.8       18       5      1       2      1
    116.9       18       9      1       6      1
```

Peak world-rendering cameras: **1** at t=0.4s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
       offscreen  rigged impostor camera, rigged impostor unpremultiply camera
           other  Cube pause camera, Cube scrim display camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=0.4s.

```text
        t  rigs  active  budget
      0.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      9.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     27.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     36.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     46.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     55.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     64.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     73.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     82.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     91.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    100.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    109.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    118.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **14** (largest dimension 2304px) at t=13.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1492        0        0
       end      16384        2035      138        1
      peak      16384        2035      138        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **3771** (2970 visible), text2d 35, per-view projections 11 at t=13.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3445** in 34 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.048          0.534      118  render/msaa_writeback/elapsed_gpu
         0.043          0.081      118  render/ui/elapsed_gpu
         0.037          0.067      113  render/main_opaque_pass_2d/elapsed_gpu
         0.030          0.093      118  render/ui/elapsed_cpu
         0.021          0.035      118  render/upscaling/elapsed_gpu
         0.015          0.063      113  render/main_opaque_pass_2d/elapsed_cpu
         0.004          0.030      118  render/main_transparent_pass_2d/elapsed_gpu
         0.004          0.016      118  render/msaa_writeback/elapsed_cpu
         0.002          0.019      118  render/upscaling/elapsed_cpu
         0.001          0.015      118  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   2751098.336    2985984.000      113  render/main_opaque_pass_2d/fragment_shader_invocations
    325770.508    3921099.000      118  render/ui/fragment_shader_invocations
       687.085       1450.000      118  render/ui/vertex_shader_invocations
       342.542        724.000      118  render/ui/clipper_primitives_out
       342.542        724.000      118  render/ui/clipper_invocations
         4.000          4.000      113  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000      113  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000      113  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      118  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      118  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      118  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      118  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (5 spans).
- GPU pass timings: **measured** (5 spans).
- Pipeline statistics: **measured** (12 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)


`perf` cannot produce this: a Bevy system is not a native symbol, and a
render pass is a graph node rather than a function. Counts matter as much
as totals -- a cheap zone entered ten thousand times is a scheduling
problem, not a slow function.

## Whole session, ranked by total time

```text
  total_ms     count   mean_us    max_us  worst  zone
  120799.7         1 120799739.5 120799739.5   100%  bevy_app
  120774.3         1 120774268.4 120774268.4   100%  render thread
  117699.1      5837   20164.3  493524.5     0%  update
  112361.9      5837   19249.9  456266.2     0%  main app
  112331.8      5837   19244.8  456261.2     0%  schedule{name=Main}
   54937.0      5837    9411.9  192274.1     0%  sub app{name=RenderApp}
   54878.1      5837    9401.8  192266.4     0%  schedule{name=RenderRecovery}
   54636.1      5837    9360.3  192160.8     0%  system{name="bevy_render::run_render_schedule"}
   54473.4      5837    9332.4  192110.8     0%  schedule{name=Render}
   52950.3      5837    9071.5   92687.1     0%  schedule{name=PreUpdate}
   43909.3      5837    7522.6   90859.2     0%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   43282.6      6882    6289.2   79716.5     0%  ggrs{name="HandleRequests"}
   43169.7      6882    6272.8   79707.8     0%  schedule{name="AdvanceWorld"}
   43087.9      6882    6261.0   79685.3     0%  schedule{name=AdvanceWorld}
   42744.7      6882    6211.1   79591.9     0%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
   42686.7      6882    6202.7   79586.9     0%  schedule{name=GgrsSchedule}
   30810.7      5837    5278.5   17978.7     0%  system{name="bevy_render::renderer::render_system"}
   30791.5      5837    5275.2   17975.8     0%  main_render_schedule
   29718.0      5837    5091.3   17649.3     0%  schedule{name=RenderGraph}
   27925.1      5837    4784.2  143977.1     1%  schedule{name=Update}
   23430.7      5837    4014.2   15697.7     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
   22565.3     57114     395.1    4894.8     0%  schedule{name=Core2d}
   17311.8      5837    2965.9   20160.4     0%  schedule{name=PostUpdate}
    6477.5     57114     113.4    1711.3     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    5796.5      5837     993.1    6058.4     0%  schedule{name=RunFixedMainLoop}
    5448.0   3486493       1.6    4048.5     0%  multithreaded executor
    5285.2      5837     905.5  225547.3     4%  sub app{name=RenderExtractApp}
    5080.2      5837     870.3    3184.4     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    4334.1     57114      75.9    2694.2     0%  system{name="bevy_core_pipeline::core_2d::main_transparent_pass_2d_node::main_transparent_
    4282.9      5837     733.7   68132.6     2%  schedule{name=ExtractSchedule}
    4155.1     57112      72.8    2688.7     0%  main_transparent_pass_2d
    3889.4      5837     666.3    5732.9     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    3877.9    693287       5.6     194.1     0%  par_for_each{query="(bevy_ecs::entity::Entity, &bevy_camera::visibility::InheritedVisibili
    3809.5      7698     494.9    5113.6     0%  schedule{name=FixedMain}
    3613.5     24924     145.0     861.7     0%  transparent_main_pass_2d
    3377.8      4482     753.6    2192.4     0%  camera_schedule{camera="Camera -100000 (3402v0)"}
    3148.7      5837     539.4    3082.9     0%  system{name="bevy_sprite_render::render::prepare_sprite_image_bind_groups"}
    2765.6      3715     744.5    3424.6     0%  camera_schedule{camera="Camera -100000 (5726v0)"}
    2690.9      5837     461.0    2532.0     0%  system{name="bevy_camera::visibility::check_visibility_cpu_culling"}
    2685.3      7698     348.8    4856.1     0%  schedule{name=FixedPostUpdate}
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
  117205.6      20083.2      5837    5258.8  update
  111905.7      19175.1      5837    4585.9  main app
  111875.5      19169.9      5837    4582.4  schedule{name=Main}
   54744.8       9380.5      5837    2183.0  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 5770 frames, summing to 20.71ms:

```text
    9.19 ms   44.4%  PreUpdate
    5.04 ms   24.3%  Update
    3.02 ms   14.6%  PostUpdate
    1.49 ms    7.2%  outside
    1.04 ms    5.0%  RunFixedMainLoop
    0.37 ms    1.8%  Last
    0.23 ms    1.1%  First
    0.20 ms    1.0%  StateTransition
    0.14 ms    0.7%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    9.19    24.18     2.63   PreUpdate
    5.04     8.45     1.68   Update
    3.02     5.98     1.98   PostUpdate
    1.49     4.30     2.89   outside
    1.04     2.76     2.66   RunFixedMainLoop
    0.37     0.56     1.52   Last
    0.23     0.60     2.61   First
    0.20     0.47     2.36   StateTransition
    0.14     0.23     1.69   SpawnScene
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
  80.4%  the game itself
  18.9%  profiler (Tracy)
   0.5%  audio
   0.2%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead : 18.9%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.2%   (cargo and shell; NOT a compile)
the game itself           : 80.4%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  82.6%  game binary + its Rust/C deps
  13.0%  kernel
   4.2%  GPU driver / graphics stack
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
     3.85%  Tracy Profiler   ambition_game_bin                      [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                      
     2.06%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     1.98%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     1.92%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layere
     1.55%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20so
     1.24%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCsejQFhAU0ddf_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     1.06%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::NormalizePath(char const*)                                                                                               
     1.00%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     1.00%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCsi7WNBsjk6LE_18tracing_subscriber8registry7sharded9DataInnerE3getBU_
     0.96%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_
     0.92%  ambition_game_b  ambition_game_bin                      [.] mi_theap_umalloc                                                                                                                
     0.85%  Tracy Profiler   libc.so.6                              [.] __strlen_evex                                                                                                                   
     0.79%  ambition_game_b  libc.so.6                              [.] __memcmp_evex_movbe                                                                                                             
     0.74%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsigned long, 
     0.70%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                       
     0.63%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.63%  Tracy Profiler   ambition_game_bin                      [.] tracy::rpfree(void*)                                                                                                            
     0.63%  Tracy Profiler   libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.61%  ambition_game_b  [kernel.kallsyms]                      [k] perf_adjust_freq_unthr_context                                                                                                  
     0.58%  Compute Task Po  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.56%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::report_inlined_functions(unsigned long, tracy::function*, char const*, int (*)(void*, unsigned long, unsigned long, char 
     0.55%  Compute Task Po  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.54%  ambition_game_b  ambition_game_bin                      [.] ___tracy_emit_zone_begin_alloc                                                                                                  
     0.52%  Compute Task Po  ambition_game_bin                      [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layere
     0.52%  ambition_game_b  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.48%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy::Que
     0.47%  ambition_game_b  ambition_game_bin                      [.] _RNvXs4_NtNtCsi7WNBsjk6LE_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5
     0.46%  Tracy Symbol Wo  libc.so.6                              [.] _dl_addr                                                                                                                        
     0.45%  ambition_game_b  ambition_game_bin                      [.] mi_free                                                                                                                         
     0.45%  Compute Task Po  ambition_game_bin                      [.] _RNvNtCs1i8LOh7Gf22_18bevy_sprite_render6render32prepare_sprite_image_bind_groups                                               
     0.44%  Tracy Sampling   ambition_game_bin                      [.] tracy::SysTraceWorker(void*)                                                                                                    
     0.43%  Compute Task Po  ambition_game_bin                      [.] _RNvXs3_NtCs1i8LOh7Gf22_18bevy_sprite_render6renderINtB5_25SetSpriteTextureBindGroupKj1_EINtNtNtCsjyMbgYCMBOv_11bevy_render12ren
     0.42%  ambition_game_b  ambition_game_bin                      [.] _mi_theap_realloc_zero                                                                                                          
     0.41%  Tracy Symbol Wo  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.41%  Compute Task Po  [kernel.kallsyms]                      [k] perf_adjust_freq_unthr_context                                                                                                  
```

## Assets and render resources

- Decoded images: 102 → 306 (179.0 MP, 716.1 MB of decode work).
- Images resident at end: 269.
- **Resident image BYTES at end: 716.1 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **174 images (137.4 MP)** at 15.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 20 GENERATED image(s) were allocated during gameplay (48.0 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

✔ FIRST ROOM: no notable decode landed in the second after the first `room-loaded` (frame 714) with a LATER frame stamp — the first room's art, the player's sheet included, was in before the route activated.

⛔ **8 of 34 notable decodes landed during SETTLED play** (59.0 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  18.0MP  at 11.397s  sprites/gnu_ton_boss/giant_gnu_spritesheet.png
  18.0MP  at 11.470s  sprites/gnu_ton_boss/gnu_ton_boss_spritesheet.png
   8.0MP  at 12.868s  sprites/perfect_cellular_automaton_parts.png
   5.3MP  at 11.053s  sprites/noether_parts.png
   5.2MP  at 11.153s  sprites/flying_spaghetti_monster_boss_parts.png
   2.5MP  at 11.836s  sprites/mockingbird_boss/mockingbird_boss_spritesheet.png
   1.0MP  at 11.030s  sprites/noether_vfx_spritesheet.png
   1.0MP  at 11.278s  sprites/georg_canter_parts.png
```

Textures decoded more than once:

```text
  21x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1339524 bytes

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

