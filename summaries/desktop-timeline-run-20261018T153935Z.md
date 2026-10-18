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

115 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.0      29    20.0     7.1   139.5   227.4    227.4
     14.1      84    13.6     7.4    14.0   184.3    217.9
     17.2      33    31.9    18.4   102.3   159.9    159.9
     80.7      37    27.3    23.4    46.0    88.8     88.8
     15.1      49    19.4    14.6    58.0    87.5     87.5
     81.7      47    21.6    17.0    44.0    87.2     87.2
     97.9      46    21.8    18.8    39.3    86.1     86.1
     32.3      41    24.4    21.0    38.6    80.9     80.9
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    4.165s     226.4 ms
   16.391s     217.9 ms
   16.576s     184.8 ms
   19.370s     159.9 ms
    3.939s     140.0 ms
   19.474s     103.2 ms
   19.157s     102.5 ms
   16.664s      88.3 ms
   34.512s      80.9 ms
   18.492s      60.2 ms
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
    1       15     True  central_hub_complex -> hall_of_characters
    2       20     True  hall_of_characters -> central_hub_complex
    3       17     True  central_hub_complex -> primeval_trailhead
    4       14     True  primeval_trailhead -> primeval_run
    5       19     True  primeval_run -> primeval_rookery
    6       21     True  primeval_rookery -> primeval_ridge
    7       19     True  primeval_ridge -> primeval_canopy
    8       24     True  primeval_canopy -> primeval_tyrant_hall
    9        8     True  primeval_tyrant_hall -> primeval_canopy
   10       16     True  primeval_canopy -> primeval_ridge
   11       16     True  primeval_ridge -> primeval_rookery
   12       16     True  primeval_rookery -> primeval_run
   13       18     True  primeval_run -> primeval_trailhead
   14       18     True  primeval_trailhead -> central_hub_complex
   15       20     True  central_hub_complex -> hall_of_characters
   16       19     True  hall_of_characters -> central_hub_complex
   17       18     True  central_hub_complex -> primeval_trailhead
   18       17     True  primeval_trailhead -> primeval_run
   19       17     True  primeval_run -> primeval_rookery
   20       14     True  primeval_rookery -> primeval_ridge
```

Frames over 33.4 ms AFTER the last transition was logged (t=115.679s): **0**.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

⛔ **THE SPIKE LOG HIT ITS 60-LINE CAP, so this count is a FLOOR, not a total.** Percentile summaries continued; the per-frame lines did not.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1092 images / 446.5 MP in total; 111 were notable enough to print.

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
      1.0        5       3      1       0      1
      6.0        5       3      1       0      1
     11.1        5       3      1       0      1
     16.1        5       3      1       0      1
     21.2       11       3      1       0      1
     26.2       11       5      1       2      1
     31.3       11       5      1       2      1
     36.3        5       3      1       0      1
     41.4        5       3      1       0      1
     46.4        5       3      1       0      1
     51.4        5       3      1       0      1
     56.5        5       3      1       0      1
     61.5        7       3      1       0      1
     66.6        5       3      1       0      1
     71.6        5       3      1       0      1
     76.7        5       3      1       0      1
     81.7        5       3      1       0      1
     86.8       11       5      1       2      1
     91.9       11       3      1       0      1
     96.9       11       3      1       0      1
    101.9        5       3      1       0      1
    107.0        5       3      1       0      1
    112.0        5       3      1       0      1
    117.1        5       3      1       0      1
```

Peak world-rendering cameras: **1** at t=1.0s.

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

Peak active portal capture rigs: **0** of 0 at t=1.0s.

