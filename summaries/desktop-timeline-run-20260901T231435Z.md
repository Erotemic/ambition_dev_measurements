# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `62e50c553e6a` on `main` |
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

Observed span of the game's own log: **386.7s**.

## Frame time

34 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     25.6       3   328.1   287.1   542.6   542.6    542.6
     29.8       9   134.3    51.8   535.9   535.9    535.9
      2.3       3   227.4   201.4   384.4   384.4    384.4
     24.5      11   106.7   105.5   201.0   201.0    201.0
     26.6      15    61.5    51.5   115.5   138.4    138.4
     30.8      39    23.4    18.4    47.2   132.8    132.8
     23.5      28    38.4    32.5   118.3   132.2    132.2
     28.6      34    30.2    23.5    51.4   122.7    122.7
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   27.325s     542.6 ms
    3.974s     384.3 ms
   26.782s     287.4 ms
    3.590s     201.9 ms
   26.259s     200.9 ms
   26.495s     191.5 ms
   27.480s     154.6 ms
   28.207s     138.4 ms
   24.604s     132.1 ms
   25.535s     129.1 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.2        4       3      1       0      1
      2.3        4       3      1       0      1
      3.3        4       3      1       0      1
      4.3        4       3      1       0      1
      5.3        4       3      1       0      1
      6.3        4       3      1       0      1
      7.3        4       3      1       0      1
      8.3        4       3      1       0      1
      9.3        4       4      1       0      1
     10.3        4       4      1       0      1
     11.3        4       4      1       0      1
     12.4        4       4      1       0      1
     13.4        4       4      1       0      1
     14.4        4       4      1       0      1
     15.4        4       4      1       0      1
     16.4        4       4      1       0      1
     17.4        4       4      1       0      1
     18.4        4       4      1       0      1
     19.4        4       4      1       0      1
     20.4        4       4      1       0      1
     21.4        4       3      1       0      1
     22.4        4       3      1       0      1
     23.5        4       3      1       0      1
     24.5        4       3      1       0      1
     25.6        4       3      1       0      1
     26.6        4       3      1       0      1
     27.6        4       3      1       0      1
     28.6        4       4      1       0      1
     29.8        4       3      1       0      1
     30.8        4       3      1       0      1
     31.8        4       3      1       0      1
     32.8        4       3      1       0      1
     33.8        4       3      1       0      1
     34.8        4       3      1       0      1
     35.8        4       3      1       0      1
