# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `bc7057067c69` on `main` |
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

Observed span of the game's own log: **214.2s**.

## Frame time

21 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     12.0       8   124.7    41.4   637.2   637.2    637.2
     10.9      30    38.0    23.5   106.5   319.6    319.6
      1.7      20    34.5     7.1   202.7   307.5    307.5
      8.8      19    55.5    24.7   164.4   288.2    288.2
      5.7      96    10.5     7.4    23.4    37.9    170.7
      7.7      64    16.0    10.5    43.8    54.1     74.5
      9.8      48    21.7    19.8    48.3    60.6     60.6
     19.0      63    16.0    15.7    20.7    21.7     34.8
```

Full series: `frame_times.csv`.

36 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   13.359s     637.3 ms
   12.721s     319.6 ms
    3.256s     307.5 ms
   10.058s     288.3 ms
    2.948s     203.2 ms
   13.539s     180.2 ms
    7.046s     170.6 ms
    9.769s     164.3 ms
   10.171s     113.4 ms
   12.402s     106.5 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.7        4       3      1       0      1
      1.7        4       3      1       0      1
      2.7        4       3      1       0      1
      3.7        4       3      1       0      1
      4.7        4       3      1       0      1
      5.7        4       3      1       0      1
      6.7        4       3      1       0      1
      7.7        4       3      1       0      1
      8.8        4       3      1       0      1
      9.8        4       3      1       0      1
     10.9        4       3      1       0      1
     12.0        4       3      1       0      1
     13.0        4       3      1       0      1
     14.0        4       3      1       0      1
     15.0        4       3      1       0      1
     16.0        4       3      1       0      1
     17.0        4       3      1       0      1
     18.0        4       3      1       0      1
     19.0        4       3      1       0      1
     20.0        4       3      1       0      1
     21.1        4       4      1       0      1
     22.1        4       4      1       0      1
```

Peak world-rendering cameras: **1** at t=0.7s.

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

Peak active portal capture rigs: **0** of 0 at t=0.7s.

