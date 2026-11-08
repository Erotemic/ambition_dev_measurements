# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `c62122dfe5c0` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/smash_match_profile` |
| capture target | `direct-built-binary` |
| package / bin | `ambition_app_tools` / `smash_match_profile` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.99.1 (58cf8f9c3 2026-10-13)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/target/profiling/smash_match_profile --window --fighters 4 --seconds 90 ` |
| host | `toothbrush` |
| kernel | `Linux toothbrush 6.8.0-147-generic #147-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep 24 11:33:16 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `smash-match-4p` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

## Smash match coverage

- Entered live match: **yes**.
- All-CPU roster in log: **yes**.
- Completed measured window: **yes**.
- **Round may have ended early**; later frames may be menus/results.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=16
- MemTotal:       131727480 kB

## Frame pacing

| observed frame cap | observed present mode |
| --- | --- |
| `Auto` | `Fifo` |

**REFRESH/CAP-LIMITED BENCHMARK:** VSync (`Fifo`) and/or a frame
cap were active. Steady FPS near the monitor refresh rate is
not the game's uncapped throughput. Native hotspot and stall
measurements are still informative.

## Renderer

```text
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.99.02", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **101.5s**.

## Frame time

91 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      6.7      38    34.0    16.8    44.5   393.9    393.9
      5.4      11    42.7    17.2   193.9   193.9    193.9
      7.7      15    74.7    58.6   134.9   140.3    140.3
      8.7      43    22.1    19.3    47.2    54.5     54.5
     38.0      42    23.8    22.2    45.8    51.6     51.6
     42.0      51    19.7    16.9    33.0    49.6     49.6
     18.8      46    21.5    19.6    34.2    47.3     47.3
     40.0      46    21.8    20.0    30.6    43.8     43.8
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    9.882s     393.9 ms
    9.231s     281.3 ms
    8.502s     199.2 ms
   10.067s     141.1 ms
    8.303s     141.0 ms
   10.913s     133.9 ms
   11.045s     133.2 ms
   10.777s      85.5 ms
   10.427s      70.7 ms
   10.196s      67.3 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1011 images / 477.0 MP in total; 157 were notable enough to print.

```text
ordered by: frame
before smash_stage: 11 image(s), 29.6 MP
  unknown                  5    21.3 MP
  parallax                 4     4.6 MP
  boss-sheet               1     2.5 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      4.4        4       3      1       0      1
      8.7        8       5      1       2      1
     12.8        8       7      1       4      1
     16.8        8       5      1       2      1
     20.8       10       5      1       2      1
     24.9       12       7      1       4      1
     28.9       12       5      1       2      1
     33.0       12       5      1       2      1
     37.0       12       7      1       4      1
     41.0       10       3      1       0      1
     45.1       12       5      1       2      1
     49.1       10       3      1       0      1
     53.1       12       5      1       2      1
     57.1       12       7      1       4      1
     61.2       12       5      1       2      1
     65.2       12       3      1       0      1
     69.2       12       7      1       4      1
     73.3       12       3      1       0      1
     77.3        8       3      1       0      1
     81.3       10       3      1       0      1
     85.3       10       3      1       0      1
     89.4       10       3      1       0      1
     93.4       10       3      1       0      1
```

Peak world-rendering cameras: **1** at t=4.4s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
       offscreen  Cast bubble camera, rigged impostor camera, rigged impostor unpremultiply camera
           other  Cube scrim display camera, Developer Panel Camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=4.4s.

```text
        t  rigs  active  budget
      4.4     0       0  captures<=2
     11.8     0       0  captures<=2
     18.8     0       0  captures<=2
     25.9     0       0  captures<=2
     33.0     0       0  captures<=2
     40.0     0       0  captures<=2
     47.1     0       0  captures<=2
     54.1     0       0  captures<=2
     61.2     0       0  captures<=2
     68.2     0       0  captures<=2
     75.3     0       0  captures<=2
     82.3     0       0  captures<=2
     89.4     0       0  captures<=2
     96.4     0       0  captures<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **8** (largest dimension 1152px) at t=21.9s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1694        0        0
       end       4096        2098        0        0
      peak       4096        2098        4        0
