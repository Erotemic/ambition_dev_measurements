# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `7b00d6273b9f` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
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

**Two-pass timed Smash capture.** The primary frame, census, and native
`perf` reports come from a separate build **without Tracy**. The
`tracy_diagnostic/` directory is a second launch built with the `profile`
feature for per-system zone analysis. Do not combine their absolute
frame-time or CPU percentages as if they were the same run.
No native recorder remains active during Tracy shutdown/export.
If the primary pass lacks GPU/render diagnostics, inspect the second
pass at `tracy_diagnostic/render_diagnostics.csv`.

- Active match in primary capture: **6.3–84.2s** (77.9s).
- The `perf-report-active*.txt` tables exclude startup and post-match samples using an approximate perf time filter.
- Match ended early: **yes**.

- Tracy diagnostic build/launch status: `0`.

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

Observed span of the game's own log: **96.7s**.

## Frame time

91 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      3.1      32    20.5     7.0   107.2   183.8    183.8
      4.1     108     9.3     7.4    17.8    28.2    113.8
     30.2     144     7.1     6.9    10.0    11.6     26.8
     81.4     142     7.1     7.0     8.8    13.0     22.9
      5.1     143     7.0     6.6    10.8    14.0     21.9
     50.3     144     7.0     6.8    10.5    12.9     15.2
     89.4     144     7.0     7.0     7.5    11.6     14.1
     11.1     144     7.0     6.9    10.7    13.1     14.1
```

Full series: `frame_times.csv`.

4 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    5.736s     183.8 ms
    5.354s     163.1 ms
    6.069s     113.7 ms
    5.191s     104.6 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1124 images / 568.0 MP in total; 211 were notable enough to print.

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
      2.0        4       3      1       0      1
      6.1        8       3      1       0      1
     10.1        8       5      1       2      1
     14.1        8       3      1       0      1
     18.1        8       3      1       0      1
     22.1       12       3      1       0      1
     26.2       12       3      1       0      1
     30.2       12       3      1       0      1
     34.2       12       3      1       0      1
     38.2       10       3      1       0      1
     42.2       12       3      1       0      1
     46.2       10       3      1       0      1
     50.3       12       5      1       2      1
     54.3       12       5      1       2      1
     58.3       12       5      1       2      1
     62.3       12       4      1       1      1
     66.3       12       3      1       0      1
     70.3       10       3      1       0      1
     74.3        8       3      1       0      1
     78.4       10       3      1       0      1
     82.4       10       3      1       0      1
     86.4       10       3      1       0      1
     90.4       10       3      1       0      1
```

Peak world-rendering cameras: **1** at t=2.0s.

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

Peak active portal capture rigs: **0** of 0 at t=2.0s.