```text
        t  rigs  active  budget
      0.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      1.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      2.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      3.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      4.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      5.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      6.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      7.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      8.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     13.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     14.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     15.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     16.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     17.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     19.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     20.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     21.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     22.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.7s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1959      130        1
      peak       8192        1959      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **253** (52 visible), text2d 160, per-view projections 36 at t=15.0s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3234** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.136          0.139        2  render/clustering/elapsed_gpu
         0.060          0.063        2  render/main_opaque_pass_3d/elapsed_gpu
         0.052          0.329       21  render/ui/elapsed_gpu
         0.044          0.062       21  render/msaa_writeback/elapsed_gpu
         0.024          0.025        2  render/clustering/elapsed_cpu
         0.024          0.024        2  render/early_mesh_preprocessing/elapsed_gpu
         0.023          0.042       21  render/ui/elapsed_cpu
         0.023          0.026        2  render/main_opaque_pass_3d/elapsed_cpu
         0.022          0.029       21  render/upscaling/elapsed_gpu
         0.019          0.019        2  render/bin_unpacking/elapsed_gpu
         0.011          0.011        2  render/main_transparent_pass_3d/elapsed_gpu
         0.009          0.011        2  render/main_transparent_pass_3d/elapsed_cpu
         0.009          0.011        2  render/bin_unpacking/elapsed_cpu
         0.007          0.011        2  render/early_mesh_preprocessing/elapsed_cpu
         0.004          0.005       21  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.008       21  render/msaa_writeback/elapsed_cpu
         0.002          0.006       21  render/upscaling/elapsed_cpu
         0.001          0.003       21  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1389567.500    1429201.000        2  render/main_opaque_pass_3d/fragment_shader_invocations
    483529.190    2636227.000       21  render/ui/fragment_shader_invocations
     22169.000      26681.000        2  render/main_transparent_pass_3d/fragment_shader_invocations
      1102.000       1329.000        2  render/main_transparent_pass_3d/vertex_shader_invocations
       605.810        702.000       21  render/ui/vertex_shader_invocations
       547.000        657.000        2  render/main_transparent_pass_3d/clipper_invocations
       377.000        461.000        2  render/main_transparent_pass_3d/clipper_primitives_out
       301.905        350.000       21  render/ui/clipper_primitives_out
       301.905        350.000       21  render/ui/clipper_invocations
       144.000        164.000        2  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        2  render/early_mesh_preprocessing/compute_shader_invocations
        75.000         86.000        2  render/main_opaque_pass_3d/clipper_primitives_out
        72.000         82.000        2  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000        2  render/bin_unpacking/compute_shader_invocations
         0.000          0.000       21  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       21  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       21  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       21  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (9 spans).
- GPU pass timings: **measured** (9 spans).
- Pipeline statistics: **measured** (18 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)


`perf` cannot produce this: a Bevy system is not a native symbol, and a
render pass is a graph node rather than a function. Counts matter as much
as totals -- a cheap zone entered ten thousand times is a scheduling
problem, not a slow function.

## Whole session, ranked by total time

```text
  total_ms     count   mean_us    max_us  worst  zone
   22125.0         1 22125002.8 22125002.8   100%  bevy_app
   22091.7         1 22091661.9 22091661.9   100%  render thread
   21640.1      1479   14631.6  893631.0     4%  update
   17853.2      1479   12071.1  719115.0     4%  main app
   17846.5      1479   12066.6  719107.9     4%  schedule{name=Main}
    8778.5      1479    5935.5  204031.7     2%  sub app{name=RenderApp}
    8767.2      1479    5927.8  204021.9     2%  schedule{name=RenderRecovery}
    8713.3      1479    5891.3  203930.8     2%  system{name="bevy_render::run_render_schedule"}
    8675.7      1479    5865.9  203896.2     2%  schedule{name=Render}
    6886.9      1479    4656.4  146262.9     2%  schedule{name=PreUpdate}
    4946.4      1479    3344.5  145212.2     3%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
    4860.6      1479    3286.4  274008.9     6%  schedule{name=Update}
    4845.2       990    4894.1  142213.3     3%  ggrs{name="HandleRequests"}
    4831.1       990    4879.9  142203.3     3%  schedule{name="AdvanceWorld"}
    4820.5       990    4869.2  142182.1     3%  schedule{name=AdvanceWorld}
    4777.2       990    4825.5  141983.3     3%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
    4768.4       990    4816.6  141977.5     3%  schedule{name=GgrsSchedule}
    3777.1      1479    2553.8  534407.5    14%  sub app{name=RenderExtractApp}
    2975.5      1479    2011.8  534256.9    18%  schedule{name=ExtractSchedule}
    2955.3      1479    1998.1   27091.9     1%  schedule{name=PostUpdate}
    2655.6      1479    1795.5    9207.9     0%  system{name="bevy_render::renderer::render_system"}
    2651.5      1479    1792.8    9204.3     0%  main_render_schedule
    2398.0      1479    1621.4    8859.9     0%  schedule{name=RenderGraph}
    2098.4      1479    1418.8  533416.7    25%  system{name="bevy_render::render_asset::extract_render_asset<bevy_render::texture::gpu_ima
    1660.2      1479    1122.5    5113.5     0%  system{name="bevy_framepace::framerate_limiter"}
    1583.3      1479    1070.5    7410.4     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    1396.3      4434     314.9    2788.8     0%  schedule{name=Core2d}
    1076.6    674823       1.6     260.2     0%  multithreaded executor
    1031.8      1479     697.7    7657.0     1%  schedule{name=RunFixedMainLoop}
     665.0         4  166241.4  664946.5   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="audio/music/g
     602.8      1478     407.9    2198.6     0%  camera_schedule{camera="Camera 0 (375v0)"}
     602.6      2958     203.7     700.1     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
     601.0      1479     406.4    6957.4     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
     593.8      1479     401.5    6947.2     1%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
     579.3      1320     438.9    6923.8     1%  schedule{name=FixedMain}
     562.9         1  562911.0  562911.0   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
     510.8      1479     345.4   83470.6    16%  system{name="bevy_render::render_asset::prepare_assets<bevy_render::texture::gpu_image::Gp
     494.4      1372     360.4    6922.4     1%  queue_submit{count=11}
     455.6      1478     308.3    2803.7     1%  camera_schedule{camera="Camera 9 (376v0)"}
     404.3      1320     306.3    6430.3     2%  schedule{name=FixedPostUpdate}
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
   20746.5      14036.8      1479    5059.5  update
   17134.1      11592.8      1479    4472.9  main app
   17127.4      11588.2      1479    4470.1  schedule{name=Main}
    8574.5       5801.4      1479    2365.9  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 1477 frames, summing to 14.49ms:

```text
    4.71 ms   32.5%  PreUpdate
    3.49 ms   24.1%  Update
    2.72 ms   18.8%  outside
    2.04 ms   14.1%  PostUpdate
    0.73 ms    5.0%  RunFixedMainLoop
    0.27 ms    1.9%  Last
    0.22 ms    1.5%  First
    0.18 ms    1.2%  StateTransition
    0.12 ms    0.9%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    4.71    14.34    -9.63   PreUpdate
    3.49     7.64    -4.15   Update
    2.72     7.63    -4.90   outside
    2.04     4.31    -2.27   PostUpdate
    0.73     2.11    -1.38   RunFixedMainLoop
    0.27     0.49    -0.21   Last
    0.22     0.74    -0.53   First
    0.18     0.55    -0.38   StateTransition
    0.12     0.27    -0.15   SpawnScene
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
  81.8%  profiler (Tracy)
  17.9%  the game itself
   0.1%  build launcher (cargo, shell)
   0.1%  audio
```

```text
profiler (Tracy) overhead : 81.8%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           : 17.9%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 82% of sampled cycles.** Its symbol-resolution and
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
  55.1%  game binary + its Rust/C deps
  44.2%  kernel
   0.6%  GPU driver / graphics stack
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
    21.66%  Tracy Profiler   ambition_game_bin            [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                               
     7.01%  Tracy Profiler   [kernel.kallsyms]            [k] do_sys_poll                                                                                                                               
     4.48%  Tracy Profiler   [kernel.kallsyms]            [k] clear_bhb_loop                                                                                                                            
     4.06%  Tracy Profiler   [kernel.kallsyms]            [k] _copy_from_user                                                                                                                           
     3.69%  Tracy Profiler   libc.so.6                    [.] __poll                                                                                                                                    
     2.30%  Tracy Profiler   [kernel.kallsyms]            [k] __fdget                                                                                                                                   
     2.26%  Tracy Profiler   [kernel.kallsyms]            [k] entry_SYSRETQ_unsafe_stack                                                                                                                
     2.12%  Tracy Profiler   [kernel.kallsyms]            [k] do_poll.constprop.0                                                                                                                       
     1.99%  Tracy Profiler   libc.so.6                    [.] __GI___pthread_disable_asynccancel                                                                                                        
     1.57%  Tracy Profiler   [kernel.kallsyms]            [k] do_syscall_64                                                                                                                             
     1.55%  Tracy Profiler   libc.so.6                    [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                                         
     1.55%  Tracy Profiler   ambition_game_bin            [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                                
     1.50%  Tracy Profiler   libc.so.6                    [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                                         
     1.49%  Tracy Profiler   [kernel.kallsyms]            [k] tcp_poll                                                                                                                                  
     1.32%  Tracy Profiler   libc.so.6                    [.] __GI___pthread_enable_asynccancel                                                                                                         
     1.30%  Tracy Profiler   [kernel.kallsyms]            [k] fput                                                                                                                                      
     1.02%  Tracy Profiler   [kernel.kallsyms]            [k] arch_exit_to_user_mode_prepare.isra.0                                                                                                     
     0.82%  Tracy Profiler   [kernel.kallsyms]            [k] __x64_sys_poll                                                                                                                            
     0.72%  Tracy Profiler   ambition_game_bin            [.] tracy::Profiler::DequeueSerial()                                                                                                          
     0.70%  Tracy Profiler   [kernel.kallsyms]            [k] check_stack_object                                                                                                                        
     0.68%  Tracy Profiler   [kernel.kallsyms]            [k] its_return_thunk                                                                                                                          
     0.65%  Tracy Profiler   [kernel.kallsyms]            [k] entry_SYSCALL_64_after_hwframe                                                                                                            
     0.62%  Tracy Profiler   ambition_game_bin            [.] tracy::rpmalloc(unsigned long)                                                                                                            
     0.61%  Tracy Profiler   ambition_game_bin            [.] tracy::NormalizePath(char const*)                                                                                                         
     0.57%  Tracy Profiler   [kernel.kallsyms]            [k] syscall_exit_to_user_mode                                                                                                                 
     0.56%  Tracy Profiler   [kernel.kallsyms]            [k] sock_poll                                                                                                                                 
     0.54%  Tracy Profiler   [kernel.kallsyms]            [k] entry_SYSCALL_64                                                                                                                          
     0.51%  Tracy Profiler   ambition_game_bin            [.] tracy::Profiler::Worker()                                                                                                                 
     0.50%  Tracy Profiler   [kernel.kallsyms]            [k] __check_object_size.part.0                                                                                                                
     0.48%  Tracy Profiler   [kernel.kallsyms]            [k] tcp_stream_memory_free                                                                                                                    
     0.45%  Tracy Profiler   libc.so.6                    [.] __strlen_evex                                                                                                                             
     0.42%  Tracy Profiler   [kernel.kallsyms]            [k] poll_freewait                                                                                                                             
     0.42%  Tracy Profiler   [kernel.kallsyms]            [k] syscall_return_via_sysret                                                                                                                 
     0.42%  Compute Task Po  libc.so.6                    [.] __memmove_evex_unaligned_erms                                                                                                             
     0.40%  Tracy Profiler   ambition_game_bin            [.] tracy::Socket::HasData()                                                                                                                  
```

## Assets and render resources

- Decoded images: 0 → 270 (534.7 MP, 2138.8 MB of decode work).
- Images resident at end: 268.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **130 images (89.6 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 54 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **18 of 93 notable decodes landed during SETTLED play** (145.7 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  16.8MP  at 11.004s  sprites/perfect_cellular_automaton_spritesheet.1.png
  16.7MP  at 10.682s  sprites/perfect_cellular_automaton_spritesheet.2.png
  16.7MP  at 11.004s  sprites/perfect_cellular_automaton_spritesheet.5.png
  16.5MP  at 11.004s  sprites/perfect_cellular_automaton_spritesheet.3.png
  15.9MP  at 10.682s  sprites/perfect_cellular_automaton_spritesheet.4.png
  15.7MP  at 11.004s  sprites/perfect_cellular_automaton_spritesheet.png
   9.4MP  at 10.575s  sprites/perfect_cellular_automaton_spritesheet.6.png
   7.2MP  at 11.639s  sprites/patent_clerk_spritesheet.png
   6.5MP  at 11.004s  sprites/player_robot_v2_spritesheet.png
   5.7MP  at 11.004s  sprites/robot_spritesheet.png
```

93 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1121936 bytes

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

