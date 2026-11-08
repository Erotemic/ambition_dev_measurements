# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `7d6d8e4017a2` on `main` |
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

Observed span of the game's own log: **111.5s**.

## Frame time

90 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.1      43    15.8     7.2    63.2   181.3    181.3
      3.1      50    20.2    14.4    43.2   170.0    170.0
     18.2      48    21.0    18.5    35.0    45.0     45.0
     26.3      59    16.7    15.4    25.8    36.3     42.4
     25.3      54    19.2    18.1    25.2    31.1     41.4
     44.5      47    21.8    20.3    33.3    34.9     34.9
     45.5      44    22.9    22.6    29.8    34.2     34.2
     80.7     101     9.9     9.2    14.4    18.9     34.2
```

Full series: `frame_times.csv`.

20 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    4.072s     181.3 ms
    4.424s     169.9 ms
    3.685s     136.1 ms
    4.555s      96.8 ms
    3.549s      63.6 ms
   19.813s      44.9 ms
    4.630s      43.3 ms
   27.435s      42.5 ms
   19.853s      39.8 ms
   27.356s      38.9 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1006 images / 477.4 MP in total; 159 were notable enough to print.

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
     13.2       10       3      1       0      1
     17.2       10       5      1       2      1
     21.2       12       5      1       2      1
     25.3       12       5      1       2      1
     29.3       12       5      1       2      1
     33.3       12       3      1       0      1
     37.4       10       3      1       0      1
     41.4       12       7      1       4      1
     45.5       12       5      1       2      1
     49.5       12       7      1       4      1
     53.5       12       3      1       0      1
     57.6       12       5      1       2      1
     61.6        8       3      1       0      1
     65.7       10       3      1       0      1
     69.7        8       3      1       0      1
     73.7       12       5      1       2      1
     77.7       10       3      1       0      1
     81.8       10       3      1       0      1
     85.8       10       3      1       0      1
     89.8       10       3      1       0      1
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
     36.4     0       0  captures<=2
     43.5     0       0  captures<=2
     50.5     0       0  captures<=2
     57.6     0       0  captures<=2
     64.7     0       0  captures<=2
     71.7     0       0  captures<=2
     78.7     0       0  captures<=2
     85.8     0       0  captures<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **8** (largest dimension 1152px) at t=18.2s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1693        0        0
       end       4096        2105        0        0
      peak       4096        2105        4        0
```

