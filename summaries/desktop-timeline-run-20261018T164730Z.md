# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `5b011190f765` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile --autoplay ` |
| host | `toothbrush` |
| kernel | `Linux toothbrush 6.8.0-147-generic #147-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep 24 11:33:16 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `ambition-autoplay` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=16
- MemTotal:       131727480 kB

## Renderer

```text
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.99.02", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **120.0s**.

## Frame time

116 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     13.3      49    20.9     9.5    78.4   198.2    198.2
      1.2      87     9.2     6.0    10.2    69.4    176.8
     79.9      37    27.2    20.4    64.3   115.4    115.4
     16.4      36    28.2    21.6    68.1    98.2     98.2
     31.5      44    22.7    20.5    36.4    80.3     80.3
     96.1      48    20.8    17.4    37.0    78.2     78.2
     14.3      53    20.2    15.7    47.0    67.5     74.9
     80.9      43    23.9    20.6    50.4    73.7     73.7
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   14.869s     198.2 ms
    2.637s     175.2 ms
   15.007s     137.6 ms
   81.066s     115.4 ms
   17.486s      96.3 ms
   32.886s      80.3 ms
   15.085s      78.3 ms
   97.307s      78.3 ms
   16.329s      75.6 ms
   82.281s      73.7 ms
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
    1       16     True  central_hub_complex -> hall_of_characters
    2       29     True  hall_of_characters -> central_hub_complex
    3       16     True  central_hub_complex -> primeval_trailhead
    4       17     True  primeval_trailhead -> primeval_run
    5       13     True  primeval_run -> primeval_rookery
    6       17     True  primeval_rookery -> primeval_ridge
    7       18     True  primeval_ridge -> primeval_canopy
    8       19     True  primeval_canopy -> primeval_tyrant_hall
    9       13     True  primeval_tyrant_hall -> primeval_canopy
   10       12     True  primeval_canopy -> primeval_ridge
   11       15     True  primeval_ridge -> primeval_rookery
   12       15     True  primeval_rookery -> primeval_run
   13       17     True  primeval_run -> primeval_trailhead
   14       17     True  primeval_trailhead -> central_hub_complex
   15        9     True  central_hub_complex -> hall_of_characters
   16       25     True  hall_of_characters -> central_hub_complex
   17       22     True  central_hub_complex -> primeval_trailhead
   18       15     True  primeval_trailhead -> primeval_run
   19       16     True  primeval_run -> primeval_rookery
   20       17     True  primeval_rookery -> primeval_ridge
   21       15     True  primeval_ridge -> primeval_canopy
```

Frames over 33.4 ms AFTER the last transition was logged (t=118.333s): **0**.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

⛔ **THE SPIKE LOG HIT ITS 60-LINE CAP, so this count is a FLOOR, not a total.** Percentile summaries continued; the per-frame lines did not.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1095 images / 446.8 MP in total; 111 were notable enough to print.

```text
ordered by: frame
before central_hub_complex: 7 image(s), 25.8 MP
  unknown                  2    16.4 MP
  character-sheet          2     3.2 MP
  vanity-card              1     3.0 MP
  boss-sheet               1     2.0 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.2        5       3      1       0      1
      5.2        5       3      1       0      1
     10.3        5       3      1       0      1
     15.3        5       3      1       0      1
     20.4       11       3      1       0      1
     25.4       11       3      1       0      1
     30.5       11       3      1       0      1
     35.6        5       3      1       0      1
     40.6        5       3      1       0      1
     45.6        5       3      1       0      1
     50.7        5       3      1       0      1
     55.7        7       5      1       2      1
     60.8        7       3      1       0      1
     65.8        5       3      1       0      1
     70.8        5       3      1       0      1
     75.9        5       3      1       0      1
     80.9       11       7      1       4      1
     86.0       11       5      1       2      1
     91.0       11       3      1       0      1
     96.1        7       3      1       0      1
    101.1        5       3      1       0      1
    106.1        5       3      1       0      1
    111.2        5       3      1       0      1
    116.2        5       3      1       0      1
