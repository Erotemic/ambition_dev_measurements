# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `649b9e1e2558` on `main` |
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

Observed span of the game's own log: **195.1s**.

## Frame time

51 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     31.5       5   361.1   112.0   868.9   868.9    868.9
     35.6       7   142.2    36.1   592.5   592.5    592.5
      1.5      52    15.8     6.8    10.2    95.7    299.9
     34.6      26    43.2    28.4   120.7   248.9    248.9
     32.5      23    40.2    30.7   103.6   149.1    149.1
     29.8      12    87.2    94.4   122.2   126.3    126.3
     33.5      30    33.6    26.1    72.6   123.1    123.1
      8.6      67    15.3    13.2    31.4    51.3    100.1
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   33.208s     868.8 ms
   32.339s     652.4 ms
   36.858s     592.6 ms
    2.698s     299.9 ms
   36.265s     248.9 ms
   37.063s     204.9 ms
   33.357s     149.3 ms
   31.170s     126.3 ms
   34.351s     123.1 ms
   30.946s     122.1 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.5        4       3      1       0      1
      2.5        4       3      1       0      1
      4.5        4       3      1       0      1
      6.5        4       3      1       0      1
      8.6        4       3      1       0      1
     10.6        4       3      1       0      1
     12.6        4       3      1       0      1
     14.6        4       3      1       0      1
     16.6        4       3      1       0      1
     18.6        4       3      1       0      1
     20.6        4       3      1       0      1
     22.7        4       3      1       0      1
     24.7        4       3      1       0      1
     26.7        4       3      1       0      1
     28.7        4       3      1       0      1
     31.5        4       3      1       0      1
     33.5        4       3      1       0      1
     35.6        4       3      1       0      1
     37.7        4       3      1       0      1
     39.7        4       3      1       0      1
     41.7        4       3      1       0      1
     43.7        4       3      1       0      1
     45.7        4       4      1       0      1
     47.8        4       4      1       0      1
     49.8        4       4      1       0      1
     51.8        4       4      1       0      1
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
      0.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      4.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      8.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     16.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     20.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     24.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     28.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     33.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     37.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     41.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     45.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     49.8     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1998      130        1
      peak       8192        1998      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **253** (50 visible), text2d 65, per-view projections 17 at t=36.7s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3240** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.114          0.120        9  render/clustering/elapsed_gpu
         0.049          0.075        9  render/main_opaque_pass_3d/elapsed_gpu
         0.039          0.081       51  render/msaa_writeback/elapsed_gpu
         0.039          0.079       51  render/ui/elapsed_gpu
         0.032          0.046        9  render/main_opaque_pass_3d/elapsed_cpu
         0.030          0.060       51  render/ui/elapsed_cpu
         0.030          0.053        9  render/clustering/elapsed_cpu
         0.021          0.023        9  render/early_mesh_preprocessing/elapsed_gpu
         0.021          0.048        9  render/bin_unpacking/elapsed_gpu
         0.020          0.066       51  render/upscaling/elapsed_gpu
         0.013          0.024        9  render/main_transparent_pass_3d/elapsed_cpu
         0.010          0.012        9  render/main_transparent_pass_3d/elapsed_gpu
         0.007          0.014        9  render/early_mesh_preprocessing/elapsed_cpu
         0.006          0.010        9  render/bin_unpacking/elapsed_cpu
         0.004          0.033       51  render/upscaling/elapsed_cpu
         0.004          0.009       51  render/msaa_writeback/elapsed_cpu
         0.004          0.009       51  render/main_transparent_pass_2d/elapsed_gpu
         0.001          0.005       51  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1340701.778    1529830.000        9  render/main_opaque_pass_3d/fragment_shader_invocations
    523192.804    3999485.000       51  render/ui/fragment_shader_invocations
     29107.778      41154.000        9  render/main_transparent_pass_3d/fragment_shader_invocations
      1124.667       1446.000        9  render/main_transparent_pass_3d/vertex_shader_invocations
       606.000        694.000       51  render/ui/vertex_shader_invocations
       555.778        716.000        9  render/main_transparent_pass_3d/clipper_invocations
       546.667        716.000        9  render/main_transparent_pass_3d/clipper_primitives_out
       302.000        346.000       51  render/ui/clipper_primitives_out
       302.000        346.000       51  render/ui/clipper_invocations
       185.333        256.000        9  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        9  render/early_mesh_preprocessing/compute_shader_invocations
        96.667        132.000        9  render/main_opaque_pass_3d/clipper_primitives_out
        92.667        128.000        9  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000        9  render/bin_unpacking/compute_shader_invocations
         0.000          0.000       51  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       51  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       51  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       51  render/main_transparent_pass_2d/clipper_primitives_out
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
   53045.7         1 53045716.8 53045716.8   100%  bevy_app
   53015.4         1 53015394.4 53015394.4   100%  render thread
   52262.7      3388   15425.8  782889.9     1%  update
   46077.7      3388   13600.3  460056.8     1%  main app
   46059.3      3388   13594.8  460048.0     1%  schedule{name=Main}
   19805.4      3388    5845.7  239135.2     1%  sub app{name=RenderApp}
   19775.3      3388    5836.9  239128.3     1%  schedule{name=RenderRecovery}
   19631.7      3388    5794.5  239030.7     1%  system{name="bevy_render::run_render_schedule"}
   19534.9      3388    5765.9  238983.3     1%  schedule{name=Render}
   17969.1      3388    5303.7   93126.1     1%  schedule{name=PreUpdate}
   13012.7      3388    3840.8   91736.6     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   12715.2      2629    4836.5   69450.7     1%  ggrs{name="HandleRequests"}
   12672.8      2629    4820.4   69436.4     1%  schedule{name="AdvanceWorld"}
   12639.8      2629    4807.8   69413.1     1%  schedule{name=AdvanceWorld}
   12507.5      2629    4757.5   69293.3     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
   12482.6      2629    4748.1   69284.6     1%  schedule{name=GgrsSchedule}
   12095.7      3388    3570.1  156642.4     1%  schedule{name=Update}
    7988.4      3388    2357.8   19343.0     0%  schedule{name=PostUpdate}
    7669.7      3388    2263.8   34815.5     0%  system{name="bevy_render::renderer::render_system"}
    7658.2      3388    2260.4   34813.0     0%  main_render_schedule
    6900.1      3388    2036.6   34703.4     1%  schedule{name=RenderGraph}
    6156.3      3388    1817.1  750865.4    12%  sub app{name=RenderExtractApp}
    5031.5      3388    1485.1  746829.0    15%  schedule{name=ExtractSchedule}
    4665.4      3388    1377.0   34130.9     1%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    3821.8     10161     376.1   32247.8     1%  schedule{name=Core2d}
    3171.8      3388     936.2   11642.3     0%  schedule{name=RunFixedMainLoop}
    2845.9      3388     840.0  745263.1    26%  system{name="bevy_render::render_asset::extract_render_asset<bevy_render::texture::gpu_ima
    2808.4   1589814       1.8    2242.3     0%  multithreaded executor
    2069.2      3388     610.7   10558.5     1%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    2027.2      3257     622.4    7243.9     0%  schedule{name=FixedMain}
    2002.4      3388     591.0   62113.4     3%  system{name="bevy_render::view::window::prepare_windows"}
    1703.3        10  170327.2 1702873.3   100%  asset loading{loader="bevy_image::image_loader::ImageLoader" asset="sprites/noether_sprite
    1640.4      3387     484.3    4165.5     0%  camera_schedule{camera="Camera 0 (375v0)"}
    1631.6      3388     481.6   10354.1     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    1497.7      6776     221.0     968.2     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
    1493.2      3257     458.5    6866.9     0%  schedule{name=FixedPostUpdate}
    1440.6         4  360155.7  836792.5    58%  asset loading{loader="bevy_image::image_loader::ImageLoader" asset="sprites/noether_sprite
    1242.8      3387     366.9   32255.0     3%  camera_schedule{camera="Camera 9 (376v0)"}
    1212.7         7  173239.1 1212515.9   100%  asset loading{loader="bevy_image::image_loader::ImageLoader" asset="sprites/noether_sprite
    1193.2         8  149146.9 1193056.5   100%  asset loading{loader="bevy_image::image_loader::ImageLoader" asset="sprites/noether_sprite
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
   51479.8      15199.2      3388    5146.6  update
   45617.6      13468.4      3388    4385.8  main app
   45599.3      13463.0      3388    4382.0  schedule{name=Main}
   19566.2       5776.9      3388    2231.1  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 3378 frames, summing to 15.48ms:

```text
    5.36 ms   34.6%  PreUpdate
    3.81 ms   24.6%  Update
    2.40 ms   15.5%  PostUpdate
    2.04 ms   13.2%  outside
    0.97 ms    6.3%  RunFixedMainLoop
    0.32 ms    2.1%  Last
    0.22 ms    1.4%  First
    0.20 ms    1.3%  StateTransition
    0.14 ms    0.9%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    5.36    16.17   -10.81   PreUpdate
    3.81     7.11    -3.30   Update
    2.40     4.92    -2.52   PostUpdate
    2.04     5.77    -3.73   outside
    0.97     2.45    -1.47   RunFixedMainLoop
    0.32     0.60    -0.27   Last
    0.22     0.64    -0.42   First
    0.20     0.51    -0.31   StateTransition
    0.14     0.26    -0.11   SpawnScene
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
  62.7%  profiler (Tracy)
  36.9%  the game itself
   0.2%  audio
   0.2%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead : 62.7%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.2%   (cargo and shell; NOT a compile)
the game itself           : 36.9%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 63% of sampled cycles.** Its symbol-resolution and
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
  66.3%  game binary + its Rust/C deps
  32.2%  kernel
   1.4%  GPU driver / graphics stack
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
    12.27%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                     
     8.99%  Tracy Profiler   libc.so.6                              [.] _dl_addr                                                                                                                        
     4.36%  Tracy Profiler   [kernel.kallsyms]                      [k] do_sys_poll                                                                                                                     
     2.64%  Tracy Profiler   [kernel.kallsyms]                      [k] clear_bhb_loop                                                                                                                  
     2.39%  Tracy Profiler   ambition_game_bin                      [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                      
     2.26%  Tracy Profiler   [kernel.kallsyms]                      [k] _copy_from_user                                                                                                                 
     2.11%  Tracy Profiler   libc.so.6                              [.] __poll                                                                                                                          
     1.35%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSRETQ_unsafe_stack                                                                                                      
     1.32%  Tracy Profiler   [kernel.kallsyms]                      [k] __fdget                                                                                                                         
     1.10%  Tracy Profiler   libc.so.6                              [.] __GI___pthread_disable_asynccancel                                                                                              
     1.09%  Tracy Profiler   [kernel.kallsyms]                      [k] do_poll.constprop.0                                                                                                             
     0.93%  Tracy Profiler   libc.so.6                              [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                               
     0.90%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layere
     0.90%  Tracy Profiler   [kernel.kallsyms]                      [k] do_syscall_64                                                                                                                   
     0.88%  Tracy Profiler   libc.so.6                              [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                               
     0.84%  Tracy Profiler   [kernel.kallsyms]                      [k] tcp_poll                                                                                                                        
     0.82%  Tracy Profiler   libc.so.6                              [.] __GI___pthread_enable_asynccancel                                                                                               
     0.81%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.75%  Tracy Profiler   libc.so.6                              [.] __strlen_evex                                                                                                                   
     0.72%  Tracy Profiler   [kernel.kallsyms]                      [k] fput                                                                                                                            
     0.67%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.61%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     0.60%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20so
     0.55%  Tracy Profiler   [kernel.kallsyms]                      [k] arch_exit_to_user_mode_prepare.isra.0                                                                                           
     0.54%  Tracy Profiler   libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.53%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.51%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_
     0.45%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_
     0.44%  Tracy Profiler   [kernel.kallsyms]                      [k] __x64_sys_poll                                                                                                                  
     0.43%  Tracy Profiler   ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.43%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::DequeueSerial()                                                                                                
     0.41%  Tracy Profiler   ambition_game_bin                      [.] tracy::NormalizePath(char const*)                                                                                               
     0.41%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::NormalizePath(char const*)                                                                                               
     0.39%  Tracy Profiler   [kernel.kallsyms]                      [k] its_return_thunk                                                                                                                
     0.35%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSCALL_64                                                                                                                
```

## Assets and render resources

- Decoded images: 0 → 433 (539.7 MP, 2158.7 MB of decode work).
- Images resident at end: 252.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **128 images (89.1 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 22 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **50 of 93 notable decodes landed during SETTLED play** (232.5 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  16.8MP  at 35.266s  sprites/perfect_cellular_automaton_spritesheet.1.png
  16.7MP  at 34.425s  sprites/perfect_cellular_automaton_spritesheet.2.png
  16.7MP  at 34.676s  sprites/perfect_cellular_automaton_spritesheet.5.png
  16.5MP  at 35.266s  sprites/perfect_cellular_automaton_spritesheet.3.png
  15.9MP  at 34.676s  sprites/perfect_cellular_automaton_spritesheet.4.png
  15.7MP  at 34.676s  sprites/perfect_cellular_automaton_spritesheet.png
  14.9MP  at 31.786s  sprites/gnu_ton_boss/gnu_ton_boss_spritesheet.png
   9.4MP  at 34.676s  sprites/perfect_cellular_automaton_spritesheet.6.png
   7.2MP  at 35.639s  sprites/patent_clerk_spritesheet.png
   6.5MP  at 35.266s  sprites/player_robot_v2_spritesheet.png
```

93 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1251008 bytes

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