```text
        t  rigs  active  budget
      2.0     0       0  captures<=2
      9.1     0       0  captures<=2
     16.1     0       0  captures<=2
     23.2     0       0  captures<=2
     30.2     0       0  captures<=2
     37.2     0       0  captures<=2
     44.2     0       0  captures<=2
     51.3     0       0  captures<=2
     58.3     0       0  captures<=2
     65.3     0       0  captures<=2
     72.3     0       0  captures<=2
     79.4     0       0  captures<=2
     86.4     0       0  captures<=2
     93.4     0       0  captures<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **8** (largest dimension 1152px) at t=19.1s.

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

Peak sprites: **482** (407 visible), text2d 25, per-view projections 9 at t=31.2s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3870** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.041          0.155       91  render/ui/elapsed_gpu
         0.034          0.062       91  render/msaa_writeback/elapsed_gpu
         0.016          0.057       91  render/ui/elapsed_cpu
         0.016          0.030       91  render/upscaling/elapsed_gpu
         0.011          0.030       89  render/main_opaque_pass_2d/elapsed_cpu
         0.008          0.017       89  render/main_opaque_pass_2d/elapsed_gpu
         0.003          0.009       91  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.008       91  render/msaa_writeback/elapsed_cpu
         0.002          0.014       91  render/upscaling/elapsed_cpu
         0.001          0.007       91  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    707081.110   10476910.000       91  render/ui/fragment_shader_invocations
    649193.213    1327104.000       89  render/main_opaque_pass_2d/fragment_shader_invocations
       561.956       2454.000       91  render/ui/vertex_shader_invocations
       283.088       8659.000       91  render/main_transparent_pass_2d/fragment_shader_invocations
       279.978       1226.000       91  render/ui/clipper_primitives_out
       279.978       1226.000       91  render/ui/clipper_invocations
        18.831         70.000       89  render/main_opaque_pass_2d/vertex_shader_invocations
        15.933         64.000       89  render/main_opaque_pass_2d/clipper_invocations
        15.933         64.000       89  render/main_opaque_pass_2d/clipper_primitives_out
         1.055         32.000       91  render/main_transparent_pass_2d/vertex_shader_invocations
         0.989         30.000       91  render/main_transparent_pass_2d/clipper_invocations
         0.989         30.000       91  render/main_transparent_pass_2d/clipper_primitives_out
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
   93776.2         1 93776204.1 93776204.1   100%  bevy_app
   93689.9         1 93689919.0 93689919.0   100%  render thread
   92662.0      5188   17860.8 1364018.8     1%  update
   85793.5      5188   16536.9 1218112.6     1%  main app
   85763.9      5188   16531.2 1218107.4     1%  schedule{name=Main}
   37184.1      5188    7167.3  852352.6     2%  schedule{name=PreUpdate}
   32689.1      5188    6300.9  196820.6     1%  sub app{name=RenderApp}
   32634.4      5188    6290.4  196814.3     1%  schedule{name=RenderRecovery}
   32386.3      5188    6242.6  196729.7     1%  system{name="bevy_render::run_render_schedule"}
   32214.4      5188    6209.4  196682.6     1%  schedule{name=Render}
   29420.1      5188    5670.8  850682.9     3%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   29037.5      4660    6231.2  850273.7     3%  ggrs{name="HandleRequests"}
   28964.3      4660    6215.5  850256.2     3%  schedule{name="AdvanceWorld"}
   28906.8      4660    6203.2  850226.5     3%  schedule{name=AdvanceWorld}
   28667.5      4660    6151.8  850031.3     3%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   28625.6      4660    6142.8  850022.8     3%  schedule{name=GgrsSchedule}
   24448.0      5188    4712.4  682491.5     3%  schedule{name=Update}
   13884.3      5188    2676.2   69385.1     0%  schedule{name=PostUpdate}
   13694.0      5188    2639.5   38381.9     0%  system{name="bevy_render::renderer::render_system"}
   13675.2      5188    2635.9   38379.5     0%  main_render_schedule
   12644.9      5188    2437.3   38254.5     0%  schedule{name=RenderGraph}
    8875.7      5188    1710.8    6734.7     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    8373.8     21060     397.6    4177.1     0%  schedule{name=Core2d}
    6798.9      5188    1310.5  146010.8     2%  sub app{name=RenderExtractApp}
    5917.1      5188    1140.5   55344.6     1%  schedule{name=ExtractSchedule}
    5429.7      4660    1165.2   19944.1     0%  system{name="ambition_platformer2d_actor_monolith::body::actors::update::tick_actor_brains
    5408.4   2635675       2.1   70358.0     1%  multithreaded executor
    4040.0      5188     778.7    6880.6     0%  schedule{name=RunFixedMainLoop}
    3500.3      5188     674.7    6683.6     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    3429.8      5867     584.6    5924.2     0%  schedule{name=FixedMain}
    3020.4      5867     514.8    5771.8     0%  schedule{name=FixedPostUpdate}
    2831.4      5188     545.8   32522.9     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2484.1      5187     478.9    4191.4     0%  camera_schedule{camera="Camera 0 (500v0)"}
    2344.6      5188     451.9    7368.6     0%  schedule{name=Last}
    2185.1      5187     421.3    3942.1     0%  camera_schedule{camera="Camera 9 (501v0)"}
    1893.9      5867     322.8    4735.6     0%  system{name="avian2d::schedule::run_physics_schedule"}
    1844.2      1920     960.5    4706.8     0%  schedule{name=PhysicsSchedule}
    1718.5     21060      81.6    1860.6     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    1703.7      5187     328.5    3555.4     0%  camera_schedule{camera="Camera 7 (502v0)"}
    1674.9      5188     322.8     884.6     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
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
   91298.0      17601.3      5188    6297.9  update
   84575.4      16305.3      5188    5571.5  main app
   84545.8      16299.6      5188    5565.8  schedule{name=Main}
   36331.7       7004.4      5188     745.2  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw `tracy.trace`: local capture directory only.

## Which phase of the frame owned the time

Mean milliseconds per frame over 12952 frames, summing to 7.06ms:

```text
    3.74 ms   53.0%  outside
    1.04 ms   14.8%  PreUpdate
    1.03 ms   14.6%  Update
    0.92 ms   13.0%  PostUpdate
    0.11 ms    1.6%  RunFixedMainLoop
    0.10 ms    1.4%  Last
    0.06 ms    0.8%  First
    0.03 ms    0.4%  SpawnScene
    0.02 ms    0.2%  StateTransition
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    3.74     2.30     0.61   outside
    1.04     2.91     2.79   PreUpdate
    1.03     2.56     2.47   Update
    0.92     1.63     1.77   PostUpdate
    0.11     0.33     2.87   RunFixedMainLoop
    0.10     0.15     1.44   Last
    0.06     0.13     2.22   First
    0.03     0.06     1.89   SpawnScene
    0.02     0.03     2.08   StateTransition
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
  99.5%  the game itself
   0.5%  audio
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           : 99.5%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  78.9%  game binary + its Rust/C deps
  15.7%  kernel
   5.3%  GPU driver / graphics stack
   0.1%  audio
   0.0%  software rasterizer (CPU emulating a GPU)
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

