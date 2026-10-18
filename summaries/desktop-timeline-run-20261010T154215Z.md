# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `952e2bfafa4a` on `main` |
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
| kernel | `Linux toothbrush 6.8.0-142-generic #142-Ubuntu SMP PREEMPT_DYNAMIC Wed Sep  2 14:24:27 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
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
AdapterInfo { name: "llvmpipe (LLVM 20.1.2, 256 bits)", vendor: 65541, device: 0, device_type: Cpu, device_pci_bus_id: "", driver: "llvmpipe", driver_info: "Mesa 25.2.8-0ubuntu0.24.04.2 (LLVM 20.1.2)", backend: Vulkan, subgroup_min_size: 8, subgroup_max_size: 8, transient_saves_memory: false }
```

**SOFTWARE RENDERING — READ THIS BEFORE THE NUMBERS BELOW.**

This run had no GPU: every pixel was rasterized on the CPU. Expect the
bulk of samples in llvmpipe/lavapipe threads and in unsymbolized `[JIT]`
frames, which are the rasterizer's runtime-compiled shaders -- perf can
never attribute those to a pass, a material, or a draw call. Game-code
symbols in this profile describe only the few percent left over.

This is NOT a measurement of GPU rendering performance, and it must not
be reported as one. Check `host-environment.txt` for why no GPU adapter
was selected (missing ICD, `VK_*`/`WGPU_*` override, no DRM render node,
headless session).

## Session

Observed span of the game's own log: **971.9s**.

## Frame time

58 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     57.5       2   485.9   890.8   890.8   890.8    890.8
     56.4       7   183.0   106.2   707.1   707.1    707.1
     59.6       5   220.2   139.9   540.5   540.5    540.5
     16.9       8   148.3   141.9   445.5   445.5    445.5
     39.1       7   190.8   164.1   386.8   386.8    386.8
     47.8       7   149.1   102.8   334.5   334.5    334.5
     42.3       6   186.5   153.2   319.6   319.6    319.6
     24.4       8   124.4   103.1   297.4   297.4    297.4
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    4.796s     157.5 ms
    4.639s     151.6 ms
    4.959s     112.1 ms
    5.017s      57.6 ms
    6.365s      53.9 ms
    6.919s      53.2 ms
    5.996s      52.4 ms
    7.113s      52.3 ms
    7.290s      52.1 ms
    5.068s      51.5 ms
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
    1      155     True  central_hub_complex -> military_tower
    2       84     True  military_tower -> central_hub_complex
    3      143     True  central_hub_complex -> hall_of_characters
```

Frames over 33.4 ms AFTER the last transition was logged (t=49.395s): **0**.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

⛔ **THE SPIKE LOG HIT ITS 60-LINE CAP, so this count is a FLOOR, not a total.** Percentile summaries continued; the per-frame lines did not.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 601 images / 206.3 MP in total; 66 were notable enough to print.

```text
ordered by: frame
before central_hub_complex: 6 image(s), 24.1 MP
  unknown                  2    16.5 MP
  vanity-card              1     3.0 MP
  boss-sheet               1     2.0 MP
  character-sheet          1     1.4 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.4        4       3      1       0      1
      3.5        4       3      1       0      1
      5.5        4       3      1       0      1
      7.6        4       3      1       0      1
      9.6        4       3      1       0      1
     11.7        4       3      1       0      1
     13.7        4       3      1       0      1
     15.8        4       3      1       0      1
     18.0        4       3      1       0      1
     20.1        4       3      1       0      1
     22.2        4       3      1       0      1
     24.4        6       3      1       0      1
     26.5        4       3      1       0      1
     28.6        4       3      1       0      1
     30.7        4       3      1       0      1
     32.7        6       5      1       2      1
     34.8        6       5      1       2      1
     36.9        6       5      1       2      1
     39.1        6       5      1       2      1
     41.2        6       5      1       2      1
     43.4        4       3      1       0      1
     45.7        6       5      1       2      1
     47.8       10       9      1       6      1
     50.0       10       5      1       2      1
     52.1       10       7      1       4      1
     54.2       10       7      1       4      1
     56.4       10       4      1       0      1
     58.6       10       4      1       0      1
     60.6       10       4      1       0      1
     62.8       10       4      1       0      1
```

Peak world-rendering cameras: **1** at t=1.4s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
       offscreen  rigged impostor camera, rigged impostor unpremultiply camera
           other  Cube pause camera, Cube scrim display camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=1.4s.

```text
        t  rigs  active  budget
      1.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      5.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     13.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     22.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     26.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     30.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     34.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     39.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     43.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     47.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     52.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     56.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     60.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 576px) at t=46.8s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1556        0        0
       end      16384        2230      139        1
      peak      16384        2230      139        1
