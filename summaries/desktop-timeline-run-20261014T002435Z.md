# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `752a24b685d6` on `main` |
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
| kernel | `Linux toothbrush 6.8.0-146-generic #146-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep  3 16:12:30 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
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
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.99.02", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **134.8s**.

## Frame time

108 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     18.2      37    52.7    22.1    31.6  1155.2   1155.2
     15.1      43    27.0    11.7    20.6   567.2    567.2
     16.2       9   110.1    59.4   395.1   395.1    395.1
      3.0      24    28.9     8.1   158.5   290.9    290.9
     29.4      32    34.0    26.9    87.4   108.9    108.9
     19.2      38    26.6    22.4    49.2   101.8    101.8
     32.4      23    44.5    40.6    83.6   100.3    100.3
     30.4      33    29.2    26.0    47.2    83.2     83.2
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   21.144s    1155.2 ms
   18.134s     567.2 ms
   18.705s     395.1 ms
    5.759s     290.9 ms
    5.468s     158.5 ms
   18.859s     154.0 ms
   18.969s     109.6 ms
   19.078s     108.9 ms
   32.356s     108.9 ms
   32.162s     102.7 ms
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
    1       61     True  central_hub_complex -> central_hub_complex
    2       19     True  central_hub_complex -> vertical_shaft
    3       29     True  vertical_shaft -> central_hub_complex
    4       23     True  central_hub_complex -> hall_of_characters
```

Frames over 33.4 ms AFTER the last transition was logged (t=34.775s): **14**, worst 100.3 ms.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

⛔ **THE SPIKE LOG HIT ITS 60-LINE CAP, so this count is a FLOOR, not a total.** Percentile summaries continued; the per-frame lines did not.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 786 images / 368.8 MP in total; 79 were notable enough to print.

```text
ordered by: frame
before central_hub_complex: 9 image(s), 27.9 MP
  unknown                  5    20.1 MP
  vanity-card              1     3.0 MP
  boss-sheet               1     2.0 MP
  character-sheet          1     1.6 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      2.0        5       3      1       0      1
      7.0        5       3      1       0      1
     12.1        5       3      1       0      1
     18.2        5       3      1       0      1
     23.2       11       7      1       4      1
     28.3       11       7      1       4      1
     33.4       11       7      1       4      1
     38.5       11       3      1       0      1
     43.6       11       3      1       0      1
     48.7       11       3      1       0      1
     53.8       11       7      1       4      1
     58.8       11       3      1       0      1
     63.9       11       3      1       0      1
     69.0       11       7      1       4      1
     74.1       11       3      1       0      1
     79.1       11       5      1       2      1
     84.2       11       3      1       0      1
     89.4       11       5      1       2      1
     94.4       11       3      1       0      1
     99.5       11       7      1       4      1
    104.6       11       3      1       0      1
    109.7       11       4      1       0      1
```

Peak world-rendering cameras: **1** at t=2.0s.

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

Peak active portal capture rigs: **0** of 0 at t=2.0s.