```

Peak world-rendering cameras: **1** at t=0.2s.

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

Peak active portal capture rigs: **0** of 0 at t=0.2s.

```text
        t  rigs  active  budget
      0.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      9.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     27.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     36.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     45.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     54.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     63.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     72.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     81.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     91.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    100.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    109.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 576px) at t=16.4s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1643        0        0
       end      16384        2347       11        1
      peak      16384        2347      159        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **4685** (348 visible), text2d 100, per-view projections 25 at t=19.4s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3761** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.029          0.079      116  render/msaa_writeback/elapsed_gpu
         0.029          0.075      116  render/ui/elapsed_gpu
         0.029          0.065      101  render/main_opaque_pass_2d/elapsed_cpu
         0.025          0.048      116  render/ui/elapsed_cpu
         0.013          0.017      116  render/upscaling/elapsed_gpu
         0.007          0.020      101  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.005      116  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.018      116  render/msaa_writeback/elapsed_cpu
         0.002          0.008      116  render/upscaling/elapsed_cpu
         0.001          0.028      116  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    571140.621    4752342.000      116  render/ui/fragment_shader_invocations
    154390.812     331776.000      101  render/main_opaque_pass_2d/fragment_shader_invocations
       676.621        762.000      116  render/ui/vertex_shader_invocations
       337.310        380.000      116  render/ui/clipper_primitives_out
       337.310        380.000      116  render/ui/clipper_invocations
         4.000          4.000      101  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000      101  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000      101  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      116  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      116  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      116  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      116  render/main_transparent_pass_2d/clipper_primitives_out
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
  118076.4         1 118076378.2 118076378.2   100%  bevy_app
  118048.7         1 118048708.0 118048708.0   100%  render thread
  116949.6      7671   15245.7  323305.9     0%  update
  109047.3      7671   14215.5  280931.0     0%  main app
  109009.7      7671   14210.6  280923.4     0%  schedule{name=Main}
   45900.3      7671    5983.6  125609.4     0%  schedule{name=PreUpdate}
   37778.8      7671    4924.9  193549.2     1%  sub app{name=RenderApp}
   37708.8      7671    4915.8  193537.2     1%  schedule{name=RenderRecovery}
   37404.8      7671    4876.1  193435.2     1%  system{name="bevy_render::run_render_schedule"}
   37189.9      7671    4848.1  193391.4     1%  schedule{name=Render}
   35483.5      7671    4625.7  123693.7     0%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   34740.5      6320    5496.9  123430.4     0%  ggrs{name="HandleRequests"}
   34639.2      6320    5480.9  123412.9     0%  schedule{name="AdvanceWorld"}
   34561.4      6320    5468.6  123382.7     0%  schedule{name=AdvanceWorld}
   34250.4      6320    5419.4  123239.7     0%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   34195.9      6320    5410.7  123230.8     0%  schedule{name=GgrsSchedule}
   33702.6      7671    4393.5  176926.7     1%  schedule{name=Update}
   16468.8      7671    2146.9   19994.0     0%  schedule{name=PostUpdate}
   15277.7      7671    1991.6    9294.4     0%  system{name="bevy_render::renderer::render_system"}
   15255.4      7671    1988.7    9291.1     0%  main_render_schedule
   14037.3      7671    1829.9    8490.3     0%  schedule{name=RenderGraph}
    9680.3      7671    1261.9    7102.6     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    9099.8     24908     365.3    4303.3     0%  schedule{name=Core2d}
    7833.7      7671    1021.2  168532.4     2%  sub app{name=RenderExtractApp}
    7012.0   3893313       1.8     594.0     0%  multithreaded executor
    6928.9      7671     903.3   40306.0     1%  schedule{name=ExtractSchedule}
    4014.7      7670     523.4    3429.3     0%  camera_schedule{camera="Camera 0 (492v0)"}
    3158.1      7671     411.7    3901.6     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    3085.5      7671     402.2    5731.3     0%  schedule{name=Last}
    2633.9      7671     343.4    4317.9     0%  schedule{name=RunFixedMainLoop}
    2473.5      7670     322.5    4316.1     0%  camera_schedule{camera="Camera 9 (493v0)"}
    2473.0      6797     363.8    2690.5     0%  queue_submit{count=10}
    2289.5     24908      91.9    1437.5     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    2038.9      7670     265.8    4263.2     0%  camera_schedule{camera="Camera 7 (490v0)"}
    2017.4      7671     263.0    1137.8     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
    1769.9      7671     230.7    4119.1     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    1692.0      7536     224.5    3702.8     0%  schedule{name=FixedMain}
    1447.1     15342      94.3    1984.3     0%  schedule{name=First}
    1091.9      6320     172.8    1053.9     0%  system{name="ambition_platformer2d_actor_monolith::features::ecs::actors::update::integrat
    1079.5     24908      43.3    2596.5     0%  system{name="bevy_core_pipeline::core_2d::main_transparent_pass_2d_node::main_transparent_
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
  116626.3      15205.5      7671    5257.9  update
  108766.4      14180.8      7671    4700.0  main app
  108728.8      14175.8      7671    4697.1  schedule{name=Main}
   45774.7       5968.0      7671     695.6  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 7614 frames, summing to 15.37ms:

```text
    6.06 ms   39.4%  PreUpdate
    4.55 ms   29.6%  Update
    2.22 ms   14.4%  PostUpdate
    1.21 ms    7.9%  outside
    0.46 ms    3.0%  Last
    0.39 ms    2.5%  RunFixedMainLoop
    0.23 ms    1.5%  First
    0.14 ms    0.9%  StateTransition
    0.11 ms    0.7%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    6.06    16.12     2.66   PreUpdate
    4.55     7.74     1.70   Update
    2.22     4.00     1.80   PostUpdate
    1.21     3.88     3.21   outside
    0.46     0.68     1.49   Last
    0.39     0.80     2.07   RunFixedMainLoop
    0.23     0.64     2.77   First
    0.14     0.31     2.22   StateTransition
    0.11     0.18     1.59   SpawnScene
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
  80.5%  the game itself
  18.9%  profiler (Tracy)
   0.5%  audio
   0.2%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead : 18.9%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.2%   (cargo and shell; NOT a compile)
the game itself           : 80.5%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  83.1%  game binary + its Rust/C deps
  13.9%  kernel
   2.8%  GPU driver / graphics stack
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
     4.36%  Tracy Profiler   ambition_game_bin                      [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                      
     2.78%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layere
     2.11%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     1.84%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCsejQFhAU0ddf_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     1.64%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     1.49%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20so
     1.37%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCsi7WNBsjk6LE_18tracing_subscriber8registry7sharded9DataInnerE3getBU_
     1.34%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_
     1.01%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::NormalizePath(char const*)                                                                                               
     0.97%  ambition_game_b  ambition_game_bin                      [.] mi_theap_umalloc                                                                                                                
     0.87%  Tracy Profiler   libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.78%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                       
     0.77%  Tracy Profiler   libc.so.6                              [.] __strlen_evex                                                                                                                   
     0.77%  Compute Task Po  ambition_game_bin                      [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layere
     0.69%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.69%  ambition_game_b  ambition_game_bin                      [.] _RNvXs4_NtNtCsi7WNBsjk6LE_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5
     0.64%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.61%  ambition_game_b  ambition_game_bin                      [.] ___tracy_emit_zone_begin_alloc                                                                                                  
     0.60%  Compute Task Po  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.55%  ambition_game_b  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.55%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy::Que
     0.53%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsigned long, 
     0.52%  Tracy Profiler   ambition_game_bin                      [.] tracy::rpfree(void*)                                                                                                            
     0.52%  ambition_game_b  libc.so.6                              [.] __memcmp_evex_movbe                                                                                                             
     0.51%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::report_inlined_functions(unsigned long, tracy::function*, char const*, int (*)(void*, unsigned long, unsigned long, char 
     0.49%  Compute Task Po  ambition_game_bin                      [.] _RNvMs1_NtNtNtCsejQFhAU0ddf_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.46%  Compute Task Po  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.46%  Tracy Symbol Wo  libc.so.6                              [.] _dl_addr                                                                                                                        
     0.43%  ambition_game_b  ambition_game_bin                      [.] _mi_theap_realloc_zero                                                                                                          
     0.42%  Compute Task Po  [kernel.kallsyms]                      [k] perf_adjust_freq_unthr_context                                                                                                  
     0.41%  ambition_game_b  [kernel.kallsyms]                      [k] perf_adjust_freq_unthr_context                                                                                                  
     0.41%  Tracy Sampling   ambition_game_bin                      [.] tracy::SysTraceWorker(void*)                                                                                                    
     0.39%  Tracy Symbol Wo  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.37%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::backtrace_qsort(void*, unsigned long, unsigned long, int (*)(void const*, void const*)) [clone .localalias]              
     0.35%  ambition_game_b  ambition_game_bin                      [.] _RINvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB5_4PoolNtNtNtCsi7WNBsjk6LE_18tracing_subscriber8registry7sharded9DataInnerE11crea
```

## Assets and render resources

- Decoded images: 59 → 1094 (446.7 MP, 1786.7 MB of decode work).
- Images resident at end: 231.
- **Resident image BYTES at end: 1787.3 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **317 images (177.0 MP)** at 15.1s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 6 GENERATED image(s) were allocated during gameplay (21.4 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 16 notable decode(s) (18.4 MP) landed AFTER the first `room-loaded` (frame 1648) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.0MP  f   1650  backgrounds/parallax_layers/cove_foreground.png
   1.0MP  f   1650  backgrounds/parallax_layers/cove_far_backplate.png
   1.0MP  f   1650  backgrounds/parallax_layers/lab_far_backplate.png
   1.0MP  f   1650  backgrounds/parallax_layers/lab_sky.png
   1.6MP  f   1650  backgrounds/parallax_layers/cove_near_background.png
   1.6MP  f   1650  backgrounds/parallax_layers/boss_near_background.png
   1.0MP  f   1650  backgrounds/parallax_layers/cove_sky.png
   1.0MP  f   1650  backgrounds/parallax_layers/lab_foreground.png
```

⚠ 98 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   7x  <runtime-generated>
   4x  backgrounds/parallax_layers/cove_foreground.png
   4x  backgrounds/parallax_layers/cove_far_backplate.png
   4x  backgrounds/parallax_layers/lab_far_backplate.png
   4x  backgrounds/parallax_layers/lab_sky.png
   4x  backgrounds/parallax_layers/cove_near_background.png
   4x  backgrounds/parallax_layers/cove_sky.png
   4x  backgrounds/parallax_layers/lab_foreground.png
   4x  backgrounds/parallax_layers/lab_near_background.png
   4x  backgrounds/parallax_layers/basement_near_background.png
   4x  backgrounds/parallax_layers/basement_foreground.png
   4x  backgrounds/parallax_layers/basement_sky.png
   4x  backgrounds/parallax_layers/basement_far_backplate.png
   3x  backgrounds/parallax_layers/primeval_sky.png
   3x  backgrounds/parallax_layers/primeval_far_backplate.png
   3x  backgrounds/parallax_layers/primeval_near_background.png
   2x  backgrounds/parallax_layers/hub_foreground.png
   2x  backgrounds/parallax_layers/hub_far_backplate.png
   2x  backgrounds/parallax_layers/hub_near_background.png
   2x  backgrounds/parallax_layers/hub_sky.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 124
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1313596 bytes

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
| `alloc_by_span.csv` | perf_probe: allocations per Bevy system / tracing span | no |
| `churn_by_site.csv` | perf_probe: spawns, despawns, inserts, removes per source line | no |
| `changes_by_site.csv` | perf_probe: component and resource values marked changed, per source line | no |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