```

Peak world-rendering cameras: **1** at t=1.2s.

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

Peak active portal capture rigs: **0** of 0 at t=1.2s.

```text
        t  rigs  active  budget
      1.2     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      3.3     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      5.3     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      7.3     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      9.3     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     11.3     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     13.4     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     15.4     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     17.4     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     19.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     21.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     23.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     25.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     27.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     29.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     31.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     33.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     35.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=1.2s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1973        2        1
      peak       8192        1973      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **243** (50 visible), text2d 100, per-view projections 24 at t=29.8s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3234** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.088          0.111       27  render/clustering/elapsed_gpu
         0.046          0.077       27  render/main_opaque_pass_3d/elapsed_cpu
         0.037          0.063       27  render/clustering/elapsed_cpu
         0.028          0.068       34  render/ui/elapsed_cpu
         0.023          0.061       34  render/ui/elapsed_gpu
         0.022          0.027       17  render/msaa_writeback/elapsed_gpu
         0.019          0.042       27  render/main_opaque_pass_3d/elapsed_gpu
         0.017          0.022       27  render/early_mesh_preprocessing/elapsed_gpu
         0.016          0.031       27  render/main_transparent_pass_3d/elapsed_cpu
         0.015          0.017       27  render/bin_unpacking/elapsed_gpu
         0.013          0.048       33  render/upscaling/elapsed_gpu
         0.007          0.009       27  render/main_transparent_pass_3d/elapsed_gpu
         0.007          0.010       27  render/bin_unpacking/elapsed_cpu
         0.006          0.012       17  render/msaa_writeback/elapsed_cpu
         0.005          0.009       27  render/early_mesh_preprocessing/elapsed_cpu
         0.003          0.004       34  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.008       33  render/upscaling/elapsed_cpu
         0.002          0.022       34  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    739958.407    1546184.000       27  render/main_opaque_pass_3d/fragment_shader_invocations
    433405.294    3924644.000       34  render/ui/fragment_shader_invocations
     15761.630      39834.000       27  render/main_transparent_pass_3d/fragment_shader_invocations
       910.556       1430.000       27  render/main_transparent_pass_3d/vertex_shader_invocations
       559.118       1650.000       34  render/ui/vertex_shader_invocations
       451.519        708.000       27  render/main_transparent_pass_3d/clipper_invocations
       447.370        708.000       27  render/main_transparent_pass_3d/clipper_primitives_out
       278.588        824.000       34  render/ui/clipper_primitives_out
       278.588        824.000       34  render/ui/clipper_invocations
       166.222        256.000       27  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000       27  render/early_mesh_preprocessing/compute_shader_invocations
        86.556        132.000       27  render/main_opaque_pass_3d/clipper_primitives_out
        83.111        128.000       27  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000       27  render/bin_unpacking/compute_shader_invocations
         0.000          0.000       34  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       34  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       34  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       34  render/main_transparent_pass_2d/clipper_primitives_out
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
   36553.6         1 36553641.3 36553641.3   100%  bevy_app
   36515.5         1 36515480.6 36515480.6   100%  render thread
   35352.5      1936   18260.6  955772.3     3%  update
   30756.9      1936   15886.8  821967.0     3%  main app
   30744.9      1936   15880.6  821959.5     3%  schedule{name=Main}
   12367.4      1936    6388.1  195307.0     2%  sub app{name=RenderApp}
   12347.2      1936    6377.7  195294.2     2%  schedule{name=RenderRecovery}
   12250.6      1936    6327.8  195203.1     2%  system{name="bevy_render::run_render_schedule"}
   12199.4      1936    6301.3  165659.4     1%  schedule{name=PreUpdate}
   12190.5      1936    6296.8  195080.6     2%  schedule{name=Render}
    8925.6      1936    4610.3  163122.5     2%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
    8687.1      1745    4978.3  100903.2     1%  ggrs{name="HandleRequests"}
    8655.5      1745    4960.1  100881.6     1%  schedule{name="AdvanceWorld"}
    8628.1      1745    4944.5  100864.5     1%  schedule{name=AdvanceWorld}
    8529.1      1745    4887.8  100771.9     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
    8511.2      1745    4877.5  100759.9     1%  schedule{name=GgrsSchedule}
    8011.8      1936    4138.3  346403.1     4%  schedule{name=Update}
    5141.7      1936    2655.8   14944.0     0%  system{name="bevy_render::renderer::render_system"}
    5134.9      1936    2652.3   14940.6     0%  main_render_schedule
    5134.5      1936    2652.1   38734.1     1%  schedule{name=PostUpdate}
    4681.4      1936    2418.1   12822.1     0%  schedule{name=RenderGraph}
    4579.2      1936    2365.3  432650.5     9%  sub app{name=RenderExtractApp}
    4109.0      1936    2122.4  432554.2    11%  schedule{name=ExtractSchedule}
    3238.3      1936    1672.7    9863.9     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    2656.6      1936    1372.2  431631.6    16%  system{name="bevy_render::render_asset::extract_render_asset<bevy_render::texture::gpu_ima
    2261.7      5805     389.6    4580.5     0%  schedule{name=Core2d}
    1978.8      1936    1022.1   16418.3     1%  schedule{name=RunFixedMainLoop}
    1890.3    949654       2.0    4593.8     0%  multithreaded executor
    1253.8      1936     647.6   15749.7     1%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    1227.0      2187     561.0   12585.9     1%  schedule{name=FixedMain}
    1054.4      1936     544.6    6164.0     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
     982.1      3872     253.6    2900.4     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
     978.2      1935     505.5    4589.6     0%  camera_schedule{camera="Camera 0 (375v0)"}
     840.1      2187     384.1   12225.8     1%  schedule{name=FixedPostUpdate}
     805.1       699    1151.8    5953.1     1%  camera_schedule{camera="Camera 8 (374v0)"}
     800.2       699    1144.7    5942.0     1%  schedule{name=Core3d}
     792.8         6  132139.1  792777.7   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="audio/music/g
     722.5      1935     373.4    4243.4     1%  camera_schedule{camera="Camera 9 (376v0)"}
     696.6        15   46439.4  694571.3   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="audio/music/g
     622.2        10   62221.6  620614.7   100%  asset loading{loader="bevy_image::image_loader::ImageLoader" asset="sprites/noether_sprite
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
   34396.8      17776.1      1936    5725.4  update
   29934.9      15470.2      1936    5137.1  main app
   29922.9      15464.1      1936    5133.0  schedule{name=Main}
   12172.1       6290.5      1936    2320.2  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 1900 frames, summing to 18.22ms:

```text
    6.36 ms   34.9%  PreUpdate
    4.42 ms   24.3%  Update
    2.70 ms   14.8%  PostUpdate
    2.69 ms   14.7%  outside
    1.06 ms    5.8%  RunFixedMainLoop
    0.36 ms    2.0%  Last
    0.25 ms    1.4%  First
    0.23 ms    1.3%  StateTransition
    0.16 ms    0.9%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    6.36    19.03   -12.67   PreUpdate
    4.42     8.93    -4.51   Update
    2.70     5.37    -2.67   PostUpdate
    2.69     7.50    -4.81   outside
    1.06     2.97    -1.91   RunFixedMainLoop
    0.36     0.59    -0.24   Last
    0.25     0.73    -0.48   First
    0.23     0.67    -0.44   StateTransition
    0.16     0.31    -0.15   SpawnScene
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
  83.2%  profiler (Tracy)
  16.6%  the game itself
   0.1%  build launcher (cargo, shell)
   0.1%  audio
```

```text
profiler (Tracy) overhead : 83.2%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           : 16.6%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 83% of sampled cycles.** Its symbol-resolution and
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
  53.6%  game binary + its Rust/C deps
  45.7%  kernel
   0.7%  GPU driver / graphics stack
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
    21.92%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                     
     7.76%  Tracy Profiler   [kernel.kallsyms]                      [k] do_sys_poll                                                                                                                     
     4.84%  Tracy Profiler   [kernel.kallsyms]                      [k] clear_bhb_loop                                                                                                                  
     4.17%  Tracy Profiler   [kernel.kallsyms]                      [k] _copy_from_user                                                                                                                 
     3.79%  Tracy Profiler   libc.so.6                              [.] __poll                                                                                                                          
     2.79%  Tracy Profiler   [kernel.kallsyms]                      [k] __fdget                                                                                                                         
     2.27%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSRETQ_unsafe_stack                                                                                                      
     2.14%  Tracy Profiler   [kernel.kallsyms]                      [k] do_poll.constprop.0                                                                                                             
     1.95%  Tracy Profiler   libc.so.6                              [.] __GI___pthread_disable_asynccancel                                                                                              
     1.63%  Tracy Profiler   libc.so.6                              [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                               
     1.56%  Tracy Profiler   [kernel.kallsyms]                      [k] do_syscall_64                                                                                                                   
     1.55%  Tracy Profiler   libc.so.6                              [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                               
     1.51%  Tracy Profiler   [kernel.kallsyms]                      [k] tcp_poll                                                                                                                        
     1.43%  Tracy Profiler   ambition_game_bin                      [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                      
     1.33%  Tracy Profiler   libc.so.6                              [.] __GI___pthread_enable_asynccancel                                                                                               
     1.25%  Tracy Profiler   [kernel.kallsyms]                      [k] fput                                                                                                                            
     1.16%  Tracy Profiler   [kernel.kallsyms]                      [k] arch_exit_to_user_mode_prepare.isra.0                                                                                           
     0.88%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::DequeueSerial()                                                                                                
     0.79%  Tracy Profiler   [kernel.kallsyms]                      [k] __x64_sys_poll                                                                                                                  
     0.78%  Tracy Profiler   [kernel.kallsyms]                      [k] check_stack_object                                                                                                              
     0.70%  Tracy Profiler   [kernel.kallsyms]                      [k] its_return_thunk                                                                                                                
     0.69%  Tracy Profiler   [kernel.kallsyms]                      [k] sock_poll                                                                                                                       
     0.64%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSCALL_64                                                                                                                
     0.60%  Tracy Profiler   ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.59%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSCALL_64_after_hwframe                                                                                                  
     0.51%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::Worker()                                                                                                       
     0.50%  Tracy Profiler   [kernel.kallsyms]                      [k] tcp_stream_memory_free                                                                                                          
     0.49%  Tracy Profiler   [kernel.kallsyms]                      [k] __check_object_size.part.0                                                                                                      
     0.48%  Tracy Profiler   [kernel.kallsyms]                      [k] syscall_exit_to_user_mode                                                                                                       
     0.48%  Tracy Profiler   libc.so.6                              [.] __strlen_evex                                                                                                                   
     0.45%  Tracy Profiler   [kernel.kallsyms]                      [k] syscall_return_via_sysret                                                                                                       
     0.43%  Tracy Profiler   ambition_game_bin                      [.] tracy::NormalizePath(char const*)                                                                                               
     0.42%  Tracy Profiler   [kernel.kallsyms]                      [k] poll_freewait                                                                                                                   
     0.41%  Tracy Profiler   ambition_game_bin                      [.] tracy::Socket::HasData()                                                                                                        
     0.40%  Tracy Profiler   [kernel.kallsyms]                      [k] x64_sys_call                                                                                                                    
```

## Assets and render resources

- Decoded images: 0 → 300 (560.0 MP, 2239.9 MB of decode work).
- Images resident at end: 268.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **137 images (82.3 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 19 decode(s) landed before the first `room-loaded` (70.5 MP) — boot. Not a gameplay hitch.

⚠ 17 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **34 of 99 notable decodes landed during SETTLED play** (212.0 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  16.8MP  at 29.330s  sprites/perfect_cellular_automaton_spritesheet.1.png
  16.7MP  at 29.109s  sprites/perfect_cellular_automaton_spritesheet.2.png
  16.7MP  at 29.869s  sprites/perfect_cellular_automaton_spritesheet.5.png
  16.5MP  at 29.330s  sprites/perfect_cellular_automaton_spritesheet.3.png
  15.9MP  at 29.330s  sprites/perfect_cellular_automaton_spritesheet.4.png
  15.7MP  at 29.869s  sprites/perfect_cellular_automaton_spritesheet.png
  14.9MP  at 26.284s  sprites/gnu_ton_boss/giant_gnu_hands_spritesheet.png
  14.9MP  at 26.489s  sprites/gnu_ton_boss/gnu_ton_boss_spritesheet.png
   9.4MP  at 29.869s  sprites/perfect_cellular_automaton_spritesheet.6.png
   7.2MP  at 30.080s  sprites/patent_clerk_spritesheet.png
```

Textures decoded more than once:

```text
   2x  sprites/architect_spritesheet.png
   2x  sprites/goblin_spritesheet.png
   2x  sprites/alice_spritesheet.png
   2x  sprites/erdish_spritesheet.png
   2x  sprites/oiler_spritesheet.png
   2x  sprites/bob_spritesheet.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1893860 bytes

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

