# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `b2c0bd4bdf7d` on `main` |
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
| kernel | `Linux toothbrush 6.8.0-138-generic #138-Ubuntu SMP PREEMPT_DYNAMIC Fri Jul 31 22:41:49 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
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
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.84", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **441.9s**.

## Frame time

13 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.5      61    13.7     8.0    17.5    75.6    204.0
      5.5     109     9.2     7.0    17.4    41.4     90.4
      9.5      62    16.2    15.2    25.6    36.2     56.8
     12.5      55    18.5    17.4    26.4    28.2     33.1
     10.5      59    17.0    16.9    22.6    25.1     26.9
     13.5      56    17.9    17.9    22.7    23.9     25.6
      4.5     139     7.2     6.8    10.3    13.4     23.5
      8.5      75    13.5    13.8    17.0    20.0     20.9
```

Full series: `frame_times.csv`.

10 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    2.401s     204.0 ms
    6.788s      90.4 ms
    2.477s      75.6 ms
    2.197s      75.2 ms
   15.952s      63.6 ms
   10.727s      56.8 ms
    6.697s      41.4 ms
    6.858s      39.7 ms
   10.645s      36.2 ms
   10.784s      36.1 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.5        4       3      1       0      1
      1.5        4       3      1       0      1
      2.5        4       3      1       0      1
      3.5        4       3      1       0      1
      4.5        4       3      1       0      1
      5.5        4       3      1       0      1
      6.5        4       3      1       0      1
      7.5        4       3      1       0      1
      8.5        4       3      1       0      1
      9.5        4       3      1       0      1
     10.5        4       3      1       0      1
     11.5        4       3      1       0      1
     12.5        4       3      1       0      1
     13.5        4       3      1       0      1
```

Peak world-rendering cameras: **1** at t=0.5s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
           other  Cube pause camera, Cube scrim display camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=0.5s.

```text
        t  rigs  active  budget
      0.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      1.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      2.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      3.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      4.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      5.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      6.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      7.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      8.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      9.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     10.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     11.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     12.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     13.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1879      130        1
      peak       8192        1879      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **252** (40 visible), text2d 35, per-view projections 7 at t=9.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3234** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.034          0.067       13  render/ui/elapsed_gpu
         0.020          0.037       13  render/ui/elapsed_cpu
         0.016          0.020       13  render/upscaling/elapsed_gpu
         0.003          0.005       13  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.003       13  render/upscaling/elapsed_cpu
         0.001          0.001       13  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    918353.462    4652834.000       13  render/ui/fragment_shader_invocations
       607.231        702.000       13  render/ui/vertex_shader_invocations
       302.615        350.000       13  render/ui/clipper_primitives_out
       302.615        350.000       13  render/ui/clipper_invocations
         0.000          0.000       13  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       13  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       13  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       13  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (3 spans).
- GPU pass timings: **measured** (3 spans).
- Pipeline statistics: **measured** (8 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)


`perf` cannot produce this: a Bevy system is not a native symbol, and a
render pass is a graph node rather than a function. Counts matter as much
as totals -- a cheap zone entered ten thousand times is a scheduling
problem, not a slow function.

## Whole session, ranked by total time

