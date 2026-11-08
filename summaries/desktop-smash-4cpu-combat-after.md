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

Observed span of the game's own log: **167.6s**.

## Frame time

90 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.2      38    17.1     7.5    62.9   180.9    180.9
      3.2      54    18.8    14.5    32.9    65.4    167.6
     80.0      61    17.1    16.6    21.9    22.2     50.6
     22.4      39    25.7    24.5    39.2    45.6     45.6
     61.8      44    23.4    22.3    34.5    43.1     43.1
     15.3      39    26.0    26.2    35.6    39.7     39.7
     41.6      38    26.6    26.7    37.4    39.6     39.6
     40.6      43    24.1    23.1    32.5    37.1     37.1
```

Full series: `frame_times.csv`.

33 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    4.214s     180.9 ms
    4.567s     167.6 ms
    3.813s     127.8 ms
    4.665s      65.4 ms
    3.685s      63.0 ms
    4.732s      50.2 ms
   82.102s      49.1 ms
   23.673s      45.5 ms
   23.599s      43.4 ms
   63.170s      43.1 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 988 images / 463.5 MP in total; 151 were notable enough to print.

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
      5.2        8       5      1       2      1
      9.2        8       3      1       0      1
     13.3        8       5      1       2      1
     17.3       10       5      1       2      1
     21.4       12       3      1       0      1
     25.4       12       7      1       4      1
     29.4       12       5      1       2      1
     33.5       12       7      1       4      1
     37.5       10       3      1       0      1
     41.6       12       3      1       0      1
     45.6       12       3      1       0      1
     49.7       12       5      1       2      1
     53.7       12       3      1       0      1
     57.8       12       5      1       2      1
     61.8       12       3      1       0      1
     65.9       12       3      1       0      1
     69.9        8       3      1       0      1
     73.9       12       5      1       2      1
     78.0       10       3      1       0      1
     82.0       10       3      1       0      1
     86.0       10       3      1       0      1
     90.1       10       3      1       0      1
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
     22.4     0       0  captures<=2
     29.4     0       0  captures<=2
     36.5     0       0  captures<=2
     43.6     0       0  captures<=2
     50.7     0       0  captures<=2
     57.8     0       0  captures<=2
     64.9     0       0  captures<=2
     71.9     0       0  captures<=2
     79.0     0       0  captures<=2
     86.0     0       0  captures<=2
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

Peak sprites: **411** (303 visible), text2d 20, per-view projections 9 at t=57.8s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3870** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.076          1.518       90  render/msaa_writeback/elapsed_gpu
         0.065          0.214       90  render/ui/elapsed_gpu
         0.031          0.097       90  render/ui/elapsed_cpu
         0.028          0.055       90  render/upscaling/elapsed_gpu
         0.025          0.069       88  render/main_opaque_pass_2d/elapsed_cpu
         0.015          0.038       88  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.008       90  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.009       90  render/msaa_writeback/elapsed_cpu
         0.003          0.014       90  render/upscaling/elapsed_cpu
         0.001          0.017       90  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    694187.211   11916928.000       90  render/ui/fragment_shader_invocations
    690474.341    1327104.000       88  render/main_opaque_pass_2d/fragment_shader_invocations
       531.911       2446.000       90  render/ui/vertex_shader_invocations
       264.956       1222.000       90  render/ui/clipper_primitives_out
       264.956       1222.000       90  render/ui/clipper_invocations
       192.444       8666.000       90  render/main_transparent_pass_2d/fragment_shader_invocations
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
   91970.0         1 91969994.4 91969994.4   100%  bevy_app
   91908.5         1 91908501.2 91908501.2   100%  render thread
   90969.4      5643   16120.7  630839.5     1%  update
   84216.1      5643   14924.0  594014.1     1%  main app
   84184.2      5643   14918.3  594009.3     1%  schedule{name=Main}
   35598.9      5643    6308.5  153546.7     0%  schedule{name=PreUpdate}
   32572.0      5643    5772.1  141096.6     0%  sub app{name=RenderApp}
   32517.1      5643    5762.4  141086.5     0%  schedule{name=RenderRecovery}
   32277.6      5643    5719.9  141015.7     0%  system{name="bevy_render::run_render_schedule"}
   32103.1      5643    5689.0  140979.8     0%  schedule{name=Render}
   27702.3      5643    4909.1  152130.4     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   27335.0      4661    5864.6  151963.6     1%  ggrs{name="HandleRequests"}
   27262.6      4661    5849.1  151953.3     1%  schedule{name="AdvanceWorld"}
   27206.7      4661    5837.1  151910.1     1%  schedule{name=AdvanceWorld}
   26970.4      4661    5786.4  151736.9     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   26928.2      4661    5777.3  151726.3     1%  schedule{name=GgrsSchedule}
   24652.7      5643    4368.7  332202.1     1%  schedule{name=Update}
   13897.9      5643    2462.9   25386.3     0%  schedule{name=PostUpdate}
   13707.7      5643    2429.2   18399.2     0%  system{name="bevy_render::renderer::render_system"}
   13688.6      5643    2425.8   18395.8     0%  main_render_schedule
   12630.7      5643    2238.3    7532.2     0%  schedule{name=RenderGraph}
    8859.8      5643    1570.1    6654.6     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    8352.7     22362     373.5    4431.1     0%  schedule{name=Core2d}
    6684.3      5643    1184.5  117381.8     2%  sub app{name=RenderExtractApp}
    6037.2      5643    1069.9   42532.3     1%  schedule{name=ExtractSchedule}
    5599.0   2852769       2.0    3145.4     0%  multithreaded executor
    5285.6      4661    1134.0   13567.2     0%  system{name="ambition_platformer2d_actor_monolith::body::actors::update::tick_actor_brains
    4017.5      5643     711.9    5985.3     0%  schedule{name=RunFixedMainLoop}
    3495.4      5643     619.4    5903.6     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    3424.9      5843     586.2    5228.2     0%  schedule{name=FixedMain}
    3025.6      5843     517.8    5129.4     0%  schedule{name=FixedPostUpdate}
    2788.5      5643     494.2    3465.9     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2546.5      5642     451.3    3104.4     0%  camera_schedule{camera="Camera 0 (500v0)"}
    2316.5      5643     410.5    5747.2     0%  schedule{name=Last}
    2200.6      5642     390.0    3908.9     0%  camera_schedule{camera="Camera 9 (501v0)"}
    1908.4      5843     326.6    4678.3     0%  system{name="avian2d::schedule::run_physics_schedule"}
    1859.3      1923     966.9    4659.2     0%  schedule{name=PhysicsSchedule}
    1722.2     22362      77.0     759.7     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    1691.9      5642     299.9    2878.6     0%  camera_schedule{camera="Camera 7 (502v0)"}
    1689.6      5643     299.4    3378.4     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
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
   90338.6      16011.8      5643    5431.7  update
   83622.1      14821.4      5643    4712.0  main app
   83590.2      14815.7      5643    4709.5  schedule{name=Main}
   35445.4       6282.4      5643     742.4  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw `tracy.trace`: local capture directory only.

## Which phase of the frame owned the time

Mean milliseconds per frame over 5576 frames, summing to 16.30ms:

```text
    6.39 ms   39.2%  PreUpdate
    4.44 ms   27.2%  Update
    2.50 ms   15.3%  PostUpdate
    1.37 ms    8.4%  outside
    0.73 ms    4.5%  RunFixedMainLoop
    0.44 ms    2.7%  Last
    0.23 ms    1.4%  First
    0.11 ms    0.7%  StateTransition
    0.09 ms    0.5%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    6.39    18.09     2.83   PreUpdate
    4.44     8.48     1.91   Update
    2.50     4.74     1.89   PostUpdate
    1.37     4.38     3.19   outside
    0.73     1.54     2.10   RunFixedMainLoop
    0.44     0.71     1.62   Last
    0.23     0.56     2.49   First
    0.11     0.24     2.25   StateTransition
    0.09     0.14     1.51   SpawnScene
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
  58.7%  the game itself
  40.9%  profiler (Tracy)
   0.4%  audio