```

Entity count rose by 2048 and then held flat at 4096 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **515** (486 visible), text2d 25, per-view projections 9 at t=34.0s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3870** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.053          0.190       91  render/ui/elapsed_gpu
         0.046          0.096       91  render/msaa_writeback/elapsed_gpu
         0.027          0.122       87  render/main_opaque_pass_2d/elapsed_cpu
         0.027          0.076       91  render/ui/elapsed_cpu
         0.021          0.034       91  render/upscaling/elapsed_gpu
         0.012          0.033       87  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.006       91  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.009       91  render/msaa_writeback/elapsed_cpu
         0.003          0.037       91  render/upscaling/elapsed_cpu
         0.001          0.010       91  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    852101.473   13364751.000       91  render/ui/fragment_shader_invocations
    694676.172    1327104.000       87  render/main_opaque_pass_2d/fragment_shader_invocations
       561.912       2454.000       91  render/ui/vertex_shader_invocations
       379.637       8658.000       91  render/main_transparent_pass_2d/fragment_shader_invocations
       279.956       1226.000       91  render/ui/clipper_primitives_out
       279.956       1226.000       91  render/ui/clipper_invocations
        20.690         70.000       87  render/main_opaque_pass_2d/vertex_shader_invocations
        17.678         64.000       87  render/main_opaque_pass_2d/clipper_invocations
        17.678         64.000       87  render/main_opaque_pass_2d/clipper_primitives_out
         1.407         32.000       91  render/main_transparent_pass_2d/vertex_shader_invocations
         1.319         30.000       91  render/main_transparent_pass_2d/clipper_invocations
         1.319         30.000       91  render/main_transparent_pass_2d/clipper_primitives_out
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
   95641.6         1 95641627.4 95641627.4   100%  bevy_app
   95564.7         1 95564687.5 95564687.5   100%  render thread
   93094.1      6294   14790.9 2438943.7     3%  update
   85789.1      6294   13630.3 2348946.8     3%  main app
   85757.1      6294   13625.2 2348938.2     3%  schedule{name=Main}
   35029.8      6294    5565.6  319471.3     1%  schedule{name=PreUpdate}
   34470.4      6294    5476.7  233007.5     1%  sub app{name=RenderApp}
   34413.8      6294    5467.7  233000.6     1%  schedule{name=RenderRecovery}
   34162.6      6294    5427.8  231686.0     1%  system{name="bevy_render::run_render_schedule"}
   33988.8      6294    5400.2  231624.1     1%  schedule{name=Render}
   26978.6      6294    4286.4  318306.2     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   26618.4      4661    5710.9  318171.8     1%  ggrs{name="HandleRequests"}
   26550.0      4661    5696.2  318163.1     1%  schedule{name="AdvanceWorld"}
   26492.1      4661    5683.8  318138.3     1%  schedule{name=AdvanceWorld}
   26265.7      4661    5635.2  318028.0     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   26223.9      4661    5626.2  318020.6     1%  schedule{name=GgrsSchedule}
   25293.2      6294    4018.6  503525.2     2%  schedule{name=Update}
   13959.8      6294    2218.0   50171.7     0%  schedule{name=PostUpdate}
   13630.5      6294    2165.6   13533.3     0%  system{name="bevy_render::renderer::render_system"}
   13611.1      6294    2162.6   13527.6     0%  main_render_schedule
   12610.7      6294    2003.6   12836.1     0%  schedule{name=RenderGraph}
    8849.5      6294    1406.0    9804.2     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    8337.5     24206     344.4    4890.1     0%  schedule{name=Core2d}
    7237.3      6294    1149.9  182377.1     3%  sub app{name=RenderExtractApp}
    6241.4      6294     991.6   83084.5     1%  schedule{name=ExtractSchedule}
    5738.7   3170105       1.8    1798.0     0%  multithreaded executor
    5224.2      4661    1120.8   39817.9     1%  system{name="ambition_platformer2d_actor_monolith::body::actors::update::tick_actor_brains
    3861.8      6294     613.6    5210.7     0%  schedule{name=RunFixedMainLoop}
    3301.7      6294     524.6    5138.0     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    3233.6      5883     549.7    5127.4     0%  schedule{name=FixedMain}
    2862.5      5883     486.6    5061.2     0%  schedule{name=FixedPostUpdate}
    2763.4      6294     439.1    5819.8     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2561.1      6293     407.0    3284.9     0%  camera_schedule{camera="Camera 0 (500v0)"}
    2352.1         1 2352061.9 2352061.9   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
    2336.3      6294     371.2    8806.4     0%  schedule{name=Last}
    2237.7      6293     355.6    3650.7     0%  camera_schedule{camera="Camera 9 (501v0)"}
    1800.9         1 1800873.6 1800873.6   100%  schedule{name=Startup}
    1791.4      5883     304.5    3667.9     0%  system{name="avian2d::schedule::run_physics_schedule"}
    1746.4      6293     277.5    3609.9     0%  camera_schedule{camera="Camera 7 (502v0)"}
    1744.6      1920     908.6    3646.2     0%  schedule{name=PhysicsSchedule}
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
   90655.2      14405.7      6294    5677.1  update
   83440.2      13259.2      6294    4869.1  main app
   83408.1      13254.1      6294    4866.5  schedule{name=Main}
   34710.3       5515.7      6294     714.5  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw `tracy.trace`: local capture directory only.

## Which phase of the frame owned the time

Mean milliseconds per frame over 6220 frames, summing to 14.79ms:

```text
    5.64 ms   38.2%  PreUpdate
    4.09 ms   27.6%  Update
    2.26 ms   15.2%  PostUpdate
    1.39 ms    9.4%  outside
    0.63 ms    4.3%  RunFixedMainLoop
    0.40 ms    2.7%  Last
    0.20 ms    1.4%  First
    0.10 ms    0.7%  StateTransition
    0.08 ms    0.5%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    5.64    16.61     2.94   PreUpdate
    4.09     8.15     1.99   Update
    2.26     4.39     1.95   PostUpdate
    1.39     4.42     3.17   outside
    0.63     1.43     2.26   RunFixedMainLoop
    0.40     0.66     1.66   Last
    0.20     0.52     2.56   First
    0.10     0.24     2.47   StateTransition
    0.08     0.12     1.56   SpawnScene
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
  78.0%  the game itself
  21.6%  profiler (Tracy)
   0.4%  audio
