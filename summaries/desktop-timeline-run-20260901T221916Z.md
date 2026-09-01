# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `2c1e73085d4f` on `main` |
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

Observed span of the game's own log: **157.4s**.

## Frame time

66 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.5      19    43.5     7.0    87.7   546.3    546.3
     63.0      11    90.3    45.1   399.6   399.6    399.6
     66.1      11   103.2    65.9   396.6   396.6    396.6
      3.6      94    10.8     7.6    27.6    48.2    141.0
     64.0      34    30.1    27.4    51.3    97.5     97.5
      4.6      84    12.0    11.1    16.2    27.6     65.1
     62.0      43    24.5    21.6    57.3    64.8     64.8
     67.1      54    17.4    16.1    23.1    39.1     46.5
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    2.958s     546.3 ms
   64.236s     399.6 ms
   67.673s     396.6 ms
   67.276s     270.7 ms
   64.455s     219.1 ms
    4.803s     141.0 ms
   67.778s     104.8 ms
   64.890s      97.4 ms
    2.411s      88.1 ms
   66.857s      77.3 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.5        4       3      1       0      1
      3.6        4       3      1       0      1
      6.6        4       4      1       0      1
      9.6        4       4      1       0      1
     12.6        4       4      1       0      1
     15.6        4       4      1       0      1
     18.7        4       4      1       0      1
     21.7        4       4      1       0      1
     24.7        4       4      1       0      1
     27.7        4       4      1       0      1
     30.7        4       4      1       0      1
     33.8        4       4      1       0      1
     36.8        4       4      1       0      1
     39.8        4       4      1       0      1
     42.8        4       4      1       0      1
     45.8        4       4      1       0      1
     48.9        4       4      1       0      1
     51.9        4       4      1       0      1
     54.9        4       4      1       0      1
     57.9        4       3      1       0      1
     60.9        4       3      1       0      1
     64.0        4       3      1       0      1
     67.1        4       4      1       0      1
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
      5.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     15.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     20.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     25.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     30.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     35.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     40.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     45.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     50.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     55.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     60.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     66.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1925      130        1
      peak       8192        1925      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **249** (49 visible), text2d 50, per-view projections 14 at t=63.0s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3234** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.118          0.157       63  render/clustering/elapsed_gpu
         0.044          0.088       63  render/main_opaque_pass_3d/elapsed_gpu
         0.039          0.094       66  render/msaa_writeback/elapsed_gpu
         0.031          0.045       66  render/ui/elapsed_gpu
         0.029          0.076       63  render/main_opaque_pass_3d/elapsed_cpu
         0.027          0.052       63  render/clustering/elapsed_cpu
         0.024          0.058       63  render/bin_unpacking/elapsed_gpu
         0.023          0.065       66  render/ui/elapsed_cpu
         0.022          0.027       63  render/early_mesh_preprocessing/elapsed_gpu
         0.019          0.028       66  render/upscaling/elapsed_gpu
         0.018          0.041       63  render/main_transparent_pass_3d/elapsed_cpu
         0.010          0.026       63  render/main_transparent_pass_3d/elapsed_gpu
         0.005          0.012       63  render/bin_unpacking/elapsed_cpu
         0.005          0.029       66  render/msaa_writeback/elapsed_cpu
         0.005          0.045       66  render/main_transparent_pass_2d/elapsed_gpu
         0.005          0.015       63  render/early_mesh_preprocessing/elapsed_cpu
         0.003          0.012       66  render/upscaling/elapsed_cpu
         0.001          0.006       66  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1337415.000    1796766.000       63  render/main_opaque_pass_3d/fragment_shader_invocations
    210372.606    2636245.000       66  render/ui/fragment_shader_invocations
     28720.762      50990.000       63  render/main_transparent_pass_3d/fragment_shader_invocations
      1360.063       2227.000       63  render/main_transparent_pass_3d/vertex_shader_invocations
       673.746       1107.000       63  render/main_transparent_pass_3d/clipper_invocations
       519.556        919.000       63  render/main_transparent_pass_3d/clipper_primitives_out
       439.455        694.000       66  render/ui/vertex_shader_invocations
       218.727        346.000       66  render/ui/clipper_primitives_out
       218.727        346.000       66  render/ui/clipper_invocations
       198.794        276.000       63  render/main_opaque_pass_3d/vertex_shader_invocations
       152.381        192.000       63  render/early_mesh_preprocessing/compute_shader_invocations
       103.159        142.000       63  render/main_opaque_pass_3d/clipper_primitives_out
        99.397        138.000       63  render/main_opaque_pass_3d/clipper_invocations
        88.381        128.000       63  render/bin_unpacking/compute_shader_invocations
         0.000          0.000       66  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       66  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       66  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       66  render/main_transparent_pass_2d/clipper_primitives_out
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
   68102.3         1 68102265.4 68102265.4   100%  bevy_app
   68077.8         1 68077770.3 68077770.3   100%  render thread
   67268.4      4522   14875.8  557525.3     1%  update
   61088.5      4522   13509.2  497448.3     1%  main app
   61063.2      4522   13503.6  497439.2     1%  schedule{name=Main}
   30607.3      4522    6768.5  223211.8     1%  sub app{name=RenderApp}
   30565.4      4522    6759.3  223202.3     1%  schedule{name=RenderRecovery}
   30372.5      4522    6716.6  223086.5     1%  system{name="bevy_render::run_render_schedule"}
   30240.1      4522    6687.3  223029.8     1%  schedule{name=Render}
   22130.9      4522    4894.1  116848.4     1%  schedule{name=PreUpdate}
   17107.1      4522    3783.1  155351.3     1%  schedule{name=Update}
   14929.4      4522    3301.5  115590.9     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   14516.8      3882    3739.5  112099.9     1%  ggrs{name="HandleRequests"}
   14455.3      3882    3723.7  112092.0     1%  schedule{name="AdvanceWorld"}
   14410.1      3882    3712.0  111980.2     1%  schedule{name=AdvanceWorld}
   14294.3      4522    3161.1   14845.3     0%  system{name="bevy_render::renderer::render_system"}
   14278.9      4522    3157.7   14840.9     0%  main_render_schedule
   14210.1      3882    3660.5  111793.1     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
   14175.5      3882    3651.6  111786.7     1%  schedule{name=GgrsSchedule}
   13340.4      4522    2950.1   10528.5     0%  schedule{name=RenderGraph}
   11600.8      4522    2565.4   19059.1     0%  schedule{name=PostUpdate}
    9435.4      4522    2086.6    7783.9     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    6145.1      4522    1358.9  534891.4     9%  sub app{name=RenderExtractApp}
    5273.7     13563     388.8    4302.3     0%  schedule{name=Core2d}
    4867.6      4522    1076.4  361385.4     7%  schedule{name=ExtractSchedule}
    4030.5   2265590       1.8     872.7     0%  multithreaded executor
    3891.4      4522     860.5    9993.0     0%  schedule{name=RunFixedMainLoop}
    3776.8      3747    1008.0    4644.2     0%  camera_schedule{camera="Camera 8 (374v0)"}
    3756.0      3747    1002.4    4637.4     0%  schedule{name=Core3d}
    3071.5      4522     679.2    7036.0     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2632.5      3747     702.6    1796.8     0%  queue_submit{count=21}
    2305.9      4522     509.9    7785.1     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    2246.1      4286     524.1    4230.0     0%  schedule{name=FixedMain}
    2224.6      4521     492.1    3379.3     0%  camera_schedule{camera="Camera 0 (375v0)"}
    2153.0      9044     238.1    8909.0     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
    1837.0      4522     406.2  359496.6    20%  system{name="bevy_render::render_asset::extract_render_asset<bevy_render::texture::gpu_ima
    1638.6      4521     362.4    3440.1     0%  camera_schedule{camera="Camera 9 (376v0)"}
    1550.8      4286     361.8    3979.9     0%  schedule{name=FixedPostUpdate}
    1519.4      4522     336.0   27037.6     2%  system{name="bevy_framepace::framerate_limiter"}
    1484.6      4521     328.4    4311.8     0%  camera_schedule{camera="Camera 7 (373v0)"}
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
   66710.8      14755.8      4522    5429.8  update
   60591.0      13402.1      4522    4686.1  main app
   60565.8      13396.6      4522    4683.5  schedule{name=Main}
   30384.1       6720.7      4522    2812.9  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 4463 frames, summing to 14.92ms:

```text
    4.94 ms   33.1%  PreUpdate
    4.02 ms   27.0%  Update
    2.61 ms   17.5%  PostUpdate
    1.57 ms   10.5%  outside
    0.90 ms    6.0%  RunFixedMainLoop
    0.32 ms    2.1%  Last
    0.22 ms    1.5%  First
    0.20 ms    1.4%  StateTransition
    0.14 ms    0.9%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    4.94    15.81   -10.87   PreUpdate
    4.02     7.16    -3.13   Update
    2.61     4.67    -2.07   PostUpdate
    1.57     4.54    -2.97   outside
    0.90     2.72    -1.82   RunFixedMainLoop
    0.32     0.48    -0.16   Last
    0.22     0.59    -0.36   First
    0.20     0.60    -0.40   StateTransition
    0.14     0.22    -0.08   SpawnScene
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
  51.8%  the game itself
  47.7%  profiler (Tracy)
   0.3%  audio
   0.2%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead : 47.7%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.2%   (cargo and shell; NOT a compile)
the game itself           : 51.8%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 48% of sampled cycles.** Its symbol-resolution and
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
  68.2%  game binary + its Rust/C deps
  29.0%  kernel
   2.7%  GPU driver / graphics stack
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
     9.60%  Tracy Profiler   ambition_game_bin                  [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                         
     3.32%  Tracy Profiler   [kernel.kallsyms]                  [k] do_sys_poll                                                                                                                         
     3.20%  Tracy Profiler   ambition_game_bin                  [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                          
     1.96%  Tracy Profiler   [kernel.kallsyms]                  [k] clear_bhb_loop                                                                                                                      
     1.82%  Tracy Profiler   libc.so.6                          [.] __poll                                                                                                                              
     1.78%  Tracy Profiler   [kernel.kallsyms]                  [k] _copy_from_user                                                                                                                     
     1.32%  ambition_game_b  ambition_game_bin                  [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINt
     1.26%  ambition_game_b  libc.so.6                          [.] __memmove_evex_unaligned_erms                                                                                                       
     1.24%  ambition_game_b  ambition_game_bin                  [.] _mi_page_malloc_zero                                                                                                                
     1.18%  Tracy Profiler   [kernel.kallsyms]                  [k] __fdget                                                                                                                             
     0.99%  Tracy Profiler   [kernel.kallsyms]                  [k] do_poll.constprop.0                                                                                                                 
     0.95%  ambition_game_b  ambition_game_bin                  [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20sort_u
     0.95%  ambition_game_b  ambition_game_bin                  [.] _RNvXNtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run         
     0.92%  Tracy Profiler   libc.so.6                          [.] __GI___pthread_disable_asynccancel                                                                                                  
     0.89%  Tracy Profiler   [kernel.kallsyms]                  [k] entry_SYSRETQ_unsafe_stack                                                                                                          
     0.79%  Compute Task Po  libc.so.6                          [.] __memmove_evex_unaligned_erms                                                                                                       
     0.78%  ambition_game_b  ambition_game_bin                  [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_    
     0.76%  Tracy Profiler   [kernel.kallsyms]                  [k] tcp_poll                                                                                                                            
     0.74%  Tracy Profiler   libc.so.6                          [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                                   
     0.73%  Tracy Profiler   [kernel.kallsyms]                  [k] do_syscall_64                                                                                                                       
     0.72%  Tracy Profiler   libc.so.6                          [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                                   
     0.65%  ambition_game_b  ambition_game_bin                  [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_keyj
     0.63%  Tracy Profiler   libc.so.6                          [.] __strlen_evex                                                                                                                       
     0.60%  Tracy Profiler   libc.so.6                          [.] __memmove_evex_unaligned_erms                                                                                                       
     0.57%  ambition_game_b  ambition_game_bin                  [.] mi_theap_umalloc                                                                                                                    
     0.55%  Compute Task Po  ambition_game_bin                  [.] _mi_page_malloc_zero                                                                                                                
     0.52%  Tracy Profiler   libc.so.6                          [.] __GI___pthread_enable_asynccancel                                                                                                   
     0.51%  Tracy Profiler   [kernel.kallsyms]                  [k] fput                                                                                                                                
     0.46%  Tracy Profiler   [kernel.kallsyms]                  [k] arch_exit_to_user_mode_prepare.isra.0                                                                                               
     0.45%  Tracy Symbol Wo  ambition_game_bin                  [.] tracy::NormalizePath(char const*)                                                                                                   
     0.45%  Tracy Profiler   ambition_game_bin                  [.] tracy::NormalizePath(char const*)                                                                                                   
     0.43%  Compute Task Po  ambition_game_bin                  [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINt
     0.42%  Tracy Profiler   ambition_game_bin                  [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                           
     0.41%  Tracy Profiler   ambition_game_bin                  [.] tracy::rpmalloc(unsigned long)                                                                                                      
     0.41%  Tracy Profiler   ambition_game_bin                  [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy::QueueIt
```

## Assets and render resources

- Decoded images: 0 → 267 (533.5 MP, 2134.0 MB of decode work).
- Images resident at end: 266.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **149 images (104.4 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 43 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **29 of 93 notable decodes landed during SETTLED play** (165.1 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  16.8MP  at 65.678s  sprites/perfect_cellular_automaton_spritesheet.1.png
  16.7MP  at 65.407s  sprites/perfect_cellular_automaton_spritesheet.2.png
  16.7MP  at 66.074s  sprites/perfect_cellular_automaton_spritesheet.5.png
  16.5MP  at 65.678s  sprites/perfect_cellular_automaton_spritesheet.3.png
  15.9MP  at 66.074s  sprites/perfect_cellular_automaton_spritesheet.4.png
  15.7MP  at 65.678s  sprites/perfect_cellular_automaton_spritesheet.png
   9.4MP  at 65.407s  sprites/perfect_cellular_automaton_spritesheet.6.png
   7.2MP  at 66.226s  sprites/patent_clerk_spritesheet.png
   6.5MP  at 65.678s  sprites/player_robot_v2_spritesheet.png
   5.7MP  at 65.678s  sprites/robot_spritesheet.png
```

93 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1204616 bytes

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

