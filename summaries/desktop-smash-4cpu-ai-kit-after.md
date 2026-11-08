# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `78b613603665` on `main` |
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

Observed span of the game's own log: **135.8s**.

## Frame time

90 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.1      42    16.2     7.0    63.6   173.4    173.4
      3.1      63    16.0    13.3    23.2    50.5    145.9
     57.5      56    18.3    17.0    24.4    28.1     45.8
     58.5      66    14.8    14.3    22.8    33.9     37.6
     80.6      60    16.7    16.5    20.1    21.1     37.6
     77.6      43    23.3    21.9    32.5    36.5     36.5
     51.4      63    15.8    15.9    21.5    27.6     34.3
     56.4      63    16.1    16.1    19.3    20.9     30.2
```

Full series: `frame_times.csv`.

12 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    4.068s     173.4 ms
    4.388s     145.9 ms
    3.668s     129.5 ms
    3.538s      61.9 ms
    4.458s      50.5 ms
   59.524s      43.3 ms
   59.598s      37.7 ms
   81.928s      37.6 ms
   79.475s      36.5 ms
   59.560s      36.3 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1033 images / 498.1 MP in total; 171 were notable enough to print.

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
      1.1        4       3      1       0      1
      5.1        8       3      1       0      1
      9.1        8       3      1       0      1
     13.2        8       3      1       0      1
     17.2       10       3      1       0      1
     21.2       12       3      1       0      1
     25.2       12       5      1       2      1
     29.3       12       3      1       0      1
     33.3       12       7      1       4      1
     37.3       10       5      1       2      1
     41.3       12       6      1       3      1
     45.4       12       5      1       2      1
     49.4       12       7      1       4      1
     53.4       12       5      1       2      1
     57.5       12       5      1       2      1
     61.5        8       3      1       0      1
     65.5       12       7      1       4      1
     69.5        8       3      1       0      1
     73.6        8       3      1       0      1
     77.6       10       3      1       0      1
     81.7       10       3      1       0      1
     85.7       10       3      1       0      1
     89.7       10       3      1       0      1
```

Peak world-rendering cameras: **1** at t=1.1s.

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

Peak active portal capture rigs: **0** of 0 at t=1.1s.

```text
        t  rigs  active  budget
      1.1     0       0  captures<=2
      8.1     0       0  captures<=2
     15.2     0       0  captures<=2
     22.2     0       0  captures<=2
     29.3     0       0  captures<=2
     36.3     0       0  captures<=2
     43.4     0       0  captures<=2
     50.4     0       0  captures<=2
     57.5     0       0  captures<=2
     64.5     0       0  captures<=2
     71.6     0       0  captures<=2
     78.6     0       0  captures<=2
     85.7     0       0  captures<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **8** (largest dimension 1152px) at t=18.2s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1693        0        0
       end       4096        2113        0        0
      peak       4096        2113        4        0
```