```text
  total_ms     count   mean_us    max_us  worst  zone
   14455.9         1 14455872.6 14455872.6   100%  bevy_app
   14426.0         1 14426038.0 14426038.0   100%  render thread
   14118.9      1214   11630.1  481080.4     3%  update
   12699.2      1214   10460.6  436651.6     3%  main app
   12693.6      1214   10456.0  436645.7     3%  schedule{name=Main}
    6200.3      1214    5107.3  179062.5     3%  sub app{name=RenderApp}
    6190.6      1214    5099.4  179054.1     3%  schedule{name=RenderRecovery}
    6145.4      1214    5062.1  178952.3     3%  system{name="bevy_render::run_render_schedule"}
    6115.7      1214    5037.6  178916.9     3%  schedule{name=Render}
    4180.5      1214    3443.6   63809.7     2%  schedule{name=PreUpdate}
    3701.9      1214    3049.3  149687.0     4%  schedule{name=Update}
    2653.8      1214    2186.0   62564.7     2%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
    2594.7       554    4683.5   62216.5     2%  ggrs{name="HandleRequests"}
    2586.6       554    4669.0   62200.4     2%  schedule{name="AdvanceWorld"}
    2580.8       554    4658.4   62171.0     2%  schedule{name=AdvanceWorld}
    2555.6       554    4612.9   62020.2     2%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
    2551.0       554    4604.7   62013.5     2%  schedule{name=GgrsSchedule}
    2359.4      1214    1943.5   15630.5     1%  schedule{name=PostUpdate}
    1805.7      1214    1487.4   13335.1     1%  system{name="bevy_render::renderer::render_system"}
    1802.4      1214    1484.7   13331.6     1%  main_render_schedule
    1536.0      1214    1265.2    8776.8     1%  schedule{name=RenderGraph}
    1411.6      1214    1162.7  190828.2    14%  sub app{name=RenderExtractApp}
    1332.5      1214    1097.6    5676.2     0%  system{name="bevy_render::view::window::prepare_windows"}
    1010.0      1214     831.9    5114.4     1%  system{name="bevy_core_pipeline::schedule::camera_driver"}
     937.0      3639     257.5    4339.7     0%  schedule{name=Core2d}
     877.8    540608       1.6    2558.9     0%  multithreaded executor
     782.4      1214     644.5    8813.1     1%  schedule{name=RunFixedMainLoop}
     761.9      1214     627.6   42304.2     6%  schedule{name=ExtractSchedule}
     501.9      2428     206.7     731.3     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
     451.4      1213     372.1    4349.6     1%  camera_schedule{camera="Camera 0 (375v0)"}
     419.8      1214     345.8    8507.3     2%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
     408.5       891     458.4    4498.3     1%  schedule{name=FixedMain}
     357.4      1214     294.4    2584.6     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
     343.2         1  343196.1  343196.1   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
     319.9      1198     267.1     994.7     0%  queue_submit{count=9}
     296.5      1213     244.5    2555.6     1%  camera_schedule{camera="Camera 9 (376v0)"}
     288.1         1  288108.8  288108.8   100%  plugin build{plugin="bevy_render::RenderPlugin"}
     282.8      1214     232.9    3977.5     1%  schedule{name=Last}
     278.2       891     312.3    3532.4     1%  schedule{name=FixedPostUpdate}
     273.3         4   68328.7  273304.9   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="audio/music/g
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
   13637.8      11243.0      1214    5329.3  update
   12262.6      10109.3      1214    4425.8  main app
   12257.0      10104.7      1214    4421.9  schedule{name=Main}
    6021.2       4963.9      1214    2210.9  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 1165 frames, summing to 11.24ms:

```text
    3.33 ms   29.6%  PreUpdate
    3.19 ms   28.4%  Update
    1.95 ms   17.4%  PostUpdate
    1.33 ms   11.8%  outside
    0.67 ms    5.9%  RunFixedMainLoop
    0.28 ms    2.5%  Last
    0.18 ms    1.6%  First
    0.18 ms    1.6%  StateTransition
    0.13 ms    1.2%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    3.33     9.67    -6.34   PreUpdate
    3.19     6.82    -3.63   Update
    1.95     4.64    -2.69   PostUpdate
    1.33     4.12    -2.79   outside
    0.67     1.85    -1.19   RunFixedMainLoop
    0.28     0.64    -0.36   Last
    0.18     0.59    -0.41   First
    0.18     0.50    -0.32   StateTransition
    0.13     0.27    -0.14   SpawnScene
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
  94.0%  profiler (Tracy)
   5.8%  the game itself
   0.1%  build launcher (cargo, shell)
   0.0%  audio