```

```text
profiler (Tracy) overhead : 21.6%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           : 78.0%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  79.1%  game binary + its Rust/C deps
  18.3%  kernel
   2.5%  GPU driver / graphics stack
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
    39.05%     0.00%  smash_match_pro  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
    26.37%     0.04%  smash_match_pro  smash_match_profile                    [.] _RNvMs1_NtNtCs6STjheAsjvA_8bevy_ecs8schedule8scheduleNtB5_8Schedule3run                                               
    25.51%     1.98%  smash_match_pro  smash_match_profile                    [.] _RNvXNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecuto
    16.86%     0.00%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World18try_schedule_scopeuINtNtB8_6intern8InternedDNtNtNtB8_8schedule3se
    10.63%     0.04%  smash_match_pro  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace29run_without_applying_deferred     
    10.44%     0.10%  Compute Task Po  smash_match_profile                    [.] _RINvMs2_NtNtCs8ZKwes1zzDd_3std6thread5localINtB6_8LocalKeyINtNtCskADBYZwJmSU_4core4cell7RefCellTNtCsbsL9vopLbSS_7park
    10.25%     0.18%  Compute Task Po  smash_match_profile                    [.] _RNvXs5_NtCscJLDsxKi8GJ_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNtB19_
     9.81%     0.01%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Worker()                                                                                             
     9.76%     0.00%  Tracy Profiler   libc.so.6                              [.] clone3                                                                                                                
     9.76%     0.00%  Tracy Profiler   libc.so.6                              [.] start_thread                                                                                                          
     9.76%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Thread::Launch(void*)                                                                                          
     9.50%     0.00%  Compute Task Po  smash_match_profile                    [.] _RINvNtNtCs8ZKwes1zzDd_3std3sys9backtrace28___rust_begin_short_backtraceNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_po
     8.91%     0.11%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                           
     8.52%     0.07%  Compute Task Po  smash_match_profile                    [.] _RNvMs1_NtCs9Rcz90M69zT_10async_task3rawINtB5_7RawTaskINtCsjFFWrCkEdj5_14async_executor15AsyncCallOnDropINtNtCscJLDsxK
     8.40%     0.03%  Compute Task Po  smash_match_profile                    [.] _RNvXso_NtNtCskADBYZwJmSU_4core5panic11unwind_safeINtB5_16AssertUnwindSafeNCNvMs3_NtNtNtCs6STjheAsjvA_8bevy_ecs8schedu
     7.85%     0.51%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(
     7.72%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNSNvYNCINvNtNtCs8ZKwes1zzDd_3std6thread9lifecycle15spawn_uncheckedNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNt
     7.68%     0.00%  Compute Task Po  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
     6.86%     0.00%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World14schedule_scopeuNtCsaMjW6A3uaLQ_9bevy_ggrs12GgrsScheduleNCINvB2_12
     6.77%     0.26%  Compute Task Po  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                        
     5.97%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNvNvMs0_NtNtNtCs8ZKwes1zzDd_3std3sys6thread4unixNtB7_6Thread3new12thread_start                                      
     5.96%     0.00%  Tracy Symbol Wo  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
     5.70%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::CommitData()                                                                                         
     5.69%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::SendData(char const*, unsigned long)                                                                 
     4.99%     4.79%  Tracy Profiler   smash_match_profile                    [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                            
     4.97%     0.00%  Compute Task Po  libc.so.6                              [.] start_thread                                                                                                          
     4.91%     0.74%  Tracy Symbol Wo  smash_match_profile                    [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsig
     4.86%     2.25%  smash_match_pro  smash_match_profile                    [.] _RNvXs1_CsauKbtVXGAam_13tracing_tracyNtB5_10TracyLayerINtNtCshdbjT2rFsCC_18tracing_subscriber5layer5LayerINtNtBS_7laye
     4.55%     0.01%  Tracy Symbol Wo  smash_match_profile                    [.] tracy::Profiler::SymbolWorker()                                                                                       
     4.50%     0.00%  Tracy Symbol Wo  smash_match_profile                    [.] tracy::Thread::Launch(void*)                                                                                          
     4.50%     0.00%  Tracy Symbol Wo  libc.so.6                              [.] start_thread                                                                                                          
     4.46%     0.05%  Tracy Symbol Wo  smash_match_profile                    [.] tracy::Profiler::HandleSymbolQueueItem(tracy::Profiler::SymbolQueueItem const&)                                       
     4.42%     0.00%  Tracy Symbol Wo  libc.so.6                              [.] clone3                                                                                                                
     4.22%     0.00%  Compute Task Po  libc.so.6                              [.] clone3                                                                                                                
     4.19%     0.08%  Tracy Symbol Wo  smash_match_profile                    [.] tracy::DecodeCallstackPtr(unsigned long)                                                                              
```

## Assets and render resources

- Decoded images: 139 → 1011 (477.0 MP, 1908.1 MB of decode work).
- Images resident at end: 865.
- **Resident image BYTES at end: 1908.1 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **671 images (331.8 MP)** at 10.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 44 GENERATED image(s) were allocated during gameplay (57.2 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 38 notable decode(s) (92.3 MP) landed AFTER the first `room-loaded` (frame 47) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.3MP  f     50  content://ambition_shared/__art/bundles/7b2e18c59fd353969aa1ef3a7dab32478b27a46fb01246109215292bfac1350c/runtime/full/sprites/castle_cells_room_spritesheet.png
   1.7MP  f     50  content://ambition_shared/__art/bundles/5bbbec296222fe797dcca2fb9b56cf7682229d9cb7cac1403c599e1574151f93/runtime/full/sprites/castle_guild_room_spritesheet.png
   1.2MP  f     50  content://ambition_shared/__art/bundles/580b3cdf846906cc0f8f7ab19e24d352217f6ede35909fdd195c5049800e98aa/runtime/full/sprites/rift_outlines_spritesheet.png
   2.3MP  f     50  content://ambition_shared/__art/bundles/57be1a6ff4a899edcbd1b493ac864347541ed0308bfbb3925d1ad6634794c6d3/runtime/full/sprites/castle_kitchens_room_spritesheet.png
   3.9MP  f     50  content://ambition_shared/__art/bundles/b86e5bdbc5114d3ff6af7577f0536af0b6db8234a273d297c9142718a9cface3/runtime/full/sprites/math_hotel_lift_shaft_spritesheet.png
   1.8MP  f     50  content://ambition_shared/__art/bundles/71c513e455c2e877e9b4a1a7a605658e02599f66a6d1452779827c81cd4b6e81/runtime/full/sprites/roots_east_spritesheet.png
   2.4MP  f     50  content://ambition_shared/__art/bundles/48d101a50c2a45a674358860dcf34cf8eedcebbbd9ff253d2d3abbc444bd85ac/runtime/full/sprites/catacombs_east_spritesheet.png
   2.4MP  f     50  content://ambition_shared/__art/bundles/5faf2c9ccd6d495cb3109215eba963acd95df15e7928f9868303bee552eacb12/runtime/full/sprites/math_hotel_lobby_room_spritesheet.png
```

⚠ 86 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  45x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 108261380 bytes

## Files in the local capture directory and shareable archive

Raw `perf.data` and `tracy.trace` are retained in the local capture
directory but omitted from the default shareable archive. Use
`--include-raw-data` to include them in the archive.

| file | contents | local | archived |
| --- | --- | --- | --- |
| `summary.md` | this file | yes | yes |
| `metadata.txt / metadata.json` | build, commit, host, and capture settings | yes | yes |
| `host-environment.txt` | CPU, GPU, DRM nodes, Vulkan ICDs, graphics env overrides | yes | yes |
| `timeline.md` | per-window perf symbols labelled with the game's own log markers | yes | yes |
| `frame_times.csv` | per-census-window frame-time percentiles | yes | yes |
| `frame_spikes.csv` | every frame over 33.4ms, with its wall-clock second | yes | yes |
| `frame_windows.csv` | the always-on 5s frame census | yes | yes |
| `camera_views.csv` | one row per camera per sample: role, target, size, layers | yes | yes |
| `view_totals.csv` | camera/active/world-rendering/offscreen counts per sample | yes | yes |
| `runtime_census.csv` | entity, archetype, component, body, and player counts | yes | yes |
| `draw_census.csv` | sprite/text/projection population and visibility | yes | yes |
| `render_target_census.csv` | offscreen image targets and their bytes | yes | yes |
| `render_diagnostics.csv` | Bevy per-pass CPU/GPU times and pipeline statistics | yes | yes |
| `portal_activity.csv` | portal capture rigs and the budget bounding them | yes | yes |
| `asset_activity.csv` | cumulative decode work and resident images | yes | yes |
| `image_decodes.csv` | every notable texture decode, with its path | yes | yes |
| `image_arrivals.csv` | images reaching Assets<Image> per census window | yes | yes |
| `world_events.csv` | room loads and session starts/ends, with game time | yes | yes |
| `schedule_census.csv` | registered system counts per sample | yes | yes |
| `schedule_phases.csv` | per-frame milliseconds in each main-schedule phase | yes | yes |
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | yes | yes |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | yes | yes |
| `tracy.trace` | the raw Tracy trace, for the GUI | yes | no |
| `alloc_by_span.csv` | perf_probe: allocations per Bevy system / tracing span | no | no |
| `churn_by_site.csv` | perf_probe: spawns, despawns, inserts, removes per source line | no | no |
| `changes_by_site.csv` | perf_probe: component and resource values marked changed, per source line | no | no |
| `perf_windows/` | one flat perf report per time slice | yes | yes |
| `perf_report.txt` | whole-run flat perf report | yes | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes | yes |
| `perf.data` | the raw perf capture | yes | no |

