# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `aa3a5e535dea` on `main` |
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

Observed span of the game's own log: **132.7s**.

## Frame time

90 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.2      42    15.7     7.3    61.4   170.9    170.9
      3.2      63    16.1    13.4    24.4    47.3    108.3
     20.3      41    24.6    20.9    51.5    57.8     57.8
     16.3      32    32.0    30.0    43.7    53.8     53.8
     17.3      48    20.4    18.4    34.6    47.7     47.7
     52.6      43    23.1    21.3    44.1    46.0     46.0
     22.3      42    23.9    21.1    37.1    45.2     45.2
     21.3      34    30.0    30.4    38.3    43.2     43.2
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    4.208s     170.9 ms
    3.828s     130.7 ms
    4.477s     108.2 ms
    3.697s      60.2 ms
   22.273s      57.7 ms
   22.187s      54.0 ms
   17.983s      53.8 ms
   22.352s      51.5 ms
   18.509s      47.8 ms
    4.549s      47.3 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 998 images / 466.8 MP in total; 151 were notable enough to print.

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
      1.2        4       3      1       0      1
      5.2        8       3      1       0      1
      9.2        8       3      1       0      1
     13.2       10       3      1       0      1
     17.3       10       5      1       2      1
     21.3       12       3      1       0      1
     25.4       12       5      1       2      1
     29.4       12       5      1       2      1
     33.5       12       5      1       2      1
     37.5       10       3      1       0      1
     41.5       12       7      1       4      1
     45.6       12       7      1       4      1
     49.6       12       5      1       2      1
     53.6       12       5      1       2      1
     57.7       12       5      1       2      1
     61.7        8       3      1       0      1
     65.7       12       3      1       0      1
     69.8        8       3      1       0      1
     73.8       12       3      1       0      1
     77.8       10       3      1       0      1
     81.9       10       3      1       0      1
     85.9       10       3      1       0      1
     90.0       10       3      1       0      1
```

Peak world-rendering cameras: **1** at t=1.2s.

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

Peak active portal capture rigs: **0** of 0 at t=1.2s.

```text
        t  rigs  active  budget
      1.2     0       0  captures<=2
      8.2     0       0  captures<=2
     15.3     0       0  captures<=2
     22.3     0       0  captures<=2
     29.4     0       0  captures<=2
     36.5     0       0  captures<=2
     43.5     0       0  captures<=2
     50.6     0       0  captures<=2
     57.7     0       0  captures<=2
     64.7     0       0  captures<=2
     71.8     0       0  captures<=2
     78.9     0       0  captures<=2
     85.9     0       0  captures<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **8** (largest dimension 1152px) at t=18.3s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1693        0        0
       end       4096        2106        0        0
      peak       4096        2106        4        0