```text
        t  rigs  active  budget
      1.0     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     10.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     19.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     28.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     37.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     46.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     55.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     64.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     73.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     82.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     91.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    100.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    110.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 576px) at t=17.2s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1643        0        0
       end      16384        2348       10        1
      peak      16384        2348      159        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **4685** (338 visible), text2d 170, per-view projections 39 at t=92.9s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3761** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.038          0.407      100  render/main_opaque_pass_2d/elapsed_cpu
         0.027          0.070      115  render/ui/elapsed_gpu
         0.026          0.031      115  render/msaa_writeback/elapsed_gpu
         0.025          0.059      115  render/ui/elapsed_cpu
         0.012          0.033      115  render/upscaling/elapsed_gpu
         0.006          0.008      100  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.025      115  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.010      115  render/msaa_writeback/elapsed_cpu
         0.002          0.008      115  render/upscaling/elapsed_cpu
         0.001          0.005      115  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    573605.939    4751052.000      115  render/ui/fragment_shader_invocations
    157593.600     331776.000      100  render/main_opaque_pass_2d/fragment_shader_invocations
       672.122        762.000      115  render/ui/vertex_shader_invocations
       335.061        380.000      115  render/ui/clipper_primitives_out
       335.061        380.000      115  render/ui/clipper_invocations
         4.000          4.000      100  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000      100  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000      100  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      115  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      115  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      115  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      115  render/main_transparent_pass_2d/clipper_primitives_out
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
  117661.4         1 117661439.0 117661439.0   100%  bevy_app
  117591.3         1 117591258.1 117591258.1   100%  render thread
  115966.3      7382   15709.3  780312.8     1%  update
  107982.2      7382   14627.8  662973.4     1%  main app
  107944.6      7382   14622.7  662968.1     1%  schedule{name=Main}
   45804.7      7382    6204.9  150800.5     0%  schedule{name=PreUpdate}
   36719.5      7382    4974.2  234495.7     1%  sub app{name=RenderApp}
   36649.7      7382    4964.7  234488.3     1%  schedule{name=RenderRecovery}
   36358.7      7382    4925.3  234382.5     1%  system{name="bevy_render::run_render_schedule"}
   36149.6      7382    4897.0  234345.1     1%  schedule{name=Render}
   35559.4      7382    4817.0  149200.3     0%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   34809.3      6230    5587.4  148877.7     0%  ggrs{name="HandleRequests"}
   34710.0      6230    5571.4  148859.0     0%  schedule{name="AdvanceWorld"}
   34629.6      6230    5558.5  148825.0     0%  schedule{name=AdvanceWorld}
   34316.4      6230    5508.3  148669.7     0%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   34262.2      6230    5499.5  148665.1     0%  schedule{name=GgrsSchedule}
   33359.1      7382    4519.0  359642.8     1%  schedule{name=Update}
   15960.0      7382    2162.0   26435.4     0%  schedule{name=PostUpdate}
   14919.2      7382    2021.0   35592.9     0%  system{name="bevy_render::renderer::render_system"}
   14897.0      7382    2018.0   35589.6     0%  main_render_schedule
   13707.1      7382    1856.8   35108.8     0%  schedule{name=RenderGraph}
    9432.1      7382    1277.7   16528.4     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    8865.8     24019     369.1   14988.6     0%  schedule{name=Core2d}
    7916.4      7382    1072.4  217530.9     3%  sub app{name=RenderExtractApp}
    6918.2      7382     937.2  100203.9     1%  schedule{name=ExtractSchedule}
    6832.4   3744938       1.8    4048.1     0%  multithreaded executor
    3872.3      7381     524.6    4210.6     0%  camera_schedule{camera="Camera 0 (492v0)"}
    3116.5      7382     422.2   31988.5     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    3016.6      7382     408.6    7748.1     0%  schedule{name=Last}
    2625.7      7382     355.7    3930.8     0%  schedule{name=RunFixedMainLoop}
    2442.7      6531     374.0   31928.7     1%  queue_submit{count=10}
    2397.3      7381     324.8    3804.8     0%  camera_schedule{camera="Camera 9 (493v0)"}
    2201.9     24019      91.7    2118.1     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    2018.4      7381     273.5   14995.6     1%  camera_schedule{camera="Camera 7 (490v0)"}
    1959.2      7382     265.4    1004.7     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
    1785.3      7382     241.8    3821.1     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    1711.5      7441     230.0    3810.1     0%  schedule{name=FixedMain}
    1554.9         8  194363.3  909106.6    58%  asset loading{loader="bevy_image::image_loader::ImageLoader" asset="sprites/gnu_ton_boss/g
    1433.2     14764      97.1    2330.3     0%  schedule{name=First}
    1162.0         1 1162030.0 1162030.0   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
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
  115185.9      15605.7      7382    5369.0  update
  107319.2      14539.9      7382    4705.9  main app
  107281.7      14534.8      7382    4703.8  schedule{name=Main}
   45653.9       6185.3      7382     711.0  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 7339 frames, summing to 15.81ms:

```text
    6.28 ms   39.7%  PreUpdate
    4.68 ms   29.6%  Update
    2.24 ms   14.2%  PostUpdate
    1.26 ms    8.0%  outside
    0.46 ms    2.9%  Last
    0.40 ms    2.5%  RunFixedMainLoop
    0.23 ms    1.5%  First
    0.14 ms    0.9%  StateTransition
    0.11 ms    0.7%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    6.28    16.86     2.68   PreUpdate
    4.68     8.10     1.73   Update
    2.24     4.11     1.84   PostUpdate
    1.26     4.07     3.22   outside
    0.46     0.70     1.51   Last
    0.40     0.82     2.06   RunFixedMainLoop
    0.23     0.65     2.76   First
    0.14     0.30     2.20   StateTransition
    0.11     0.17     1.49   SpawnScene
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
   0.4%  audio
   0.1%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead : 18.9%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           : 80.5%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  80.3%  game binary + its Rust/C deps
  16.9%  kernel
   2.7%  GPU driver / graphics stack
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
     4.77%  Tracy Profiler   ambition_game_bin                 [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                           
     2.64%  ambition_game_b  ambition_game_bin                 [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     2.18%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     1.69%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCsejQFhAU0ddf_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     1.51%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20sort_un
     1.50%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     1.27%  ambition_game_b  ambition_game_bin                 [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCsi7WNBsjk6LE_18tracing_subscriber8registry7sharded9DataInnerE3getBU_     
     1.06%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_keyjN
     0.99%  ambition_game_b  ambition_game_bin                 [.] mi_theap_umalloc                                                                                                                     
     0.90%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::NormalizePath(char const*)                                                                                                    
     0.75%  Compute Task Po  ambition_game_bin                 [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     0.74%  Tracy Profiler   libc.so.6                         [.] __strlen_evex                                                                                                                        
     0.67%  ambition_game_b  ambition_game_bin                 [.] _RNvXs4_NtNtCsi7WNBsjk6LE_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5layer
     0.65%  Tracy Profiler   libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.64%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.62%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsigned long, char 
     0.59%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::rpmalloc(unsigned long)                                                                                                       
     0.56%  Tracy Profiler   ambition_game_bin                 [.] tracy::rpfree(void*)                                                                                                                 
     0.56%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                            
     0.55%  ambition_game_b  ambition_game_bin                 [.] tracy::rpmalloc(unsigned long)                                                                                                       
     0.52%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtNtNtCsejQFhAU0ddf_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.51%  ambition_game_b  ambition_game_bin                 [.] ___tracy_emit_zone_begin_alloc                                                                                                       
     0.51%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::report_inlined_functions(unsigned long, tracy::function*, char const*, int (*)(void*, unsigned long, unsigned long, char const
     0.50%  Compute Task Po  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.48%  Tracy Symbol Wo  libc.so.6                         [.] _dl_addr                                                                                                                             
     0.47%  ambition_game_b  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     0.46%  Compute Task Po  ambition_game_bin                 [.] tracy::rpmalloc(unsigned long)                                                                                                       
     0.43%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy::QueueIte
     0.42%  ambition_game_b  ambition_game_bin                 [.] _mi_theap_realloc_zero                                                                                                               
     0.40%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::backtrace_qsort(void*, unsigned long, unsigned long, int (*)(void const*, void const*)) [clone .localalias]                   
     0.37%  Tracy Symbol Wo  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.37%  ambition_game_b  [kernel.kallsyms]                 [k] perf_adjust_freq_unthr_context                                                                                                       
     0.36%  ambition_game_b  ambition_game_bin                 [.] _RINvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB5_4PoolNtNtNtCsi7WNBsjk6LE_18tracing_subscriber8registry7sharded9DataInnerE11create_wi
     0.36%  Compute Task Po  ambition_game_bin                 [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCsi7WNBsjk6LE_18tracing_subscriber8registry7sharded9DataInnerE3getBU_     
     0.36%  Compute Task Po  [kernel.kallsyms]                 [k] perf_adjust_freq_unthr_context                                                                                                       
```

## Assets and render resources

- Decoded images: 48 → 1092 (446.5 MP, 1786.1 MB of decode work).
- Images resident at end: 232.
- **Resident image BYTES at end: 1786.1 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **245 images (146.9 MP)** at 85.2s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 6 GENERATED image(s) were allocated during gameplay (21.4 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 16 notable decode(s) (18.4 MP) landed AFTER the first `room-loaded` (frame 1619) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.0MP  f   1621  backgrounds/parallax_layers/cove_sky.png
   1.0MP  f   1621  backgrounds/parallax_layers/cove_foreground.png
   1.6MP  f   1621  backgrounds/parallax_layers/cove_near_background.png
   1.0MP  f   1621  backgrounds/parallax_layers/cove_far_backplate.png
   1.0MP  f   1621  backgrounds/parallax_layers/lab_sky.png
   1.0MP  f   1622  backgrounds/parallax_layers/lab_far_backplate.png
   1.6MP  f   1622  backgrounds/parallax_layers/lab_near_background.png
   1.0MP  f   1622  backgrounds/parallax_layers/lab_foreground.png
```

⚠ 98 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   7x  <runtime-generated>
   4x  backgrounds/parallax_layers/cove_sky.png
   4x  backgrounds/parallax_layers/cove_foreground.png
   4x  backgrounds/parallax_layers/cove_near_background.png
   4x  backgrounds/parallax_layers/cove_far_backplate.png
   4x  backgrounds/parallax_layers/lab_sky.png
   4x  backgrounds/parallax_layers/lab_far_backplate.png
   4x  backgrounds/parallax_layers/lab_near_background.png
   4x  backgrounds/parallax_layers/lab_foreground.png
   4x  backgrounds/parallax_layers/basement_far_backplate.png
   4x  backgrounds/parallax_layers/basement_sky.png
   4x  backgrounds/parallax_layers/basement_foreground.png
   4x  backgrounds/parallax_layers/basement_near_background.png
   3x  backgrounds/parallax_layers/primeval_sky.png
   3x  backgrounds/parallax_layers/primeval_far_backplate.png
   3x  backgrounds/parallax_layers/primeval_near_background.png
   2x  backgrounds/parallax_layers/hub_foreground.png
   2x  backgrounds/parallax_layers/hub_far_backplate.png
   2x  backgrounds/parallax_layers/hub_sky.png
   2x  backgrounds/parallax_layers/hub_near_background.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 124
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1331824 bytes

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

