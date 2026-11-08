# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `7b00d6273b9f` on `main` |
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

Observed span of the game's own log: **150.3s**.

## Frame time

90 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.3      34    19.2     7.8    65.5   180.6    180.6
      3.4      58    17.7    16.6    27.1    38.2    106.9
     15.4      47    21.9    21.1    33.8    41.0     41.0
     34.6      52    19.2    18.3    27.0    29.3     36.1
     32.6      58    17.4    16.2    23.7    25.2     35.9
     81.0      93    10.8     9.9    14.1    18.5     35.4
     22.5      58    17.2    17.1    25.3    32.0     33.8
     24.5      48    21.3    20.0    28.6    33.5     33.5
```

Full series: `frame_times.csv`.

13 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    5.510s     180.6 ms
    5.114s     161.0 ms
    5.831s     106.9 ms
    4.953s      68.4 ms
   18.651s      39.6 ms
    5.887s      38.1 ms
   18.161s      37.6 ms
   36.971s      36.1 ms
   35.147s      35.9 ms
   83.401s      35.4 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1002 images / 470.1 MP in total; 153 were notable enough to print.

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
      1.3        4       3      1       0      1
      5.4        8       5      1       2      1
      9.4        8       3      1       0      1
     13.4        8       3      1       0      1
     17.4       10       3      1       0      1
     21.5       12       5      1       2      1
     25.5       12       3      1       0      1
     29.5       12       5      1       2      1
     33.6       12       3      1       0      1
     37.6       10       3      1       0      1
     41.7       12       5      1       2      1
     45.7       12       5      1       2      1
     49.7       12       5      1       2      1
     53.8       12       5      1       2      1
     57.8       12       7      1       4      1
     61.8       12       5      1       2      1
     65.9       12       5      1       2      1
     69.9        8       3      1       0      1
     73.9       10       3      1       0      1
     78.0       10       3      1       0      1
     82.0       10       3      1       0      1
     86.0       10       3      1       0      1
     90.0       10       3      1       0      1
```

Peak world-rendering cameras: **1** at t=1.3s.

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

Peak active portal capture rigs: **0** of 0 at t=1.3s.

```text
        t  rigs  active  budget
      1.3     0       0  captures<=2
      8.4     0       0  captures<=2
     15.4     0       0  captures<=2
     22.5     0       0  captures<=2
     29.5     0       0  captures<=2
     36.6     0       0  captures<=2
     43.7     0       0  captures<=2
     50.7     0       0  captures<=2
     57.8     0       0  captures<=2
     64.9     0       0  captures<=2
     71.9     0       0  captures<=2
     79.0     0       0  captures<=2
     86.0     0       0  captures<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **8** (largest dimension 1152px) at t=18.4s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1694        0        0
       end       4096        2099        0        0
      peak       4096        2099        4        0
```