```

Entity count rose by 2048 and then held flat at 4096 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **429** (320 visible), text2d 20, per-view projections 9 at t=57.7s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3870** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.049          0.254       90  render/ui/elapsed_gpu
         0.044          0.100       90  render/msaa_writeback/elapsed_gpu
         0.028          0.086       90  render/ui/elapsed_cpu
         0.023          0.050       88  render/main_opaque_pass_2d/elapsed_cpu
         0.021          0.048       90  render/upscaling/elapsed_gpu
         0.012          0.030       88  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.006       90  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.013       90  render/msaa_writeback/elapsed_cpu
         0.002          0.012       90  render/upscaling/elapsed_cpu
         0.001          0.013       90  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    694223.100   11916838.000       90  render/ui/fragment_shader_invocations
    675423.000    1327104.000       88  render/main_opaque_pass_2d/fragment_shader_invocations
       532.533       2438.000       90  render/ui/vertex_shader_invocations
       265.267       1218.000       90  render/ui/clipper_primitives_out
       265.267       1218.000       90  render/ui/clipper_invocations
       192.678       8677.000       90  render/main_transparent_pass_2d/fragment_shader_invocations
        19.000         70.000       88  render/main_opaque_pass_2d/vertex_shader_invocations
        16.091         64.000       88  render/main_opaque_pass_2d/clipper_invocations
        16.091         64.000       88  render/main_opaque_pass_2d/clipper_primitives_out
         0.711         32.000       90  render/main_transparent_pass_2d/vertex_shader_invocations
         0.667         30.000       90  render/main_transparent_pass_2d/clipper_invocations
         0.667         30.000       90  render/main_transparent_pass_2d/clipper_primitives_out
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
   91966.6         1 91966585.7 91966585.7   100%  bevy_app
   91899.0         1 91898963.5 91898963.5   100%  render thread
   81480.7      5167   15769.4  612593.6     1%  update
   75428.9      5167   14598.2  575740.2     1%  main app
   75396.4      5167   14591.9  575734.3     1%  schedule{name=Main}
   32833.5      5167    6354.5   85676.6     0%  schedule{name=PreUpdate}
   28245.3      5167    5466.5  140650.5     0%  sub app{name=RenderApp}
   28196.3      5167    5457.0  140643.6     0%  schedule{name=RenderRecovery}
   27985.5      5167    5416.2  140571.5     1%  system{name="bevy_render::run_render_schedule"}
   27834.4      5167    5387.0  140536.4     1%  schedule{name=Render}
   25851.5      5167    5003.2   84335.0     0%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   25509.6      4661    5473.0   84189.3     0%  ggrs{name="HandleRequests"}
   25440.5      4661    5458.2   84174.3     0%  schedule{name="AdvanceWorld"}
   25386.5      4661    5446.6   84141.7     0%  schedule{name=AdvanceWorld}
   25169.2      4661    5400.0   84004.1     0%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   25128.0      4661    5391.1   83997.9     0%  schedule{name=GgrsSchedule}
   21274.5      5167    4117.4  324393.6     2%  schedule{name=Update}
   12142.8      5167    2350.1   21878.0     0%  schedule{name=PostUpdate}
   11914.3      5167    2305.8   28996.0     0%  system{name="bevy_render::renderer::render_system"}
   11897.4      5167    2302.6   28992.5     0%  main_render_schedule
   10826.5      5167    2095.3   15293.1     0%  schedule{name=RenderGraph}
    7581.0      5167    1467.2    9078.4     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    7155.8     20845     343.3    6029.8     0%  schedule{name=Core2d}
    5990.8      5167    1159.4  118923.3     2%  sub app{name=RenderExtractApp}
    5374.4      5167    1040.1   55862.4     1%  schedule{name=ExtractSchedule}
    4958.9      4661    1063.9   10804.5     0%  system{name="ambition_platformer2d_actor_monolith::body::actors::update::tick_actor_brains
    4800.6   2619227       1.8    1860.8     0%  multithreaded executor
    3733.3      5167     722.5    6636.8     0%  schedule{name=RunFixedMainLoop}
    3292.7      5167     637.3    6482.8     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    3228.4      5839     552.9    3432.9     0%  schedule{name=FixedMain}
    2866.9      5839     491.0    3349.8     0%  schedule{name=FixedPostUpdate}
    2421.7      5167     468.7    3799.9     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2130.0      5166     412.3    3822.5     0%  camera_schedule{camera="Camera 0 (500v0)"}
    2096.2      5167     405.7    7428.4     0%  schedule{name=Last}
    1813.1      5839     310.5    2909.4     0%  system{name="avian2d::schedule::run_physics_schedule"}
    1810.3      5166     350.4    6041.3     0%  camera_schedule{camera="Camera 9 (501v0)"}
    1767.7      1921     920.2    2893.4     0%  schedule{name=PhysicsSchedule}
    1511.6     20845      72.5     722.2     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    1430.8      5167     276.9     902.5     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
    1398.2      5166     270.7    4003.2     0%  camera_schedule{camera="Camera 7 (502v0)"}
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
   80868.1      15653.9      5167    5677.6  update
   74853.2      14489.6      5167    4691.3  main app
   74820.7      14483.3      5167    4688.3  schedule{name=Main}
   32747.9       6339.1      5167     727.6  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw `tracy.trace`: local capture directory only.

## Which phase of the frame owned the time

Mean milliseconds per frame over 5130 frames, summing to 17.70ms:

```text
    6.41 ms   36.2%  PreUpdate
    4.18 ms   23.6%  Update
    3.15 ms   17.8%  outside
    2.38 ms   13.5%  PostUpdate
    0.74 ms    4.2%  RunFixedMainLoop
    0.43 ms    2.4%  Last
    0.23 ms    1.3%  First
    0.10 ms    0.6%  StateTransition
    0.08 ms    0.5%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    6.41    16.14     2.52   PreUpdate
    4.18     7.01     1.68   Update
    3.15     7.76     2.47   outside
    2.38     4.49     1.89   PostUpdate
    0.74     1.41     1.91   RunFixedMainLoop
    0.43     0.70     1.62   Last
    0.23     0.63     2.69   First
    0.10     0.18     1.79   StateTransition
    0.08     0.13     1.52   SpawnScene
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
  66.2%  the game itself
  33.4%  profiler (Tracy)
   0.5%  audio