```text
        t  rigs  active  budget
      2.0     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     11.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     21.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     30.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     39.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     48.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     57.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     67.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     76.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     85.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     94.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    103.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    112.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 1152px) at t=23.2s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1607        0        0
       end      16384        2488      155        1
      peak      16384        2488      155        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **6071** (368 visible), text2d 515, per-view projections 106 at t=85.3s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3734** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.174          0.602        4  render/main_opaque_pass_3d/elapsed_cpu
         0.140          0.145        4  render/clustering/elapsed_gpu
         0.072          0.252        4  render/main_transparent_pass_3d/elapsed_cpu
         0.059          0.125        4  render/clustering/elapsed_cpu
         0.045          0.060        4  render/main_opaque_pass_3d/elapsed_gpu
         0.043          0.060      108  render/msaa_writeback/elapsed_gpu
         0.029          0.069       94  render/main_opaque_pass_2d/elapsed_cpu
         0.026          0.219      108  render/ui/elapsed_gpu
         0.024          0.025        4  render/early_mesh_preprocessing/elapsed_gpu
         0.024          0.061      108  render/ui/elapsed_cpu
         0.021          0.041      108  render/upscaling/elapsed_gpu
         0.020          0.020        4  render/bin_unpacking/elapsed_gpu
         0.011          0.030       94  render/main_opaque_pass_2d/elapsed_gpu
         0.010          0.012        4  render/main_transparent_pass_3d/elapsed_gpu
         0.009          0.013        4  render/bin_unpacking/elapsed_cpu
         0.006          0.008        4  render/early_mesh_preprocessing/elapsed_cpu
         0.004          0.006      108  render/main_transparent_pass_2d/elapsed_gpu
         0.004          0.014      108  render/msaa_writeback/elapsed_cpu
         0.003          0.009      108  render/upscaling/elapsed_cpu
         0.001          0.005      108  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    979696.750    1308597.000        4  render/main_opaque_pass_3d/fragment_shader_invocations
    969725.648    9615991.000      108  render/ui/fragment_shader_invocations
    360894.638    1327104.000       94  render/main_opaque_pass_2d/fragment_shader_invocations
     19159.000      25634.000        4  render/main_transparent_pass_3d/fragment_shader_invocations
       678.750        937.000        4  render/main_transparent_pass_3d/vertex_shader_invocations
       632.185       1336.000      108  render/ui/vertex_shader_invocations
       333.750        461.000        4  render/main_transparent_pass_3d/clipper_invocations
       333.250        459.000        4  render/main_transparent_pass_3d/clipper_primitives_out
       316.093        668.000      108  render/ui/clipper_primitives_out
       316.093        668.000      108  render/ui/clipper_invocations
       128.000        128.000        4  render/early_mesh_preprocessing/compute_shader_invocations
       108.000        144.000        4  render/main_opaque_pass_3d/vertex_shader_invocations
        64.000         64.000        4  render/bin_unpacking/compute_shader_invocations
        57.000         76.000        4  render/main_opaque_pass_3d/clipper_primitives_out
        54.000         72.000        4  render/main_opaque_pass_3d/clipper_invocations
         4.000          4.000       94  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       94  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       94  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      108  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      108  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      108  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      108  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (10 spans).
- GPU pass timings: **measured** (10 spans).
- Pipeline statistics: **measured** (22 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)


`perf` cannot produce this: a Bevy system is not a native symbol, and a
render pass is a graph node rather than a function. Counts matter as much
as totals -- a cheap zone entered ten thousand times is a scheduling
problem, not a slow function.

## Whole session, ranked by total time

```text
  total_ms     count   mean_us    max_us  worst  zone
  112942.9         1 112942856.2 112942856.2   100%  bevy_app
  112468.6         1 112468608.7 112468608.7   100%  render thread
  108916.5      4304   25305.9 1155385.2     1%  update
  103617.0      4304   24074.6 1154218.4     1%  main app
  103589.3      4304   24068.1 1154212.0     1%  schedule{name=Main}
   47252.7      4304   10978.8  162649.4     0%  schedule{name=PreUpdate}
   39310.8      4304    9133.5  160085.9     0%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   38475.9      5786    6649.8  159552.1     0%  ggrs{name="HandleRequests"}
   38386.5      5786    6634.4  159531.8     0%  schedule{name="AdvanceWorld"}
   38309.2      5786    6621.0  159484.9     0%  schedule{name=AdvanceWorld}
   38036.2      5786    6573.8  159283.7     0%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   37983.7      5786    6564.8  159271.6     0%  schedule{name=GgrsSchedule}
   28112.5      4304    6531.7 1141834.6     4%  schedule{name=Update}
   26772.4      4304    6220.3  250516.6     1%  sub app{name=RenderApp}
   26725.8      4304    6209.5  250507.4     1%  schedule{name=RenderRecovery}
   26522.5      4304    6162.3  250436.3     1%  system{name="bevy_render::run_render_schedule"}
   26390.3      4304    6131.6  250404.6     1%  schedule{name=Render}
   14190.3      4304    3297.0   39503.6     0%  schedule{name=PostUpdate}
   11409.0      4304    2650.8   22554.0     0%  system{name="bevy_render::renderer::render_system"}
   11394.4      4304    2647.4   22550.7     0%  main_render_schedule
   10564.5      4304    2454.6   22381.8     0%  schedule{name=RenderGraph}
    7394.7      4304    1718.1   21406.9     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    6844.5     17387     393.7   19502.8     0%  schedule{name=Core2d}
    5665.4      4304    1316.3   14051.9     0%  schedule{name=RunFixedMainLoop}
    5253.9      4304    1220.7  279648.2     5%  sub app{name=RenderExtractApp}
    4999.6   2718395       1.8    2967.1     0%  multithreaded executor
    4471.0      4304    1038.8   84598.3     2%  schedule{name=ExtractSchedule}
    3994.8      4304     928.2   13175.2     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    3930.8      6987     562.6   12396.3     0%  schedule{name=FixedMain}
    2854.7      6987     408.6   11230.3     0%  schedule{name=FixedPostUpdate}
    2443.9      4303     568.0    2643.5     0%  camera_schedule{camera="Camera 0 (485v0)"}
    2378.9      4304     552.7   14336.3     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2357.9      4304     547.9    7527.5     0%  schedule{name=Last}
    2088.5      8608     242.6    1275.4     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
    1807.6      5786     312.4    2028.0     0%  system{name="ambition_platformer2d_actor_monolith::features::ecs::actors::update::integrat
    1806.5      3271     552.3    4825.4     0%  system{name="ambition_render::rendering::actors::rigged::drive_rigged_presentations"}
    1719.0     17387      98.9   18816.6     1%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
    1581.5      5786     273.3    1104.7     0%  system{name="ambition_platformer2d_actor_monolith::features::ecs::actors::update::tick_act
    1528.2    254571       6.0     279.6     0%  par_for_each{query="(bevy_ecs::entity::Entity, &bevy_camera::visibility::InheritedVisibili
    1521.7      4303     353.6   19512.2     1%  camera_schedule{camera="Camera 9 (486v0)"}
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
  107761.1      25043.3      4304    6369.5  update
  102462.8      23811.9      4304    5781.3  main app
  102435.1      23805.5      4304    5775.5  schedule{name=Main}
   47090.0      10943.5      4304     923.6  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 4302 frames, summing to 25.74ms:

```text
   11.06 ms   43.0%  PreUpdate
    6.82 ms   26.5%  Update
    3.38 ms   13.1%  PostUpdate
    1.87 ms    7.3%  outside
    1.36 ms    5.3%  RunFixedMainLoop
    0.61 ms    2.4%  Last
    0.27 ms    1.0%  First
    0.23 ms    0.9%  StateTransition
    0.15 ms    0.6%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
   11.06    24.53     2.22   PreUpdate
    6.82    10.85     1.59   Update
    3.38     6.05     1.79   PostUpdate
    1.87     5.23     2.80   outside
    1.36     3.26     2.40   RunFixedMainLoop
    0.61     0.89     1.46   Last
    0.27     0.72     2.68   First
    0.23     0.49     2.18   StateTransition
    0.15     0.22     1.49   SpawnScene
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
  73.1%  the game itself
  26.2%  profiler (Tracy)
   0.5%  audio
   0.3%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead : 26.2%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.3%   (cargo and shell; NOT a compile)
the game itself           : 73.1%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 26% of sampled cycles.** Its symbol-resolution and
compression threads compete with the game for the same cores, so every frame
time, zone duration and plugin-build number here is inflated too.

Zone RATIOS remain usable — the instrumentation is uniform across systems.
Absolute per-frame costs are not. For an honest frame time, re-run:

```bash
scripts/profile_desktop.sh --no-tracy
```

which drops `--features profile` (and with it the per-system zones), and
compare its frame census against this one to size the gap.

## Where the native time went

```text
  79.8%  game binary + its Rust/C deps
  17.8%  kernel
   2.3%  GPU driver / graphics stack
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
     3.96%  Tracy Profiler   ambition_game_bin                 [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                           
     1.96%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     1.93%  ambition_game_b  ambition_game_bin                 [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     1.83%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     1.64%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                          
     1.36%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCsejQFhAU0ddf_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     1.23%  ambition_game_b  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     1.20%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20sort_un
     1.02%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::NormalizePath(char const*)                                                                                                    
     0.99%  ambition_game_b  ambition_game_bin                 [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCsi7WNBsjk6LE_18tracing_subscriber8registry7sharded9DataInnerE3getBU_     
     0.97%  Tracy Profiler   libc.so.6                         [.] __strlen_evex                                                                                                                        
     0.92%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_keyjN
     0.87%  Tracy Profiler   libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.77%  ambition_game_b  ambition_game_bin                 [.] mi_theap_umalloc                                                                                                                     
     0.71%  Tracy Profiler   [kernel.kallsyms]                 [k] do_sys_poll                                                                                                                          
     0.65%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::rpmalloc(unsigned long)                                                                                                       
     0.58%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy::QueueIte
     0.55%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsigned long, char 
     0.54%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.53%  Compute Task Po  ambition_game_bin                 [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     0.52%  ambition_game_b  ambition_game_bin                 [.] tracy::rpmalloc(unsigned long)                                                                                                       
     0.50%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                            
     0.49%  Tracy Profiler   ambition_game_bin                 [.] tracy::rpfree(void*)                                                                                                                 
     0.48%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::report_inlined_functions(unsigned long, tracy::function*, char const*, int (*)(void*, unsigned long, unsigned long, char const
     0.46%  Tracy Symbol Wo  libc.so.6                         [.] _dl_addr                                                                                                                             
     0.46%  ambition_game_b  ambition_game_bin                 [.] ___tracy_emit_zone_begin_alloc                                                                                                       
     0.46%  ambition_game_b  ambition_game_bin                 [.] _RNvXs4_NtNtCsi7WNBsjk6LE_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5layer
     0.44%  ambition_game_b  ambition_game_bin                 [.] _mi_theap_realloc_zero                                                                                                               
     0.44%  Tracy Sampling   ambition_game_bin                 [.] tracy::SysTraceWorker(void*)                                                                                                         
     0.42%  Compute Task Po  ambition_game_bin                 [.] tracy::rpmalloc(unsigned long)                                                                                                       
     0.41%  Compute Task Po  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.38%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtNtNtCsejQFhAU0ddf_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.37%  Tracy Symbol Wo  ambition_game_bin                 [.] tracy::backtrace_qsort(void*, unsigned long, unsigned long, int (*)(void const*, void const*)) [clone .localalias]                   
     0.37%  ambition_game_b  [kernel.kallsyms]                 [k] perf_adjust_freq_unthr_context                                                                                                       
     0.36%  ambition_game_b  ambition_game_bin                 [.] mi_free                                                                                                                              
```

## Assets and render resources

- Decoded images: 125 → 786 (368.8 MP, 1475.0 MB of decode work).
- Images resident at end: 373.
- **Resident image BYTES at end: 1475.0 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **341 images (174.9 MP)** at 20.3s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 5 GENERATED image(s) were allocated during gameplay (13.2 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 16 notable decode(s) (18.4 MP) landed AFTER the first `room-loaded` (frame 1032) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.0MP  f   1034  backgrounds/parallax_layers/cove_foreground.png
   1.0MP  f   1034  backgrounds/parallax_layers/cove_far_backplate.png
   1.0MP  f   1034  backgrounds/parallax_layers/cove_sky.png
   1.6MP  f   1034  backgrounds/parallax_layers/cove_near_background.png
   1.0MP  f   1034  backgrounds/parallax_layers/boss_far_backplate.png
   1.0MP  f   1034  backgrounds/parallax_layers/boss_foreground.png
   1.0MP  f   1034  backgrounds/parallax_layers/boss_sky.png
   1.0MP  f   1034  backgrounds/parallax_layers/lab_sky.png
```

⚠ 58 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **4 of 79 notable decodes landed during SETTLED play** (4.6 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
   1.6MP  at 19.742s  backgrounds/parallax_layers/cave_near_background.png
   1.0MP  at 19.718s  backgrounds/parallax_layers/cave_foreground.png
   1.0MP  at 19.718s  backgrounds/parallax_layers/cave_far_backplate.png
   1.0MP  at 19.742s  backgrounds/parallax_layers/cave_sky.png
```

Textures decoded more than once:

```text
   6x  <runtime-generated>
   3x  backgrounds/parallax_layers/hub_far_backplate.png
   3x  backgrounds/parallax_layers/hub_sky.png
   3x  backgrounds/parallax_layers/hub_near_background.png
   2x  backgrounds/parallax_layers/cove_foreground.png
   2x  backgrounds/parallax_layers/cove_far_backplate.png
   2x  backgrounds/parallax_layers/cove_sky.png
   2x  backgrounds/parallax_layers/cove_near_background.png
   2x  backgrounds/parallax_layers/primeval_sky.png
   2x  backgrounds/parallax_layers/primeval_far_backplate.png
   2x  backgrounds/parallax_layers/primeval_near_background.png
   2x  backgrounds/parallax_layers/hub_foreground.png
   2x  sprites/goblin_parts.png
   2x  sprites/imperfect_cellular_automaton_parts.png
   2x  sprites/bob_parts.png
   2x  sprites/noether_parts.png
   2x  sprites/flying_spaghetti_monster_boss_parts.png
   2x  sprites/gnu_ton_scholar_fighter_spritesheet.png
   2x  sprites/ramen_nujan_parts.png
   2x  sprites/gnu_ton_boss/giant_gnu_spritesheet.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1227996 bytes

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