Entity count rose by 2048 and then held flat at 4096 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **424** (368 visible), text2d 20, per-view projections 9 at t=57.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3870** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.046          0.180       90  render/ui/elapsed_gpu
         0.045          0.085       90  render/msaa_writeback/elapsed_gpu
         0.026          0.075       90  render/ui/elapsed_cpu
         0.021          0.040       90  render/upscaling/elapsed_gpu
         0.021          0.050       88  render/main_opaque_pass_2d/elapsed_cpu
         0.013          0.034       88  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.005       90  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.023       90  render/msaa_writeback/elapsed_cpu
         0.002          0.006       90  render/upscaling/elapsed_cpu
         0.002          0.056       90  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    701781.648    1327104.000       88  render/main_opaque_pass_2d/fragment_shader_invocations
    694126.767   11916928.000       90  render/ui/fragment_shader_invocations
       532.756       2446.000       90  render/ui/vertex_shader_invocations
       265.378       1222.000       90  render/ui/clipper_primitives_out
       265.378       1222.000       90  render/ui/clipper_invocations
       191.622       8681.000       90  render/main_transparent_pass_2d/fragment_shader_invocations
        18.250         70.000       88  render/main_opaque_pass_2d/vertex_shader_invocations
        15.386         64.000       88  render/main_opaque_pass_2d/clipper_invocations
        15.386         64.000       88  render/main_opaque_pass_2d/clipper_primitives_out
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
   91898.2         1 91898173.5 91898173.5   100%  bevy_app
   91831.1         1 91831084.5 91831084.5   100%  render thread
   85317.5      6173   13821.1  585664.3     1%  update
   78717.7      6173   12751.9  547681.5     1%  main app
   78684.2      6173   12746.5  547675.5     1%  schedule{name=Main}
   32324.9      6173    5236.5  131218.9     0%  schedule{name=PreUpdate}
   30722.6      6173    4976.9  141753.1     0%  sub app{name=RenderApp}
   30670.1      6173    4968.4  141745.0     0%  schedule{name=RenderRecovery}
   30446.9      6173    4932.3  141675.6     0%  system{name="bevy_render::run_render_schedule"}
   30279.6      6173    4905.2  141643.2     0%  schedule{name=Render}
   24680.6      6173    3998.2  128562.1     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   24346.4      4660    5224.6  128240.8     1%  ggrs{name="HandleRequests"}
   24278.7      4660    5210.0  128224.7     1%  schedule{name="AdvanceWorld"}
   24229.6      4660    5199.5  128176.3     1%  schedule{name=AdvanceWorld}
   24020.8      4660    5154.7  127676.7     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   23982.7      4660    5146.5  127663.7     1%  schedule{name=GgrsSchedule}
   23712.6      6173    3841.3  304707.1     1%  schedule{name=Update}
   13368.2      6173    2165.6   20143.5     0%  schedule{name=PostUpdate}
   12556.9      6173    2034.2   26118.4     0%  system{name="bevy_render::renderer::render_system"}
   12538.9      6173    2031.2   26116.2     0%  main_render_schedule
   11522.5      6173    1866.6    8841.7     0%  schedule{name=RenderGraph}
    8048.9      6173    1303.9    5507.2     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    7584.2     23797     318.7    3757.2     0%  schedule{name=Core2d}
    6534.5      6173    1058.6  118125.3     2%  sub app{name=RenderExtractApp}
    5886.0      6173     953.5   31754.5     1%  schedule{name=ExtractSchedule}
    5321.7   3114404       1.7    1382.7     0%  multithreaded executor
    4722.0      4660    1013.3    6743.4     0%  system{name="ambition_platformer2d_actor_monolith::body::actors::update::tick_actor_brains
    3614.1      6173     585.5    4561.4     0%  schedule{name=RunFixedMainLoop}
    3136.3      6173     508.1    4405.7     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    3068.2      5841     525.3    2764.0     0%  schedule{name=FixedMain}
    2723.9      5841     466.3    2587.0     0%  schedule{name=FixedPostUpdate}
    2587.7      6173     419.2    8080.2     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2430.0      6172     393.7    3471.4     0%  camera_schedule{camera="Camera 0 (500v0)"}
    2208.6      6173     357.8    7317.2     0%  schedule{name=Last}
    1977.5      6172     320.4    3765.0     0%  camera_schedule{camera="Camera 9 (501v0)"}
    1706.6      5841     292.2    1977.2     0%  system{name="avian2d::schedule::run_physics_schedule"}
    1660.6      1922     864.0    1947.2     0%  schedule{name=PhysicsSchedule}
    1633.8     23797      68.7     841.7     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    1616.0      6173     261.8     750.1     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
    1569.8      6172     254.3    3113.2     0%  camera_schedule{camera="Camera 7 (502v0)"}
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
   84731.9      13728.4      6173    5596.6  update
   78170.0      12665.3      6173    4860.7  main app
   78136.5      12659.8      6173    4857.7  schedule{name=Main}
   32193.7       5216.1      6173     700.2  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw `tracy.trace`: local capture directory only.

## Which phase of the frame owned the time

Mean milliseconds per frame over 6126 frames, summing to 14.80ms:

```text
    5.29 ms   35.7%  PreUpdate
    3.90 ms   26.3%  Update
    2.19 ms   14.8%  PostUpdate
    2.08 ms   14.0%  outside
    0.60 ms    4.1%  RunFixedMainLoop
    0.38 ms    2.6%  Last
    0.20 ms    1.3%  First
    0.09 ms    0.6%  StateTransition
    0.08 ms    0.5%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    5.29    15.03     2.84   PreUpdate
    3.90     6.84     1.76   Update
    2.19     4.11     1.87   PostUpdate
    2.08     5.28     2.54   outside
    0.60     1.23     2.04   RunFixedMainLoop
    0.38     0.59     1.57   Last
    0.20     0.47     2.37   First
    0.09     0.18     1.99   StateTransition
    0.08     0.12     1.51   SpawnScene
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
  66.3%  the game itself
  33.2%  profiler (Tracy)
   0.4%  audio
```

```text
profiler (Tracy) overhead : 33.2%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           : 66.3%
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
  78.6%  game binary + its Rust/C deps
  18.9%  kernel
   2.4%  GPU driver / graphics stack
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
    33.58%     0.00%  smash_match_pro  [unknown]                              [.] 0xffffffffffffffff                                                                                                    
    23.63%     0.12%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Worker()                                                                                             
    23.18%     0.00%  Tracy Profiler   libc.so.6                              [.] start_thread                                                                                                          
    23.18%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Thread::Launch(void*)                                                                                          
    23.14%     0.00%  Tracy Profiler   libc.so.6                              [.] clone3                                                                                                                
    23.14%     0.02%  smash_match_pro  smash_match_profile                    [.] _RNvMs1_NtNtCs6STjheAsjvA_8bevy_ecs8schedule8scheduleNtB5_8Schedule3run                                               
    22.22%     1.60%  smash_match_pro  smash_match_profile                    [.] _RNvXNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecuto
    14.85%     0.00%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World18try_schedule_scopeuINtNtB8_6intern8InternedDNtNtNtB8_8schedule3se
    11.66%     4.37%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                           
     9.97%     0.06%  Compute Task Po  smash_match_profile                    [.] _RINvMs2_NtNtCs8ZKwes1zzDd_3std6thread5localINtB6_8LocalKeyINtNtCskADBYZwJmSU_4core4cell7RefCellTNtCsbsL9vopLbSS_7park
     9.86%     0.19%  Compute Task Po  smash_match_profile                    [.] _RNvXs5_NtCscJLDsxKi8GJ_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNtB19_
     9.25%     0.02%  smash_match_pro  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace29run_without_applying_deferred     
     9.05%     0.00%  Compute Task Po  smash_match_profile                    [.] _RINvNtNtCs8ZKwes1zzDd_3std3sys9backtrace28___rust_begin_short_backtraceNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_po
     8.78%     0.06%  Tracy Profiler   smash_match_profile                    [.] tracy::Socket::HasData()                                                                                              
     8.17%     0.08%  Compute Task Po  smash_match_profile                    [.] _RNvMs1_NtCs9Rcz90M69zT_10async_task3rawINtB5_7RawTaskINtCsjFFWrCkEdj5_14async_executor15AsyncCallOnDropINtNtCscJLDsxK
     8.11%     0.78%  Tracy Profiler   libc.so.6                              [.] __poll                                                                                                                
     8.05%     0.05%  Compute Task Po  smash_match_profile                    [.] _RNvXso_NtNtCskADBYZwJmSU_4core5panic11unwind_safeINtB5_16AssertUnwindSafeNCNvMs3_NtNtNtCs6STjheAsjvA_8bevy_ecs8schedu
     7.77%     0.11%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSCALL_64_after_hwframe                                                                                        
     7.39%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNSNvYNCINvNtNtCs8ZKwes1zzDd_3std6thread9lifecycle15spawn_uncheckedNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNt
     7.18%     0.00%  Compute Task Po  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
     6.79%     0.32%  Tracy Profiler   [kernel.kallsyms]                      [k] do_syscall_64                                                                                                         
     6.49%     0.24%  Compute Task Po  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                        
     6.49%     0.40%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(
     6.01%     0.10%  Tracy Profiler   [kernel.kallsyms]                      [k] x64_sys_call                                                                                                          
     5.92%     0.00%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World14schedule_scopeuNtCsaMjW6A3uaLQ_9bevy_ggrs12GgrsScheduleNCINvB2_12
     5.65%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNvNvMs0_NtNtNtCs8ZKwes1zzDd_3std3sys6thread4unixNtB7_6Thread3new12thread_start                                      
     5.18%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::SendData(char const*, unsigned long)                                                                 
     5.18%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::CommitData()                                                                                         
     4.77%     0.00%  Compute Task Po  libc.so.6                              [.] start_thread                                                                                                          
     4.73%     0.21%  Tracy Profiler   [kernel.kallsyms]                      [k] __x64_sys_poll                                                                                                        
     4.42%     2.12%  smash_match_pro  smash_match_profile                    [.] _RNvXs1_CsauKbtVXGAam_13tracing_tracyNtB5_10TracyLayerINtNtCshdbjT2rFsCC_18tracing_subscriber5layer5LayerINtNtBS_7laye
     4.38%     0.00%  Tracy Symbol Wo  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
     4.34%     4.14%  Tracy Profiler   smash_match_profile                    [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                            
     4.28%     1.49%  Tracy Profiler   [kernel.kallsyms]                      [k] do_sys_poll                                                                                                           
     4.12%     0.00%  Compute Task Po  libc.so.6                              [.] clone3                                                                                                                
```

## Assets and render resources

- Decoded images: 139 → 1033 (498.1 MP, 1992.4 MB of decode work).
- Images resident at end: 867.
- **Resident image BYTES at end: 1992.4 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **810 images (365.6 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 58 GENERATED image(s) were allocated during gameplay (75.4 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 87 notable decode(s) (220.6 MP) landed AFTER the first `room-loaded` (frame 49) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   2.6MP  f     52  content://ambition_shared/__art/bundles/f45d089756d7bd5c1063e7c81561eacc9e9e82dcf374887bff8ea241526326a3/runtime/full/sprites/catacombs_west_spritesheet.png
   2.5MP  f     52  content://ambition_shared/__art/bundles/e071a6f3a91ff0a75b02a342ab5f723ac4ba596bd0d45e20671996be4dd22efd/runtime/full/sprites/port_gantry_spritesheet.png
   1.3MP  f     52  content://ambition_shared/__art/bundles/7b2e18c59fd353969aa1ef3a7dab32478b27a46fb01246109215292bfac1350c/runtime/full/sprites/castle_cells_room_spritesheet.png
   1.4MP  f     52  content://ambition_shared/__art/bundles/7a59d69edda556320ba6919d1b2bbf378d920d03529cc5facde52c5807138f87/runtime/full/sprites/port_harbour_house_spritesheet.png
   1.7MP  f     52  content://ambition_shared/__art/bundles/cae0fd7bc351b2c87cd37b630551ea98a1d6f9b3e01db40e58db4a7a57bcf11c/runtime/full/sprites/inn_room_spritesheet.png
   3.5MP  f     52  content://ambition_shared/__art/bundles/9c264330e3e9c5343c522c0f0240f84b5efe60fc3c2fa0a5eaa62e200d6530ae/runtime/full/sprites/cellars_vault_spritesheet.png
   1.2MP  f     52  content://ambition_shared/__art/bundles/49d4b5422eedc5d3c904e775294e57384f026d16c0977d4c17e5df33f3812ae5/runtime/full/sprites/port_tavern_front_spritesheet.png
   1.7MP  f     52  content://ambition_shared/__art/bundles/5bbbec296222fe797dcca2fb9b56cf7682229d9cb7cac1403c599e1574151f93/runtime/full/sprites/castle_guild_room_spritesheet.png
```

⚠ 87 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  59x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 113915244 bytes

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