If present, active-match native reports exclude startup and results
screen time; see `match-window.json` for the selected interval.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     2.36%  sprite-sheet-wa  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     1.99%  smash_match_pro  smash_match_profile               [.] _RNvXNtNtNtCsC5QvuQdLl2_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run           
     1.67%  smash_match_pro  smash_match_profile               [.] _mi_page_malloc_zero                                                                                                                 
     1.45%  Compute Task Po  smash_match_profile               [.] _RNvMs1_NtNtNtCsC5QvuQdLl2_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                   
     1.14%  sprite-sheet-wa  smash_match_profile               [.] _RINvMs_NtNtNtCsgFX3jaOk65H_5alloc11collections5btree6searchINtNtB7_4node7NodeRefNtNtBY_6marker3MutNtNtBb_6string6StringINtNtB7_3map8
     1.05%  smash_match_pro  smash_match_profile               [.] _RNvXsb_NtCsgFX3jaOk65H_5alloc3vecINtB5_3VecNtCs6y8RcMu9aQ3_21ambition_sprite_sheet8SheetRowENtNtCskADBYZwJmSU_4core5clone5Clone5clon
     0.85%  Compute Task Po  smash_match_profile               [.] _RNvXs5_NtCscJLDsxKi8GJ_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs28NoSLtwhar_10bevy_tasks9task_poolNtB19_8TaskPool12new_
     0.67%  smash_match_pro  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.66%  Compute Task Po  smash_match_profile               [.] _RNvMs2_Cs5qfZyUtJKxp_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs9Rcz90M69zT_10async_task8runnable8RunnableE3popCs28NoSLtwhar_10b
     0.66%  Compute Task Po  smash_match_profile               [.] _RNvMNtCs5qfZyUtJKxp_16concurrent_queue7boundedINtB2_7BoundedNtNtCs9Rcz90M69zT_10async_task8runnable8RunnableE3popCs28NoSLtwhar_10bev
     0.66%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.63%  Compute Task Po  smash_match_profile               [.] _mi_page_malloc_zero                                                                                                                 
     0.57%  Compute Task Po  smash_match_profile               [.] _RNvNtNtNtCsC5QvuQdLl2_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                        
     0.53%  Compute Task Po  smash_match_profile               [.] _mi_page_free_collect                                                                                                                
     0.53%  Compute Task Po  smash_match_profile               [.] _RNvMNtNtNtNtCs8ZKwes1zzDd_3std3sys4sync5mutex5futexNtB2_5Mutex14lock_contended                                                      
     0.53%  smash_match_pro  smash_match_profile               [.] mi_free                                                                                                                              
     0.51%  Compute Task Po  smash_match_profile               [.] _RNvMsd_CsjFFWrCkEdj5_14async_executorNtB5_6Ticker5sleep                                                                             
     0.50%  smash_match_pro  smash_match_profile               [.] _RNvMs1_NtNtNtCsC5QvuQdLl2_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                   
     0.46%  Compute Task Po  smash_match_profile               [.] _RNvMs0_CsiQd8IESJLk0_11fixedbitsetNtB5_11FixedBitSet11is_disjoint                                                                   
     0.44%  sprite-sheet-wa  [kernel.kallsyms]                 [k] _copy_to_iter                                                                                                                        
     0.43%  sprite-sheet-wa  smash_match_profile               [.] _RNvMsi_NtNtNtCsgFX3jaOk65H_5alloc11collections5btree3mapINtB5_8BTreeMapNtNtBb_6string6StringB17_E6insertCs2mynKJEWxKj_21ambition_pla
     0.42%  Compute Task Po  [kernel.kallsyms]                 [k] psi_group_change                                                                                                                     
     0.42%  smash_match_pro  smash_match_profile               [.] _RINvNtCskADBYZwJmSU_4core3ptr9drop_glueNtCs6y8RcMu9aQ3_21ambition_sprite_sheet8SheetRowECsboJjwvqjfv1_26ambition_character_sprites.l
     0.41%  smash_match_pro  smash_match_profile               [.] _RINvNtNtNtCsC5QvuQdLl2_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace12readonly_runbEB8_                                
     0.37%  Compute Task Po  [kernel.kallsyms]                 [k] futex_wake                                                                                                                           
     0.37%  smash_match_pro  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     0.36%  Compute Task Po  [kernel.kallsyms]                 [k] try_to_wake_up                                                                                                                       
     0.33%  Compute Task Po  [kernel.kallsyms]                 [k] _raw_spin_lock                                                                                                                       
     0.33%  smash_match_pro  smash_match_profile               [.] _RNvMs0_NtCs6y8RcMu9aQ3_21ambition_sprite_sheet6framesNtB7_11SheetRecord18flat_index_in_page                                         
     0.33%  Compute Task Po  libc.so.6                         [.] syscall                                                                                                                              
     0.33%  smash_match_pro  smash_match_profile               [.] _RNvXs8_NtCs9Rcz90M69zT_10async_task4taskINtB5_12FallibleTaskINtNtCskADBYZwJmSU_4core6result6ResultuINtNtCsgFX3jaOk65H_5alloc5boxed3B
     0.33%  Compute Task Po  [kernel.kallsyms]                 [k] clear_bhb_loop                                                                                                                       
     0.28%  Compute Task Po  smash_match_profile               [.] _RINvMNtCs5qfZyUtJKxp_16concurrent_queue7boundedINtB3_7BoundedNtNtNtNtCsC5QvuQdLl2_8bevy_ecs8schedule8executor14multi_threaded12Syste
     0.27%  Compute Task Po  smash_match_profile               [.] _RNvMsb_CsjFFWrCkEdj5_14async_executorNtB5_5State6active                                                                             
     0.27%  Compute Task Po  smash_match_profile               [.] _RNvMs1_NtCs9Rcz90M69zT_10async_task3rawINtB5_7RawTaskINtCsjFFWrCkEdj5_14async_executor15AsyncCallOnDropINtNtCscJLDsxKi8GJ_12futures_