Entity count rose by 2048 and then held flat at 4096 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **477** (368 visible), text2d 20, per-view projections 9 at t=57.6s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3870** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.054          0.191       90  render/ui/elapsed_gpu
         0.047          0.097       90  render/msaa_writeback/elapsed_gpu
         0.025          0.076       90  render/ui/elapsed_cpu
         0.022          0.127       88  render/main_opaque_pass_2d/elapsed_cpu
         0.022          0.045       90  render/upscaling/elapsed_gpu
         0.012          0.030       88  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.022       90  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.013       90  render/msaa_writeback/elapsed_cpu
         0.002          0.005       90  render/upscaling/elapsed_cpu
         0.001          0.012       90  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    743282.250    1327104.000       88  render/main_opaque_pass_2d/fragment_shader_invocations
    694099.344   11916838.000       90  render/ui/fragment_shader_invocations
       533.244       2446.000       90  render/ui/vertex_shader_invocations
       265.622       1222.000       90  render/ui/clipper_primitives_out
       265.622       1222.000       90  render/ui/clipper_invocations
       192.378       8664.000       90  render/main_transparent_pass_2d/fragment_shader_invocations
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
   91963.6         1 91963636.4 91963636.4   100%  bevy_app
   91903.0         1 91902968.8 91902968.8   100%  render thread
   90978.4      6377   14266.6  592794.7     1%  update
   84368.7      6377   13230.1  551757.4     1%  main app
   84339.3      6377   13225.5  551752.7     1%  schedule{name=Main}
   37982.0      6377    5956.1  154081.7     0%  schedule{name=PreUpdate}
   30709.5      6377    4815.7  148317.1     0%  sub app{name=RenderApp}
   30657.4      6377    4807.5  148310.7     0%  schedule{name=RenderRecovery}
   30431.7      6377    4772.1  148246.7     0%  system{name="bevy_render::run_render_schedule"}
   30267.0      6377    4746.3  148204.0     0%  schedule{name=Render}
   30226.5      6377    4739.9  152816.6     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   29896.2      4660    6415.5  152686.3     1%  ggrs{name="HandleRequests"}
   29833.4      4660    6402.0  152676.7     1%  schedule{name="AdvanceWorld"}
   29782.4      4660    6391.1  152659.7     1%  schedule{name=AdvanceWorld}
   29580.4      4660    6347.7  152549.0     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   29543.2      4660    6339.7  152543.3     1%  schedule{name=GgrsSchedule}
   23725.2      6377    3720.4  304385.5     1%  schedule{name=Update}
   13427.0      6377    2105.5   20296.9     0%  schedule{name=PostUpdate}
   12473.8      6377    1956.1    9333.4     0%  system{name="bevy_render::renderer::render_system"}
   12456.4      6377    1953.3    9327.8     0%  main_render_schedule
   11499.3      6377    1803.2    8991.9     0%  schedule{name=RenderGraph}
   11487.9      4660    2465.2    7032.7     0%  system{name="ambition_platformer2d_actor_monolith::body::actors::update::tick_actor_brains
    8023.3      6377    1258.2    7660.6     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    7560.4     24508     308.5    4401.9     0%  schedule{name=Core2d}
    6544.4      6377    1026.3  128547.4     2%  sub app{name=RenderExtractApp}
    5931.6      6377     930.2   32870.5     1%  schedule{name=ExtractSchedule}
    5479.0   3208968       1.7    1947.5     0%  multithreaded executor
    3530.5      6377     553.6    4087.4     0%  schedule{name=RunFixedMainLoop}
    3035.3      6377     476.0    3936.9     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    2969.3      5845     508.0    3166.7     0%  schedule{name=FixedMain}
    2629.1      5845     449.8    3098.4     0%  schedule{name=FixedPostUpdate}
    2577.1      6377     404.1    3882.1     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2396.0      6376     375.8    1491.7     0%  camera_schedule{camera="Camera 0 (500v0)"}
    2202.1      6377     345.3    6352.7     0%  schedule{name=Last}
    2038.8      6376     319.8    2345.4     0%  camera_schedule{camera="Camera 9 (501v0)"}
    1637.6      5845     280.2    2592.7     0%  system{name="avian2d::schedule::run_physics_schedule"}
    1631.8      6377     255.9     635.5     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
    1604.6     24508      65.5     478.8     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    1594.0      1921     829.8    2579.5     0%  schedule{name=PhysicsSchedule}
    1567.9      6376     245.9    3521.0     0%  camera_schedule{camera="Camera 7 (502v0)"}
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
   90385.6      14175.9      6377    5613.3  update
   83816.9      13145.7      6377    4989.2  main app
   83787.5      13141.1      6377    4986.8  schedule{name=Main}
   37828.0       5932.9      6377     697.1  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 6285 frames, summing to 14.43ms:

```text
    6.04 ms   41.9%  PreUpdate
    3.78 ms   26.2%  Update
    2.14 ms   14.8%  PostUpdate
    1.18 ms    8.2%  outside
    0.57 ms    4.0%  RunFixedMainLoop
    0.37 ms    2.5%  Last
    0.19 ms    1.3%  First
    0.09 ms    0.6%  StateTransition
    0.08 ms    0.5%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    6.04    16.34     2.70   PreUpdate
    3.78     7.30     1.93   Update
    2.14     4.03     1.89   PostUpdate
    1.18     3.85     3.25   outside
    0.57     1.25     2.20   RunFixedMainLoop
    0.37     0.59     1.61   Last
    0.19     0.45     2.41   First
    0.09     0.22     2.39   StateTransition
    0.08     0.12     1.55   SpawnScene
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
  74.2%  the game itself
  25.4%  profiler (Tracy)
   0.4%  audio
```

```text
profiler (Tracy) overhead : 25.4%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           : 74.2%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 25% of sampled cycles.** Its symbol-resolution and
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
  82.4%  game binary + its Rust/C deps
  15.0%  kernel
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
    36.46%     0.00%  smash_match_pro  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
    24.77%     0.01%  smash_match_pro  smash_match_profile                    [.] _RNvMs1_NtNtCs6STjheAsjvA_8bevy_ecs8schedule8scheduleNtB5_8Schedule3run                                               
    23.93%     1.77%  smash_match_pro  smash_match_profile                    [.] _RNvXNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecuto
    16.18%     0.01%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World18try_schedule_scopeuINtNtB8_6intern8InternedDNtNtNtB8_8schedule3se
    10.90%     0.09%  Compute Task Po  smash_match_profile                    [.] _RINvMs2_NtNtCs8ZKwes1zzDd_3std6thread5localINtB6_8LocalKeyINtNtCskADBYZwJmSU_4core4cell7RefCellTNtCsbsL9vopLbSS_7park
    10.80%     0.25%  Compute Task Po  smash_match_profile                    [.] _RNvXs5_NtCscJLDsxKi8GJ_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNtB19_
    10.07%     0.00%  Compute Task Po  smash_match_profile                    [.] _RINvNtNtCs8ZKwes1zzDd_3std3sys9backtrace28___rust_begin_short_backtraceNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_po
     9.81%     0.04%  smash_match_pro  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace29run_without_applying_deferred     
     9.03%     0.06%  Compute Task Po  smash_match_profile                    [.] _RNvMs1_NtCs9Rcz90M69zT_10async_task3rawINtB5_7RawTaskINtCsjFFWrCkEdj5_14async_executor15AsyncCallOnDropINtNtCscJLDsxK
     8.84%     0.06%  Compute Task Po  smash_match_profile                    [.] _RNvXso_NtNtCskADBYZwJmSU_4core5panic11unwind_safeINtB5_16AssertUnwindSafeNCNvMs3_NtNtNtCs6STjheAsjvA_8bevy_ecs8schedu
     8.80%     0.04%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Worker()                                                                                             
     8.50%     0.00%  Tracy Profiler   libc.so.6                              [.] start_thread                                                                                                          
     8.50%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Thread::Launch(void*)                                                                                          
     8.33%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNSNvYNCINvNtNtCs8ZKwes1zzDd_3std6thread9lifecycle15spawn_uncheckedNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNt
     7.67%     1.61%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                           
     7.08%     0.22%  Compute Task Po  smash_match_profile                    [.] _RNvNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                        
     6.64%     0.00%  Compute Task Po  [unknown]                              [.] 0xffffffffffffffff                                                                                                    
     6.43%     0.00%  Compute Task Po  smash_match_profile                    [.] _RNvNvMs0_NtNtNtCs8ZKwes1zzDd_3std3sys6thread4unixNtB7_6Thread3new12thread_start                                      
     6.17%     0.00%  smash_match_pro  smash_match_profile                    [.] _RINvMs2_NtCs6STjheAsjvA_8bevy_ecs5worldNtB6_5World14schedule_scopeuNtCsaMjW6A3uaLQ_9bevy_ggrs12GgrsScheduleNCINvB2_12
     6.03%     0.52%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(
     5.40%     0.00%  Compute Task Po  libc.so.6                              [.] start_thread                                                                                                          
     4.81%     2.39%  smash_match_pro  smash_match_profile                    [.] _RNvXs1_CsauKbtVXGAam_13tracing_tracyNtB5_10TracyLayerINtNtCshdbjT2rFsCC_18tracing_subscriber5layer5LayerINtNtBS_7laye
     4.74%     4.56%  Tracy Profiler   smash_match_profile                    [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                            
     4.70%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::CommitData()                                                                                         
     4.70%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::SendData(char const*, unsigned long)                                                                 
     4.13%     0.00%  Tracy Symbol Wo  [unknown]                              [k] 0xffffffffffffffff                                                                                                    
     3.66%     0.04%  smash_match_pro  smash_match_profile                    [.] _RNvXs_NtNtCshdbjT2rFsCC_18tracing_subscriber5layer7layeredINtB4_7LayeredNtCsauKbtVXGAam_13tracing_tracy10TracyLayerIB
     3.60%     0.05%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSCALL_64_after_hwframe                                                                                        
     3.38%     0.01%  smash_match_pro  smash_match_profile                    [.] _RINvNtCskADBYZwJmSU_4core3ptr9drop_glueNtNtCslpXFRpECQzt_7tracing4span4SpanECs6STjheAsjvA_8bevy_ecs                  
     3.33%     0.03%  smash_match_pro  smash_match_profile                    [.] _RNvMNtCslpXFRpECQzt_7tracing4spanNtB2_4Span9make_with                                                                
     3.32%     0.56%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                             
     3.32%     0.02%  smash_match_pro  smash_match_profile                    [.] _RNvMNtCslpXFRpECQzt_7tracing4spanNtB2_4Span3new                                                                      
     3.32%     0.03%  smash_match_pro  smash_match_profile                    [.] _RNvXs_NtNtCshdbjT2rFsCC_18tracing_subscriber5layer7layeredINtB4_7LayeredNtCsauKbtVXGAam_13tracing_tracy10TracyLayerIB
     3.24%     0.02%  smash_match_pro  smash_match_profile                    [.] _RNvNtCskutRBcm19Zl_26ambition_character_sprites13attack_hitbox25actor_attack_hitbox_local                            
     3.22%     0.17%  Tracy Profiler   [kernel.kallsyms]                      [k] do_syscall_64                                                                                                         
```

## Assets and render resources

- Decoded images: 139 → 1006 (477.4 MP, 1909.7 MB of decode work).
- Images resident at end: 864.
- **Resident image BYTES at end: 1909.7 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **810 images (365.6 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 46 GENERATED image(s) were allocated during gameplay (59.8 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 88 notable decode(s) (221.7 MP) landed AFTER the first `room-loaded` (frame 49) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   3.5MP  f     52  content://ambition_shared/__art/bundles/fda0778b725eb328b9f1691e7fbad8c5f0b33ceafd58978c7caacaa3373f1e5f/runtime/full/sprites/reef_wreck_spritesheet.png
   1.3MP  f     52  content://ambition_shared/__art/bundles/7b2e18c59fd353969aa1ef3a7dab32478b27a46fb01246109215292bfac1350c/runtime/full/sprites/castle_cells_room_spritesheet.png
   3.9MP  f     52  content://ambition_shared/__art/bundles/b92c7effdb548975ce7fed699d4ffa3b2597067d8feecade81a05cec76334244/runtime/full/sprites/ridge_east_spritesheet.png
   4.9MP  f     52  content://ambition_shared/__art/bundles/7625b5096634050b370f8eceacb54249d5569c5cd70fbce45582635b3c953be2/runtime/full/sprites/eureka_observatory_room_spritesheet.png
   3.9MP  f     52  content://ambition_shared/__art/bundles/b6244992db564c8c1cbae13b0512a1037f3aa9b2ebc411a41777c9350500adb9/runtime/full/sprites/ridge_west_spritesheet.png
   4.1MP  f     52  content://ambition_shared/__art/bundles/c3bb3e114f14bf51680fc3db1f9e41af05c66e05cc6445b5cf61529692431ef2/runtime/full/sprites/hanoi_temple_spritesheet.png
   1.2MP  f     52  content://ambition_shared/__art/bundles/580b3cdf846906cc0f8f7ab19e24d352217f6ede35909fdd195c5049800e98aa/runtime/full/sprites/rift_outlines_spritesheet.png
   1.8MP  f     52  content://ambition_shared/__art/bundles/6a3532230602c226a3c430262a0d45ddd79d228769e0672be71804af8116b09c/runtime/full/sprites/hill_east_spritesheet.png
```

⚠ 88 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  47x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 106774296 bytes

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