```

⚠ Entity count rose by 14336 over the session and was **still
climbing in the second half** (+8192 after t=31.7s). Growth that never falls across room
transitions is the shape of a lifecycle leak; check `runtime_census.csv`
against the room markers in `timeline.md`.

Peak sprites: **5434** (451 visible), text2d 35, per-view projections 12 at t=46.8s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3592** in 35 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
        69.282        362.982        6  render/main_opaque_pass_3d/elapsed_gpu
        64.931        385.740        6  render/main_transparent_pass_3d/elapsed_gpu
         9.351         16.633       58  render/msaa_writeback/elapsed_gpu
         2.615          9.541       58  render/ui/elapsed_gpu
         1.864          3.446       58  render/upscaling/elapsed_gpu
         1.546          2.770        6  render/clustering/elapsed_gpu
         0.586          1.533       37  render/main_opaque_pass_2d/elapsed_gpu
         0.243          0.407        6  render/early_mesh_preprocessing/elapsed_gpu
         0.222          1.274       58  render/main_transparent_pass_2d/elapsed_gpu
         0.097          0.124        6  render/bin_unpacking/elapsed_gpu
         0.032          0.068        6  render/clustering/elapsed_cpu
         0.022          0.026        6  render/main_opaque_pass_3d/elapsed_cpu
         0.020          0.050       58  render/ui/elapsed_cpu
         0.016          0.047       37  render/main_opaque_pass_2d/elapsed_cpu
         0.015          0.029        6  render/main_transparent_pass_3d/elapsed_cpu
         0.004          0.005        6  render/bin_unpacking/elapsed_cpu
         0.003          0.016       58  render/msaa_writeback/elapsed_cpu
         0.003          0.003        6  render/early_mesh_preprocessing/elapsed_cpu
         0.003          0.007       58  render/upscaling/elapsed_cpu
         0.001          0.003       58  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1512172.500    1716200.000        6  render/main_opaque_pass_3d/fragment_shader_invocations
   1096865.707    4751000.000       58  render/ui/fragment_shader_invocations
    163646.270     331776.000       37  render/main_opaque_pass_2d/fragment_shader_invocations
     21681.500      27791.000        6  render/main_transparent_pass_3d/fragment_shader_invocations
      1112.833       1381.000        6  render/main_transparent_pass_3d/vertex_shader_invocations
       632.552       1388.000       58  render/ui/vertex_shader_invocations
       550.167        683.000        6  render/main_transparent_pass_3d/clipper_invocations
       412.667        503.000        6  render/main_transparent_pass_3d/clipper_primitives_out
       315.207        692.000       58  render/ui/clipper_primitives_out
       315.207        692.000       58  render/ui/clipper_invocations
       148.000        164.000        6  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        6  render/early_mesh_preprocessing/compute_shader_invocations
        85.167         95.000        6  render/main_opaque_pass_3d/clipper_primitives_out
        74.000         82.000        6  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000        6  render/bin_unpacking/compute_shader_invocations
         4.000          4.000       37  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       37  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       37  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       58  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       58  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       58  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       58  render/main_transparent_pass_2d/clipper_primitives_out
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
   63826.9         1 63826896.2 63826896.2   100%  bevy_app
   63774.2         1 63774179.9 63774179.9   100%  render thread
   62359.7       656   95060.5  947585.0     2%  update
   60025.9       656   91502.9  882610.1     1%  sub app{name=RenderApp}
   60019.0       656   91492.3  882603.6     1%  schedule{name=RenderRecovery}
   59989.9       656   91448.0  882568.8     1%  system{name="bevy_render::run_render_schedule"}
   59970.6       656   91418.6  882534.4     1%  schedule{name=Render}
   57455.9       656   87585.2  854984.0     1%  system{name="bevy_render::renderer::render_system"}
   57453.9       656   87582.2  854982.1     1%  main_render_schedule
   37119.8       656   56585.0  823914.6     2%  main app
   37115.1       656   56577.9  823899.9     2%  schedule{name=Main}
   25231.7       656   38463.0  764813.0     3%  sub app{name=RenderExtractApp}
   20824.2       656   31744.3  322338.1     2%  schedule{name=PreUpdate}
   19357.3       656   29508.1  320587.4     2%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   18098.4      2736    6614.9  224852.1     1%  ggrs{name="HandleRequests"}
   18057.1      2736    6599.8  224598.2     1%  schedule{name="AdvanceWorld"}
   18018.9      2736    6585.9  224300.3     1%  schedule{name=AdvanceWorld}
   17857.2      2736    6526.8  223774.2     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   17824.5      2736    6514.8  223757.3     1%  schedule{name=GgrsSchedule}
    5364.2       656    8177.2  355888.6     7%  schedule{name=Update}
    4959.1       656    7559.6   37237.2     1%  schedule{name=RunFixedMainLoop}
    4604.5       656    7019.1   36725.4     1%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    4579.3      3839    1192.8   23333.9     1%  schedule{name=FixedMain}
    3070.4      3839     799.8   12049.4     0%  schedule{name=FixedPostUpdate}
    2952.1       656    4500.1   30575.1     1%  schedule{name=PostUpdate}
    1820.6    547617       3.3    8918.3     0%  multithreaded executor
    1154.2      2736     421.9    9989.6     1%  schedule{name=ReadInputs}
     938.8         1  938815.7  938815.7   100%  plugin cleanup
     936.9         1  936916.5  936916.5   100%  plugin cleanup{plugin="bevy_rich_text3d::Text3dPlugin"}
     931.8       656    1420.4   12076.5     1%  schedule{name=RenderGraph}
     825.5      3839     215.0    5520.3     1%  schedule{name=FixedFirst}
     763.1       656    1163.3   11816.6     2%  system{name="bevy_core_pipeline::schedule::camera_driver"}
     750.9       656    1144.7  109463.8    15%  schedule{name=ExtractSchedule}
     683.1       656    1041.3   12181.6     2%  schedule{name=Last}
     674.7      2501     269.8    2793.2     0%  schedule{name=Core2d}
     620.9      4496     138.1    4473.4     1%  system{name="bevy_transform::systems::mark_dirty_trees"}
     610.6        10   61057.2  610366.7   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="audio/music/g
     552.8      3839     144.0   19084.5     3%  schedule{name=FixedLast}
     458.8         1  458804.0  458804.0   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
     455.2      2736     166.4    4152.0     1%  system{name="ambition_platformer2d_actor_monolith::features::ecs::actors::update::integrat
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
   61412.1      93758.9       656   42467.3  update
   59143.3      90295.1       656    4493.9  sub app{name=RenderApp}
   59136.4      90284.5       656    4482.8  schedule{name=RenderRecovery}
   59107.3      90240.2       656    4452.1  system{name="bevy_render::run_render_schedule"}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 650 frames, summing to 94.47ms:

```text
   38.96 ms   41.2%  outside
   31.66 ms   33.5%  PreUpdate
    8.88 ms    9.4%  Update
    7.67 ms    8.1%  RunFixedMainLoop
    4.83 ms    5.1%  PostUpdate
    1.37 ms    1.5%  Last
    0.48 ms    0.5%  StateTransition
    0.37 ms    0.4%  SpawnScene
    0.26 ms    0.3%  First
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
   38.96   404.32    10.38   outside
   31.66   364.75    11.52   PreUpdate
    8.88    97.32    10.96   Update
    7.67    91.26    11.90   RunFixedMainLoop
    4.83    54.62    11.32   PostUpdate
    1.37    15.43    11.24   Last
    0.48     6.02    12.66   StateTransition
    0.37     4.76    12.74   SpawnScene
    0.26     1.19     4.63   First
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
  58.6%  profiler (Tracy)
  41.4%  the game itself
   0.0%  build launcher (cargo, shell)
   0.0%  audio