```

```text
profiler (Tracy) overhead : 94.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           :  5.8%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 94% of sampled cycles.** Its symbol-resolution and
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
  50.3%  game binary + its Rust/C deps
  49.3%  kernel
   0.3%  GPU driver / graphics stack
   0.0%  audio
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
    24.81%  Tracy Profiler   ambition_game_bin            [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                               
     9.75%  Tracy Profiler   [kernel.kallsyms]            [k] do_sys_poll                                                                                                                               
     5.81%  Tracy Profiler   [kernel.kallsyms]            [k] clear_bhb_loop                                                                                                                            
     4.58%  Tracy Profiler   [kernel.kallsyms]            [k] _copy_from_user                                                                                                                           
     4.52%  Tracy Profiler   libc.so.6                    [.] __poll                                                                                                                                    
     2.66%  Tracy Profiler   [kernel.kallsyms]            [k] __fdget                                                                                                                                   
     2.53%  Tracy Profiler   [kernel.kallsyms]            [k] do_poll.constprop.0                                                                                                                       
     2.49%  Tracy Profiler   [kernel.kallsyms]            [k] entry_SYSRETQ_unsafe_stack                                                                                                                
     2.21%  Tracy Profiler   libc.so.6                    [.] __GI___pthread_disable_asynccancel                                                                                                        
     1.94%  Tracy Profiler   [kernel.kallsyms]            [k] tcp_poll                                                                                                                                  
     1.84%  Tracy Profiler   libc.so.6                    [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                                         
     1.83%  Tracy Profiler   [kernel.kallsyms]            [k] do_syscall_64                                                                                                                             
     1.83%  Tracy Profiler   libc.so.6                    [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                                         
     1.48%  Tracy Profiler   [kernel.kallsyms]            [k] arch_exit_to_user_mode_prepare.isra.0                                                                                                     
     1.47%  Tracy Profiler   libc.so.6                    [.] __GI___pthread_enable_asynccancel                                                                                                         
     1.44%  Tracy Profiler   [kernel.kallsyms]            [k] fput                                                                                                                                      
     1.08%  Tracy Profiler   [kernel.kallsyms]            [k] __x64_sys_poll                                                                                                                            
     1.07%  Tracy Profiler   ambition_game_bin            [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                                
     0.89%  Tracy Profiler   [kernel.kallsyms]            [k] sock_poll                                                                                                                                 
     0.87%  Tracy Profiler   ambition_game_bin            [.] tracy::Profiler::DequeueSerial()                                                                                                          
     0.80%  Tracy Profiler   [kernel.kallsyms]            [k] entry_SYSCALL_64_after_hwframe                                                                                                            
     0.77%  Tracy Profiler   [kernel.kallsyms]            [k] entry_SYSCALL_64                                                                                                                          
     0.76%  Tracy Profiler   ambition_game_bin            [.] tracy::rpmalloc(unsigned long)                                                                                                            
     0.75%  Tracy Profiler   [kernel.kallsyms]            [k] its_return_thunk                                                                                                                          
     0.72%  Tracy Profiler   [kernel.kallsyms]            [k] syscall_return_via_sysret                                                                                                                 
     0.66%  Tracy Profiler   [kernel.kallsyms]            [k] check_stack_object                                                                                                                        
     0.63%  Tracy Profiler   [kernel.kallsyms]            [k] syscall_exit_to_user_mode                                                                                                                 
     0.62%  Tracy Profiler   [kernel.kallsyms]            [k] __check_object_size.part.0                                                                                                                
     0.59%  Tracy Profiler   [kernel.kallsyms]            [k] tcp_stream_memory_free                                                                                                                    
     0.59%  Tracy Profiler   ambition_game_bin            [.] tracy::Profiler::Worker()                                                                                                                 
     0.52%  Tracy Profiler   [kernel.kallsyms]            [k] x64_sys_call                                                                                                                              
     0.51%  Tracy Profiler   ambition_game_bin            [.] tracy::Socket::HasData()                                                                                                                  
     0.46%  Tracy Profiler   [kernel.kallsyms]            [k] poll_freewait                                                                                                                             
     0.45%  Tracy Profiler   [kernel.kallsyms]            [k] poll_select_set_timeout                                                                                                                   
     0.39%  Tracy Profiler   [kernel.kallsyms]            [k] fpregs_assert_state_consistent                                                                                                            
```

## Assets and render resources

- Decoded images: 0 → 273 (91.3 MP, 365.3 MB of decode work).
- Images resident at end: 264.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **142 images (86.3 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 19 decode(s) landed before the first `room-loaded` (70.5 MP) — boot. Not a gameplay hitch.

✔ No notable texture decoded while gameplay was live.

19 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1942972 bytes

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