Entity count rose by 2048 and then held flat at 4096 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **474** (440 visible), text2d 20, per-view projections 9 at t=57.8s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3870** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.050          0.179       90  render/ui/elapsed_gpu
         0.043          0.077       90  render/msaa_writeback/elapsed_gpu
         0.030          0.088       90  render/ui/elapsed_cpu
         0.022          0.054       88  render/main_opaque_pass_2d/elapsed_cpu
         0.021          0.049       90  render/upscaling/elapsed_gpu
         0.012          0.037       88  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.005       90  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.036       90  render/msaa_writeback/elapsed_cpu
         0.003          0.009       90  render/upscaling/elapsed_cpu
         0.001          0.008       90  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    701782.477    1327104.000       88  render/main_opaque_pass_2d/fragment_shader_invocations
    678351.911   10476946.000       90  render/ui/fragment_shader_invocations
       533.911       2454.000       90  render/ui/vertex_shader_invocations
       265.956       1226.000       90  render/ui/clipper_primitives_out
       265.956       1226.000       90  render/ui/clipper_invocations
        94.922       8543.000       90  render/main_transparent_pass_2d/fragment_shader_invocations
        18.250         70.000       88  render/main_opaque_pass_2d/vertex_shader_invocations
        15.386         64.000       88  render/main_opaque_pass_2d/clipper_invocations
        15.386         64.000       88  render/main_opaque_pass_2d/clipper_primitives_out
         0.356         32.000       90  render/main_transparent_pass_2d/vertex_shader_invocations
         0.333         30.000       90  render/main_transparent_pass_2d/clipper_invocations
         0.333         30.000       90  render/main_transparent_pass_2d/clipper_primitives_out
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
   92089.1         1 92089141.0 92089141.0   100%  bevy_app
   92025.0         1 92025018.1 92025018.1   100%  render thread
   91049.0      6127   14860.3  688149.7     1%  update
   84094.6      6127   13725.2  647717.8     1%  main app
   84061.6      6127   13719.9  647713.0     1%  schedule{name=Main}
   34491.9      6127    5629.5   95049.1     0%  schedule{name=PreUpdate}
   33256.4      6127    5427.9  173639.6     1%  sub app{name=RenderApp}
   33199.7      6127    5418.6  173634.9     1%  schedule{name=RenderRecovery}
   32951.0      6127    5378.0  173568.4     1%  system{name="bevy_render::run_render_schedule"}
   32772.0      6127    5348.8  173536.2     1%  schedule{name=Render}
   26281.7      6127    4289.5   93642.4     0%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   25920.3      4660    5562.3   93482.5     0%  ggrs{name="HandleRequests"}
   25851.3      4660    5547.5   93471.6     0%  schedule{name="AdvanceWorld"}
   25798.5      4660    5536.1   93419.7     0%  schedule{name=AdvanceWorld}
   25577.9      4660    5488.8   93312.9     0%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   25538.7      4660    5480.4   93306.6     0%  schedule{name=GgrsSchedule}
   25205.2      6127    4113.8  326703.7     1%  schedule{name=Update}
   14278.0      6127    2330.3   21760.4     0%  schedule{name=PostUpdate}
   13720.5      6127    2239.3    8890.7     0%  system{name="bevy_render::renderer::render_system"}
   13701.3      6127    2236.2    8888.5     0%  main_render_schedule
   12662.2      6127    2066.6    8709.0     0%  schedule{name=RenderGraph}
    8893.4      6127    1451.5    7807.2     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    8382.0     23797     352.2    5079.9     0%  schedule{name=Core2d}
    6884.8      6127    1123.7  150697.9     2%  sub app{name=RenderExtractApp}
    6207.0      6127    1013.1   49647.9     1%  schedule{name=ExtractSchedule}
    5687.2   3097286       1.8    2566.4     0%  multithreaded executor
    4998.7      4660    1072.7    8590.1     0%  system{name="ambition_platformer2d_actor_monolith::body::actors::update::tick_actor_brains
    3935.6      6127     642.3    4606.9     0%  schedule{name=RunFixedMainLoop}
    3359.3      6127     548.3    4512.8     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    3287.4      5844     562.5    3345.7     0%  schedule{name=FixedMain}
    2911.0      5844     498.1    3260.8     0%  schedule{name=FixedPostUpdate}
    2786.7      6127     454.8    3842.7     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2622.6      6126     428.1    5085.0     0%  camera_schedule{camera="Camera 0 (500v0)"}
    2355.5      6127     384.5    7532.0     0%  schedule{name=Last}
    2251.9      6126     367.6    4340.0     0%  camera_schedule{camera="Camera 9 (501v0)"}
    2190.7         1 2190723.3 2190723.3   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
    1830.0      5844     313.1    2722.4     0%  system{name="avian2d::schedule::run_physics_schedule"}
    1780.8      1925     925.1    2705.3     0%  schedule{name=PhysicsSchedule}
    1748.1     23797      73.5     516.2     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    1734.5      6127     283.1    1637.7     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
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
   90360.8      14750.4      6127    5925.2  update
   83446.9      13621.8      6127    5265.5  main app
   83413.8      13616.4      6127    5262.7  schedule{name=Main}
   34396.9       5614.9      6127     743.2  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw `tracy.trace`: local capture directory only.

## Which phase of the frame owned the time

Mean milliseconds per frame over 6054 frames, summing to 14.99ms:

```text
    5.70 ms   38.0%  PreUpdate
    4.17 ms   27.8%  Update
    2.36 ms   15.7%  PostUpdate
    1.29 ms    8.6%  outside
    0.66 ms    4.4%  RunFixedMainLoop
    0.41 ms    2.7%  Last
    0.21 ms    1.4%  First
    0.10 ms    0.7%  StateTransition
    0.08 ms    0.6%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    5.70    16.23     2.85   PreUpdate
    4.17     7.92     1.90   Update
    2.36     4.42     1.87   PostUpdate
    1.29     4.10     3.18   outside
    0.66     1.41     2.13   RunFixedMainLoop
    0.41     0.65     1.58   Last
    0.21     0.52     2.46   First
    0.10     0.24     2.33   StateTransition
    0.08     0.13     1.56   SpawnScene
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
  64.0%  the game itself
  35.6%  profiler (Tracy)
   0.4%  audio
```

```text
profiler (Tracy) overhead : 35.6%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           : 64.0%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 36% of sampled cycles.** Its symbol-resolution and
compression threads compete with the game for the same cores, so every frame
time, zone duration and plugin-build number here is inflated too.

Zone RATIOS remain usable — the instrumentation is uniform across systems.
Absolute per-frame costs are not. For an honest frame time, re-run:

```bash
scripts/measure/profile_desktop.sh --no-tracy
```

which drops `--features profile` (and with it the per-system zones), and
compare its frame census against this one to size the gap.

## Where the native time went

```text
  77.0%  game binary + its Rust/C deps
  20.8%  kernel
   2.1%  GPU driver / graphics stack
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
    32.66%     0.00%  smash_match_pro  [unknown]                              [.] 0xffffffffffffffff                                                                                                    
    26.76%     0.14%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Worker()                                                                                             
    26.34%     0.00%  Tracy Profiler   libc.so.6                              [.] start_thread                                                                                                          
    26.34%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Thread::Launch(void*)                                                                                          
    26.29%     0.00%  Tracy Profiler   libc.so.6                              [.] clone3                                                                                                                
    23.05%     0.02%  smash_match_pro  smash_match_profile                    [.] _RNvMs1_NtNtCs6STjheAsjvA_8bevy_ecs8schedule8scheduleNtB5_8Schedule3run                                               
    22.31%     1.63%  smash_match_pro  smash_match_profile                    [.] _RNvXNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecuto
    15.04%     0.00%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World18try_schedule_scopeuINtNtB8_6intern8InternedDNtNtNtB8_8schedule3se
    12.29%     5.41%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                           
    11.25%     0.11%  Tracy Profiler   smash_match_profile                    [.] tracy::Socket::HasData()                                                                                              
    10.38%     0.95%  Tracy Profiler   libc.so.6                              [.] __poll                                                                                                                
     9.56%     0.12%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSCALL_64_after_hwframe                                                                                        
     9.48%     0.04%  Compute Task Po  smash_match_profile                    [.] _RINvMs2_NtNtCs8ZKwes1zzDd_3std6thread5localINtB6_8LocalKeyINtNtCskADBYZwJmSU_4core4cell7RefCellTNtCsbsL9vopLbSS_7park
     9.40%     0.15%  Compute Task Po  smash_match_profile                    [.] _RNvXs5_NtCscJLDsxKi8GJ_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNtB19_
     9.26%     0.03%  smash_match_pro  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace29run_without_applying_deferred     
     8.62%     0.00%  Compute Task Po  smash_match_profile                    [.] _RINvNtNtCs8ZKwes1zzDd_3std3sys9backtrace28___rust_begin_short_backtraceNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_po
     8.44%     0.42%  Tracy Profiler   [kernel.kallsyms]                      [k] do_syscall_64                                                                                                         
     7.82%     0.04%  Compute Task Po  smash_match_profile                    [.] _RNvMs1_NtCs9Rcz90M69zT_10async_task3rawINtB5_7RawTaskINtCsjFFWrCkEdj5_14async_executor15AsyncCallOnDropINtNtCscJLDsxK
     7.67%     0.01%  Compute Task Po  smash_match_profile                    [.] _RNvXso_NtNtCskADBYZwJmSU_4core5panic11unwind_safeINtB5_16AssertUnwindSafeNCNvMs3_NtNtNtCs6STjheAsjvA_8bevy_ecs8schedu
     7.39%     0.12%  Tracy Profiler   [kernel.kallsyms]                      [k] x64_sys_call                                                                                                          
     7.05%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNSNvYNCINvNtNtCs8ZKwes1zzDd_3std6thread9lifecycle15spawn_uncheckedNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNt
     6.76%     0.00%  Compute Task Po  [unknown]                              [.] 0xffffffffffffffff                                                                                                    
     6.19%     0.22%  Compute Task Po  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                        
     6.19%     0.23%  Tracy Profiler   [kernel.kallsyms]                      [k] __x64_sys_poll                                                                                                        
     6.12%     0.40%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(
     5.87%     0.00%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World14schedule_scopeuNtCsaMjW6A3uaLQ_9bevy_ggrs12GgrsScheduleNCINvB2_12
     5.66%     1.86%  Tracy Profiler   [kernel.kallsyms]                      [k] do_sys_poll                                                                                                           
     5.48%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNvNvMs0_NtNtNtCs8ZKwes1zzDd_3std3sys6thread4unixNtB7_6Thread3new12thread_start                                      
     4.97%     0.01%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::CommitData()                                                                                         
     4.96%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::SendData(char const*, unsigned long)                                                                 
     4.48%     0.00%  Compute Task Po  libc.so.6                              [.] start_thread                                                                                                          
     4.36%     2.07%  smash_match_pro  smash_match_profile                    [.] _RNvXs1_CsauKbtVXGAam_13tracing_tracyNtB5_10TracyLayerINtNtCshdbjT2rFsCC_18tracing_subscriber5layer5LayerINtNtBS_7laye
     4.24%     4.07%  Tracy Profiler   smash_match_profile                    [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                            
     3.89%     0.00%  Compute Task Po  libc.so.6                              [.] clone3                                                                                                                
     3.76%     0.00%  Tracy Symbol Wo  [unknown]                              [.] 0xffffffffffffffff                                                                                                    
```

## Assets and render resources

- Decoded images: 139 → 1002 (470.1 MP, 1880.5 MB of decode work).
- Images resident at end: 864.
- **Resident image BYTES at end: 1880.5 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **810 images (365.6 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 40 GENERATED image(s) were allocated during gameplay (52.0 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 90 notable decode(s) (224.5 MP) landed AFTER the first `room-loaded` (frame 52) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.4MP  f     55  content://ambition_shared/__art/bundles/a88b44bfc129aed8facb53f1fb3b4398f538948e3d64cee7318841daf676c48f/runtime/full/sprites/amber_vault_room_spritesheet.png
   4.3MP  f     55  content://ambition_shared/__art/bundles/6bed931f4927c2368e0fdf5000c69e8fbd30af26645d258430396321611e1d18/runtime/full/sprites/plant_east_spritesheet.png
   4.3MP  f     57  content://ambition_shared/__art/bundles/bf2d1f373bc5675105ed62867291dae6888e002da288b7f540dbb77d88b8a0d8/runtime/full/sprites/plant_west_spritesheet.png
   4.5MP  f     57  content://ambition_shared/__art/bundles/167477b208f69439124209c24609036f5a6061d929977ba79e41415218a70f0c/runtime/full/sprites/terminus_hall_spritesheet.png
   1.4MP  f     58  content://ambition_shared/__art/bundles/d62b73e891247880a6344b9a51711468a6cd2a889877bbd586694cf339b695dc/runtime/full/sprites/bakery_room_spritesheet.png
   3.2MP  f     58  content://ambition_shared/__art/bundles/209ec031f774c0e3f012064b094e2d82ebc1a32f731c6009d4735bbd3858aee3/runtime/full/sprites/point_lighthouse_spritesheet.png
   2.6MP  f     59  content://ambition_shared/__art/bundles/70e42d70e190b3e4e0987176cec6bf77c5ab33b051b1c2c8d269a76956108a45/runtime/full/sprites/port_brig_spritesheet.png
   1.1MP  f     60  content://ambition_shared/__art/bundles/e9b2b9177a17424f4d9bbe814290d8f3555e4969d9345fdb84aab56d14186d12/runtime/full/sprites/hoard_room_spritesheet.png
```

⚠ 90 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  41x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 121620032 bytes

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