```

## Assets and render resources

- Decoded images: 139 → 1124 (568.0 MP, 2272.2 MB of decode work).
- Images resident at end: 868.
- **Resident image BYTES at end: 2272.2 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **806 images (362.5 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 98 GENERATED image(s) were allocated during gameplay (127.4 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 85 notable decode(s) (216.0 MP) landed AFTER the first `room-loaded` (frame 61) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   2.4MP  f     65  content://ambition_shared/__art/bundles/e6c0ac2beab7be264d1e052cc0f420c2282e7a2af6a2bfb671d91fb3680d1e23/runtime/full/sprites/eureka_lab_room_spritesheet.png
   1.7MP  f     65  content://ambition_shared/__art/bundles/5bbbec296222fe797dcca2fb9b56cf7682229d9cb7cac1403c599e1574151f93/runtime/full/sprites/castle_guild_room_spritesheet.png
   1.7MP  f     65  content://ambition_shared/__art/bundles/4771c5923143e4ecf2a9ba53dc3f275034d15d63cf44003ae2880cd4573a0e2a/runtime/full/sprites/tea_room_spritesheet.png
   2.3MP  f     65  content://ambition_shared/__art/bundles/57be1a6ff4a899edcbd1b493ac864347541ed0308bfbb3925d1ad6634794c6d3/runtime/full/sprites/castle_kitchens_room_spritesheet.png
   1.1MP  f     66  content://ambition_shared/__art/bundles/e9b2b9177a17424f4d9bbe814290d8f3555e4969d9345fdb84aab56d14186d12/runtime/full/sprites/hoard_room_spritesheet.png
   4.9MP  f     67  content://ambition_shared/__art/bundles/7625b5096634050b370f8eceacb54249d5569c5cd70fbce45582635b3c953be2/runtime/full/sprites/eureka_observatory_room_spritesheet.png
   4.5MP  f     68  content://ambition_shared/__art/bundles/167477b208f69439124209c24609036f5a6061d929977ba79e41415218a70f0c/runtime/full/sprites/terminus_hall_spritesheet.png
   2.4MP  f     68  content://ambition_shared/__art/bundles/48d101a50c2a45a674358860dcf34cf8eedcebbbd9ff253d2d3abbc444bd85ac/runtime/full/sprites/catacombs_east_spritesheet.png
```

⚠ 85 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  99x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 62146244 bytes

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
| `perf-report-active.txt` | native symbols restricted to the live-match interval | yes | yes |
| `perf-report-active-by-dso.txt` | live-match-only shared-object CPU proportions | yes | yes |
| `perf-report-active-by-thread.txt` | live-match-only thread CPU proportions | yes | yes |
| `match-window.json` | primary pass live-start/end markers and native filter bounds | yes | yes |
| `tracy_diagnostic` | second launch: Tracy build, stderr, and diagnostic metadata | yes | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes | yes |
| `perf.data` | the raw perf capture | yes | no |