```

```text
profiler (Tracy) overhead : 58.6%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           : 41.4%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 59% of sampled cycles.** Its symbol-resolution and
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
  42.4%  game binary + its Rust/C deps
  34.9%  software rasterizer (CPU emulating a GPU)
  22.6%  kernel
   0.1%  GPU driver / graphics stack
   0.0%  audio
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
    20.67%  Tracy Profiler   libc.so.6                      [.] _dl_addr                                                                                                                                
     8.39%  Tracy Profiler   ambition_game_bin              [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                             
     2.85%  Tracy Profiler   [kernel.kallsyms]              [k] do_sys_poll                                                                                                                             
     2.81%  Tracy Symbol Wo  libc.so.6                      [.] _dl_addr                                                                                                                                
     1.78%  Tracy Profiler   [kernel.kallsyms]              [k] clear_bhb_loop                                                                                                                          
     1.61%  Tracy Profiler   [kernel.kallsyms]              [k] _copy_from_user                                                                                                                         
     1.44%  Tracy Profiler   libc.so.6                      [.] __poll                                                                                                                                  
     1.35%  Tracy Profiler   [kernel.kallsyms]              [k] __fdget                                                                                                                                 
     0.89%  Tracy Profiler   [kernel.kallsyms]              [k] do_poll.constprop.0                                                                                                                     
     0.88%  Tracy Profiler   [kernel.kallsyms]              [k] entry_SYSRETQ_unsafe_stack                                                                                                              
     0.80%  Tracy Profiler   libc.so.6                      [.] __GI___pthread_disable_asynccancel                                                                                                      
     0.63%  Tracy Profiler   libc.so.6                      [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                                       
     0.61%  Tracy Profiler   libc.so.6                      [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                                       
     0.61%  Tracy Profiler   [kernel.kallsyms]              [k] tcp_poll                                                                                                                                
     0.59%  Tracy Profiler   [kernel.kallsyms]              [k] do_syscall_64                                                                                                                           
     0.52%  Tracy Profiler   libc.so.6                      [.] __GI___pthread_enable_asynccancel                                                                                                       
     0.52%  Tracy Profiler   ambition_game_bin              [.] tracy::Profiler::DequeueSerial()                                                                                                        
     0.49%  Tracy Profiler   [kernel.kallsyms]              [k] fput                                                                                                                                    
     0.46%  Tracy Profiler   ambition_game_bin              [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                              
     0.42%  Tracy Profiler   [kernel.kallsyms]              [k] arch_exit_to_user_mode_prepare.isra.0                                                                                                   
     0.35%  llvmpipe-2       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.34%  llvmpipe-5       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.34%  llvmpipe-1       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.34%  llvmpipe-3       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.33%  llvmpipe-0       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.32%  llvmpipe-4       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.31%  Tracy Profiler   [kernel.kallsyms]              [k] __x64_sys_poll                                                                                                                          
     0.31%  llvmpipe-7       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.30%  llvmpipe-6       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.29%  Tracy Profiler   [kernel.kallsyms]              [k] entry_SYSCALL_64                                                                                                                        
     0.29%  llvmpipe-8       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.28%  Tracy Profiler   [kernel.kallsyms]              [k] check_stack_object                                                                                                                      
     0.27%  llvmpipe-9       [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
     0.25%  llvmpipe-10      [JIT] tid 3438052              [.] 0x000077d4c801c125                                                                                                                      
```

## Assets and render resources

- Decoded images: 95 → 601 (206.3 MP, 825.2 MB of decode work).
- Images resident at end: 318.
- **Resident image BYTES at end: 825.2 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **258 images (113.7 MP)** at 40.3s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 3 GENERATED image(s) were allocated during gameplay (10.6 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 16 notable decode(s) (18.4 MP) landed AFTER the first `room-loaded` (frame 261) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.0MP  f    263  backgrounds/parallax_layers/cove_foreground.png
   1.0MP  f    263  backgrounds/parallax_layers/cove_far_backplate.png
   1.0MP  f    263  backgrounds/parallax_layers/cove_sky.png
   1.0MP  f    263  backgrounds/parallax_layers/boss_foreground.png
   1.6MP  f    263  backgrounds/parallax_layers/cove_near_background.png
   1.0MP  f    263  backgrounds/parallax_layers/boss_far_backplate.png
   1.0MP  f    263  backgrounds/parallax_layers/boss_sky.png
   1.6MP  f    263  backgrounds/parallax_layers/boss_near_background.png
```

⚠ 42 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **15 of 66 notable decodes landed during SETTLED play** (57.8 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  18.0MP  at 39.846s  sprites/gnu_ton_boss/giant_gnu_spritesheet.png
  18.0MP  at 40.037s  sprites/gnu_ton_boss/gnu_ton_boss_spritesheet.png
   8.0MP  at 39.846s  sprites/perfect_cellular_automaton_parts.png
   1.6MP  at 19.421s  backgrounds/parallax_layers/skybridge_near_background.png
   1.6MP  at 21.425s  backgrounds/parallax_layers/eclipse_near_background.png
   1.6MP  at 42.489s  backgrounds/parallax_layers/alarm_near_background.png
   1.0MP  at 19.421s  backgrounds/parallax_layers/skybridge_foreground.png
   1.0MP  at 19.421s  backgrounds/parallax_layers/skybridge_sky.png
   1.0MP  at 19.421s  backgrounds/parallax_layers/skybridge_far_backplate.png
   1.0MP  at 21.422s  backgrounds/parallax_layers/eclipse_foreground.png
```

Textures decoded more than once:

```text
   4x  <runtime-generated>
   2x  backgrounds/parallax_layers/cove_foreground.png
   2x  backgrounds/parallax_layers/cove_far_backplate.png
   2x  backgrounds/parallax_layers/cove_sky.png
   2x  backgrounds/parallax_layers/boss_foreground.png
   2x  backgrounds/parallax_layers/cove_near_background.png
   2x  backgrounds/parallax_layers/boss_far_backplate.png
   2x  backgrounds/parallax_layers/boss_sky.png
   2x  backgrounds/parallax_layers/boss_near_background.png
   2x  backgrounds/parallax_layers/lab_sky.png
   2x  backgrounds/parallax_layers/basement_foreground.png
   2x  backgrounds/parallax_layers/lab_foreground.png
   2x  backgrounds/parallax_layers/lab_near_background.png
   2x  backgrounds/parallax_layers/basement_sky.png
   2x  backgrounds/parallax_layers/lab_far_backplate.png
   2x  backgrounds/parallax_layers/basement_far_backplate.png
   2x  backgrounds/parallax_layers/basement_near_background.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 5978668 bytes

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