```

```text
profiler (Tracy) overhead : 40.9%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           : 58.7%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 41% of sampled cycles.** Its symbol-resolution and
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
  79.4%  game binary + its Rust/C deps
  18.3%  kernel
   2.2%  GPU driver / graphics stack
   0.1%  audio
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
    32.41%     0.12%  Tracy Profiler   smash_match_profile               [.] tracy::Profiler::Worker()                                                                                                  
    31.91%     0.00%  Tracy Profiler   libc.so.6                         [.] start_thread                                                                                                               
    31.91%     0.00%  Tracy Profiler   smash_match_profile               [.] tracy::Thread::Launch(void*)                                                                                               
    31.87%     0.00%  Tracy Profiler   libc.so.6                         [.] clone3                                                                                                                     
    29.90%     0.00%  smash_match_pro  [unknown]                         [k] 0xffffffffffffffff                                                                                                         
    20.95%     0.01%  smash_match_pro  smash_match_profile               [.] _RNvMs1_NtNtCs6STjheAsjvA_8bevy_ecs8schedule8scheduleNtB5_8Schedule3run                                                    
    20.37%     1.56%  smash_match_pro  smash_match_profile               [.] _RNvXNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run
    13.38%     0.00%  smash_match_pro  smash_match_profile               [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World18try_schedule_scopeuINtNtB8_6intern8InternedDNtNtNtB8_8schedule3set13Sc
    11.62%     5.22%  Tracy Profiler   smash_match_profile               [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                
     9.48%     0.11%  Tracy Profiler   smash_match_profile               [.] tracy::Socket::HasData()                                                                                                   
     9.47%     0.05%  Tracy Profiler   smash_match_profile               [.] tracy::CallstackDataCb(void*, unsigned long, unsigned long, char const*, int, char const*)                                 
     9.10%     0.02%  Tracy Profiler   smash_match_profile               [.] tracy::Profiler::HandleSymbolQueueItem(tracy::Profiler::SymbolQueueItem const&)                                            
     9.02%     0.02%  Tracy Profiler   smash_match_profile               [.] tracy::DecodeCallstackPtr(unsigned long)                                                                                   
     8.99%     0.01%  Tracy Profiler   smash_match_profile               [.] tracy::dwarf_fileline(tracy::backtrace_state*, unsigned long, int (*)(void*, unsigned long, unsigned long, char const*, int
     8.73%     0.81%  Tracy Profiler   libc.so.6                         [.] __poll                                                                                                                     
     8.54%     0.04%  Compute Task Po  smash_match_profile               [.] _RINvMs2_NtNtCs8ZKwes1zzDd_3std6thread5localINtB6_8LocalKeyINtNtCskADBYZwJmSU_4core4cell7RefCellTNtCsbsL9vopLbSS_7parking6P
     8.46%     0.22%  Compute Task Po  smash_match_profile               [.] _RNvXs5_NtCscJLDsxKi8GJ_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNtB19_8Task
     8.42%     8.41%  Tracy Profiler   libc.so.6                         [.] _dl_addr                                                                                                                   
     8.34%     0.03%  smash_match_pro  smash_match_profile               [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace29run_without_applying_deferred          
     8.19%     0.11%  Tracy Profiler   [kernel.kallsyms]                 [k] entry_SYSCALL_64_after_hwframe                                                                                             
     7.66%     0.00%  Compute Task Po  smash_match_profile               [.] _RINvNtNtCs8ZKwes1zzDd_3std3sys9backtrace28___rust_begin_short_backtraceNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNtB
     7.13%     0.36%  Tracy Profiler   [kernel.kallsyms]                 [k] do_syscall_64                                                                                                              
     7.02%     0.06%  Compute Task Po  smash_match_profile               [.] _RNvMs1_NtCs9Rcz90M69zT_10async_task3rawINtB5_7RawTaskINtCsjFFWrCkEdj5_14async_executor15AsyncCallOnDropINtNtCscJLDsxKi8GJ_
     6.91%     0.06%  Compute Task Po  smash_match_profile               [.] _RNvXso_NtNtCskADBYZwJmSU_4core5panic11unwind_safeINtB5_16AssertUnwindSafeNCNvMs3_NtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8ex
     6.29%     0.00%  Compute Task Po  smash_match_profile               [.] _RNSNvYNCINvNtNtCs8ZKwes1zzDd_3std6thread9lifecycle15spawn_uncheckedNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNtB1d_8
     6.26%     0.09%  Tracy Profiler   [kernel.kallsyms]                 [k] x64_sys_call                                                                                                               
     6.23%     0.00%  Compute Task Po  [unknown]                         [.] 0xffffffffffffffff                                                                                                         
     5.72%     0.47%  Tracy Profiler   smash_match_profile               [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy
     5.63%     0.00%  smash_match_pro  smash_match_profile               [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World14schedule_scopeuNtCsaMjW6A3uaLQ_9bevy_ggrs12GgrsScheduleNCINvB2_12run_s
     5.59%     0.17%  Compute Task Po  smash_match_profile               [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                             
     5.10%     0.15%  Tracy Profiler   [kernel.kallsyms]                 [k] __x64_sys_poll                                                                                                             
     4.98%     0.00%  Compute Task Po  smash_match_profile               [.] _RNvNvMs0_NtNtNtCs8ZKwes1zzDd_3std3sys6thread4unixNtB7_6Thread3new12thread_start                                           
     4.69%     1.49%  Tracy Profiler   [kernel.kallsyms]                 [k] do_sys_poll                                                                                                                
     4.49%     0.00%  Tracy Profiler   smash_match_profile               [.] tracy::Profiler::CommitData()                                                                                              
     4.49%     0.00%  Tracy Profiler   smash_match_profile               [.] tracy::Profiler::SendData(char const*, unsigned long)                                                                      
```

## Assets and render resources

- Decoded images: 139 → 988 (463.5 MP, 1853.9 MB of decode work).
- Images resident at end: 864.
- **Resident image BYTES at end: 1853.9 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **810 images (365.6 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 38 GENERATED image(s) were allocated during gameplay (49.4 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 87 notable decode(s) (220.6 MP) landed AFTER the first `room-loaded` (frame 49) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.8MP  f     52  content://ambition_shared/__art/bundles/71c513e455c2e877e9b4a1a7a605658e02599f66a6d1452779827c81cd4b6e81/runtime/full/sprites/roots_east_spritesheet.png
   1.3MP  f     52  content://ambition_shared/__art/bundles/7b2e18c59fd353969aa1ef3a7dab32478b27a46fb01246109215292bfac1350c/runtime/full/sprites/castle_cells_room_spritesheet.png
   8.6MP  f     52  content://ambition_shared/__art/bundles/15cfe4be6bb89b0383fc5e7f67c65a10b8ac5875ced89bdd8f6eb25f0314eddb/runtime/full/sprites/eureka_tether_hall_spritesheet.png
   4.1MP  f     52  content://ambition_shared/__art/bundles/c3bb3e114f14bf51680fc3db1f9e41af05c66e05cc6445b5cf61529692431ef2/runtime/full/sprites/hanoi_temple_spritesheet.png
   1.8MP  f     52  content://ambition_shared/__art/bundles/9415c37d135130fad38201dabec2372cf710621635d69f02c0b927e70491415f/runtime/full/sprites/roots_west_spritesheet.png
   1.4MP  f     52  content://ambition_shared/__art/bundles/3b45637072100dc9b818cad5f230aa4e070d11305305619c3308167f233e4f4f/runtime/full/sprites/evil_forest_roadside_spritesheet.png
   1.8MP  f     52  content://ambition_shared/__art/bundles/6a3532230602c226a3c430262a0d45ddd79d228769e0672be71804af8116b09c/runtime/full/sprites/hill_east_spritesheet.png
   1.6MP  f     52  content://ambition_shared/__art/bundles/111c06cf11ae5067bd28e6003d7d00b16b1b43b595ff6ef2bb006ad46f25ac35/runtime/full/sprites/hill_west_spritesheet.png
```

⚠ 87 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

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
- `perf.data`: 129681532 bytes

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