```

```text
profiler (Tracy) overhead : 33.4%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           : 66.2%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 33% of sampled cycles.** Its symbol-resolution and
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
  78.2%  game binary + its Rust/C deps
  19.1%  kernel
   2.6%  GPU driver / graphics stack
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
    32.42%     0.00%  smash_match_pro  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
    23.49%     0.09%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Worker()                                                                                             
    23.44%     0.01%  smash_match_pro  smash_match_profile                    [.] _RNvMs1_NtNtCs6STjheAsjvA_8bevy_ecs8schedule8scheduleNtB5_8Schedule3run                                               
    23.05%     0.00%  Tracy Profiler   libc.so.6                              [.] start_thread                                                                                                          
    23.05%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Thread::Launch(void*)                                                                                          
    23.00%     0.00%  Tracy Profiler   libc.so.6                              [.] clone3                                                                                                                
    22.70%     1.70%  smash_match_pro  smash_match_profile                    [.] _RNvXNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecuto
    14.44%     0.00%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World18try_schedule_scopeuINtNtB8_6intern8InternedDNtNtNtB8_8schedule3se
    12.17%     4.54%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                           
     9.28%     0.02%  smash_match_pro  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace29run_without_applying_deferred     
     9.08%     0.04%  Compute Task Po  smash_match_profile                    [.] _RINvMs2_NtNtCs8ZKwes1zzDd_3std6thread5localINtB6_8LocalKeyINtNtCskADBYZwJmSU_4core4cell7RefCellTNtCsbsL9vopLbSS_7park
     9.01%     0.20%  Compute Task Po  smash_match_profile                    [.] _RNvXs5_NtCscJLDsxKi8GJ_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNtB19_
     8.43%     0.06%  Tracy Profiler   smash_match_profile                    [.] tracy::Socket::HasData()                                                                                              
     8.24%     0.00%  Compute Task Po  smash_match_profile                    [.] _RINvNtNtCs8ZKwes1zzDd_3std3sys9backtrace28___rust_begin_short_backtraceNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_po
     7.71%     0.76%  Tracy Profiler   libc.so.6                              [.] __poll                                                                                                                
     7.52%     0.08%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSCALL_64_after_hwframe                                                                                        
     7.41%     0.07%  Compute Task Po  smash_match_profile                    [.] _RNvMs1_NtCs9Rcz90M69zT_10async_task3rawINtB5_7RawTaskINtCsjFFWrCkEdj5_14async_executor15AsyncCallOnDropINtNtCscJLDsxK
     7.32%     0.02%  Compute Task Po  smash_match_profile                    [.] _RNvXso_NtNtCskADBYZwJmSU_4core5panic11unwind_safeINtB5_16AssertUnwindSafeNCNvMs3_NtNtNtCs6STjheAsjvA_8bevy_ecs8schedu
     6.75%     0.53%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(
     6.72%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNSNvYNCINvNtNtCs8ZKwes1zzDd_3std6thread9lifecycle15spawn_uncheckedNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNt
     6.72%     0.00%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World14schedule_scopeuNtCsaMjW6A3uaLQ_9bevy_ggrs12GgrsScheduleNCINvB2_12
     6.65%     0.32%  Tracy Profiler   [kernel.kallsyms]                      [k] do_syscall_64                                                                                                         
     6.40%     0.00%  Compute Task Po  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
     6.02%     0.19%  Compute Task Po  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                        
     5.92%     0.07%  Tracy Profiler   [kernel.kallsyms]                      [k] x64_sys_call                                                                                                          
     5.28%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNvNvMs0_NtNtNtCs8ZKwes1zzDd_3std3sys6thread4unixNtB7_6Thread3new12thread_start                                      
     5.13%     0.01%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::SendData(char const*, unsigned long)                                                                 
     5.13%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::CommitData()                                                                                         
     4.58%     0.00%  Tracy Symbol Wo  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
     4.57%     0.17%  Tracy Profiler   [kernel.kallsyms]                      [k] __x64_sys_poll                                                                                                        
     4.53%     2.07%  smash_match_pro  smash_match_profile                    [.] _RNvXs1_CsauKbtVXGAam_13tracing_tracyNtB5_10TracyLayerINtNtCshdbjT2rFsCC_18tracing_subscriber5layer5LayerINtNtBS_7laye
     4.34%     0.00%  Compute Task Po  libc.so.6                              [.] start_thread                                                                                                          
     4.26%     4.04%  Tracy Profiler   smash_match_profile                    [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                            
     4.23%     1.47%  Tracy Profiler   [kernel.kallsyms]                      [k] do_sys_poll                                                                                                           
     3.71%     0.00%  Compute Task Po  libc.so.6                              [.] clone3                                                                                                                
```

## Assets and render resources

- Decoded images: 139 → 998 (466.8 MP, 1867.2 MB of decode work).
- Images resident at end: 864.
- **Resident image BYTES at end: 1867.2 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **810 images (365.6 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 38 GENERATED image(s) were allocated during gameplay (49.4 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 86 notable decode(s) (218.1 MP) landed AFTER the first `room-loaded` (frame 49) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   2.6MP  f     52  content://ambition_shared/__art/bundles/f45d089756d7bd5c1063e7c81561eacc9e9e82dcf374887bff8ea241526326a3/runtime/full/sprites/catacombs_west_spritesheet.png
   1.3MP  f     52  content://ambition_shared/__art/bundles/7b2e18c59fd353969aa1ef3a7dab32478b27a46fb01246109215292bfac1350c/runtime/full/sprites/castle_cells_room_spritesheet.png
   3.5MP  f     52  content://ambition_shared/__art/bundles/0b4df2a8b443e521a3d4648f4a2d9890c7f44828481ede3634d365e923a0f4d8/runtime/full/sprites/fossil_beds_spritesheet.png
   1.2MP  f     52  content://ambition_shared/__art/bundles/7eb5c97958d005303fdf75b0eba57b6560866a1f21a7acf5bdc69a275e5ce855/runtime/full/sprites/gallery_exhibits_spritesheet.png
   3.5MP  f     52  content://ambition_shared/__art/bundles/9c264330e3e9c5343c522c0f0240f84b5efe60fc3c2fa0a5eaa62e200d6530ae/runtime/full/sprites/cellars_vault_spritesheet.png
   1.7MP  f     53  content://ambition_shared/__art/bundles/5bbbec296222fe797dcca2fb9b56cf7682229d9cb7cac1403c599e1574151f93/runtime/full/sprites/castle_guild_room_spritesheet.png
   1.7MP  f     53  content://ambition_shared/__art/bundles/31785e100ba3f217170a0e9136131ac95e98bbfe1fe48e76a5c3694a4b6726ca/runtime/full/sprites/interdimensional_gate_portal_spritesheet.png
   2.3MP  f     53  content://ambition_shared/__art/bundles/57be1a6ff4a899edcbd1b493ac864347541ed0308bfbb3925d1ad6634794c6d3/runtime/full/sprites/castle_kitchens_room_spritesheet.png
```

⚠ 86 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  39x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 106710952 bytes

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

