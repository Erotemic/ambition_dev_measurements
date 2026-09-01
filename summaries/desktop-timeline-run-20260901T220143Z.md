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

Observed span of the game's own log: **189.8s**.

## Frame time

29 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     13.4       9   139.5    45.9   548.2   548.2    548.2
      2.1       2   472.5   500.9   500.9   500.9    500.9
     10.1      14    73.6    36.9   157.4   414.9    414.9
      9.1      16    63.9    40.4   124.2   298.1    298.1
      8.1      43    23.7    14.3    73.3   258.7    258.7
     11.2      43    23.9    19.3    37.8   125.2    125.2
     12.2      31    34.0    24.1    74.8   116.1    116.1
     14.4      49    18.9    16.5    37.1    96.9     96.9
```

Full series: `frame_times.csv`.

51 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   14.692s     548.2 ms
    3.238s     501.5 ms
    3.682s     444.1 ms
   11.153s     414.9 ms
   15.104s     412.5 ms
   10.738s     298.1 ms
    9.363s     258.7 ms
   11.611s     157.4 ms
   12.794s     125.0 ms
   10.173s     124.2 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.6        4       3      1       0      1
      2.1        4       3      1       0      1
      3.1        4       3      1       0      1
      4.1        4       3      1       0      1
      5.1        4       3      1       0      1
      6.1        4       3      1       0      1
      7.1        4       3      1       0      1
      8.1        4       3      1       0      1
      9.1        4       3      1       0      1
     10.1        4       3      1       0      1
     11.2        4       3      1       0      1
     12.2        4       3      1       0      1
     13.4        4       3      1       0      1
     14.4        4       3      1       0      1
     15.4        4       3      1       0      1
     16.4        4       3      1       0      1
     17.4        4       3      1       0      1
     18.4        4       3      1       0      1
     19.4        4       3      1       0      1
     20.4        4       3      1       0      1
     21.5        4       3      1       0      1
     22.5        4       3      1       0      1
     23.5        4       3      1       0      1
     24.5        4       3      1       0      1
     25.5        4       3      1       0      1
     26.5        4       3      1       0      1
     27.5        4       4      1       0      1
     28.5        4       4      1       0      1
     29.5        4       4      1       0      1
     30.5        4       4      1       0      1
```

Peak world-rendering cameras: **1** at t=0.6s.

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

Peak active portal capture rigs: **0** of 0 at t=0.6s.

```text
        t  rigs  active  budget
      0.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      3.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      5.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      7.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     11.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     13.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     15.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     17.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     19.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     21.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     23.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     25.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     27.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     29.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.6s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1972      130        1
      peak       8192        1972      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **254** (51 visible), text2d 325, per-view projections 69 at t=25.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3234** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.134          0.138        3  render/clustering/elapsed_gpu
         0.065          0.080        3  render/main_opaque_pass_3d/elapsed_gpu
         0.045          0.067       28  render/msaa_writeback/elapsed_gpu
         0.041          0.068       28  render/ui/elapsed_gpu
         0.028          0.052        3  render/clustering/elapsed_cpu
         0.026          0.045        3  render/main_opaque_pass_3d/elapsed_cpu
         0.026          0.068       28  render/ui/elapsed_cpu
         0.024          0.025        3  render/early_mesh_preprocessing/elapsed_gpu
         0.023          0.054       28  render/upscaling/elapsed_gpu
         0.019          0.019        3  render/bin_unpacking/elapsed_gpu
         0.016          0.027        3  render/main_transparent_pass_3d/elapsed_cpu
         0.012          0.012        3  render/main_transparent_pass_3d/elapsed_gpu
         0.008          0.013        3  render/early_mesh_preprocessing/elapsed_cpu
         0.007          0.010        3  render/bin_unpacking/elapsed_cpu
         0.004          0.016       28  render/msaa_writeback/elapsed_cpu
         0.004          0.005       28  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.013       28  render/upscaling/elapsed_cpu
         0.001          0.010       28  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1406042.000    1431919.000        3  render/main_opaque_pass_3d/fragment_shader_invocations
    548855.607    4653589.000       28  render/ui/fragment_shader_invocations
     27179.333      27332.000        3  render/main_transparent_pass_3d/fragment_shader_invocations
      1267.667       1333.000        3  render/main_transparent_pass_3d/vertex_shader_invocations
       671.857       1186.000       28  render/ui/vertex_shader_invocations
       626.333        659.000        3  render/main_transparent_pass_3d/clipper_invocations
       466.333        471.000        3  render/main_transparent_pass_3d/clipper_primitives_out
       334.929        592.000       28  render/ui/clipper_primitives_out
       334.929        592.000       28  render/ui/clipper_invocations
       157.333        164.000        3  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        3  render/early_mesh_preprocessing/compute_shader_invocations
        82.667         86.000        3  render/main_opaque_pass_3d/clipper_primitives_out
        78.667         82.000        3  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000        3  render/bin_unpacking/compute_shader_invocations
         0.000          0.000       28  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       28  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       28  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       28  render/main_transparent_pass_2d/clipper_primitives_out
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
   30749.1         1 30749138.9 30749138.9   100%  bevy_app
   30659.1         1 30659149.0 30659149.0   100%  render thread
   30063.8      1785   16842.5 1125771.8     4%  update
   24954.3      1785   13980.0  769958.1     3%  main app
   24945.5      1785   13975.1  769951.5     3%  schedule{name=Main}
   11362.0      1785    6365.2  452011.8     4%  sub app{name=RenderApp}
   11346.8      1785    6356.7  452005.9     4%  schedule{name=RenderRecovery}
   11273.8      1785    6315.9  451922.8     4%  system{name="bevy_render::run_render_schedule"}
   11223.2      1785    6287.5  451884.8     4%  schedule{name=Render}
   10123.7      1785    5671.5   84547.2     1%  schedule{name=PreUpdate}
    7577.2      1785    4244.9   82394.9     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
    7422.3      1474    5035.5   61800.1     1%  ggrs{name="HandleRequests"}
    7400.2      1474    5020.5   61790.7     1%  schedule{name="AdvanceWorld"}
    7383.0      1474    5008.8   61768.0     1%  schedule{name=AdvanceWorld}
    7313.5      1474    4961.7   61642.0     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
    7300.4      1474    4952.8   61636.7     1%  schedule{name=GgrsSchedule}
    6368.9      1785    3568.0  177243.6     3%  schedule{name=Update}
    5095.4      1785    2854.6  521853.1    10%  sub app{name=RenderExtractApp}
    4336.3      1785    2429.3  293797.6     7%  schedule{name=PostUpdate}
    3886.4      1785    2177.3  521706.3    13%  schedule{name=ExtractSchedule}
    3733.8      1785    2091.7   15051.9     0%  system{name="bevy_render::renderer::render_system"}
    3728.4      1785    2088.7   15048.9     0%  main_render_schedule
    3402.1      1785    1905.9   14720.6     0%  schedule{name=RenderGraph}
    2433.3      1785    1363.2  520884.2    21%  system{name="bevy_render::render_asset::extract_render_asset<bevy_render::texture::gpu_ima
    2240.8      1785    1255.3    5811.3     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    1915.0      5352     357.8    3941.6     0%  schedule{name=Core2d}
    1550.7      1785     868.7  124682.3     8%  schedule{name=RunFixedMainLoop}
    1462.4      1785     819.3    4255.4     0%  system{name="bevy_framepace::framerate_limiter"}
    1402.2    839494       1.7    1356.6     0%  multithreaded executor
    1194.8         4  298708.3 1194794.7   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="audio/music/g
     988.0      1785     553.5  124266.1    13%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
     966.5      1815     532.5  117799.5    12%  schedule{name=FixedMain}
     868.2      1785     486.4    8742.3     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
     853.2      1784     478.3    3951.0     0%  camera_schedule{camera="Camera 0 (375v0)"}
     767.6      3570     215.0     859.6     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
     702.6      1815     387.1  117654.3    17%  schedule{name=FixedPostUpdate}
     659.9      1584     416.6    8499.7     1%  queue_submit{count=11}
     635.8        10   63584.0  635743.0   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="audio/music/g
     599.3      1784     336.0    2152.3     0%  camera_schedule{camera="Camera 9 (376v0)"}
     563.0      1785     315.4    4086.7     1%  system{name="bevy_render::render_asset::prepare_assets<bevy_sprite_render::mesh2d::materia
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
   28938.1      16220.9      1785    4978.3  update
   24184.3      13556.2      1785    4437.2  main app
   24175.5      13551.3      1785    4432.8  schedule{name=Main}
   10910.0       6115.4      1785    2323.9  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 1781 frames, summing to 16.80ms:

```text
    5.74 ms   34.2%  PreUpdate
    3.80 ms   22.6%  Update
    3.04 ms   18.1%  outside
    2.47 ms   14.7%  PostUpdate
    0.90 ms    5.4%  RunFixedMainLoop
    0.31 ms    1.9%  Last
    0.21 ms    1.3%  First
    0.19 ms    1.1%  StateTransition
    0.13 ms    0.8%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    5.74    16.97   -11.24   PreUpdate
    3.80     7.34    -3.54   Update
    3.04     9.14    -6.10   outside
    2.47     5.32    -2.85   PostUpdate
    0.90     2.61    -1.70   RunFixedMainLoop
    0.31     0.55    -0.24   Last
    0.21     0.65    -0.44   First
    0.19     0.53    -0.34   StateTransition
    0.13     0.24    -0.11   SpawnScene
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
  74.0%  profiler (Tracy)
  25.7%  the game itself
   0.2%  build launcher (cargo, shell)
   0.1%  audio
```

```text
profiler (Tracy) overhead : 74.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.2%   (cargo and shell; NOT a compile)
the game itself           : 25.7%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 74% of sampled cycles.** Its symbol-resolution and
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
  59.4%  game binary + its Rust/C deps
  39.8%  kernel
   0.8%  GPU driver / graphics stack
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
    14.48%  Tracy Profiler   ambition_game_bin            [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                               
     8.41%  Tracy Profiler   libc.so.6                    [.] _dl_addr                                                                                                                                  
     5.86%  Tracy Profiler   [kernel.kallsyms]            [k] do_sys_poll                                                                                                                               
     3.57%  Tracy Profiler   [kernel.kallsyms]            [k] clear_bhb_loop                                                                                                                            
     3.13%  Tracy Profiler   [kernel.kallsyms]            [k] _copy_from_user                                                                                                                           
     2.74%  Tracy Profiler   libc.so.6                    [.] __poll                                                                                                                                    
     1.93%  Tracy Profiler   [kernel.kallsyms]            [k] __fdget                                                                                                                                   
     1.85%  Tracy Profiler   ambition_game_bin            [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                                
     1.75%  Tracy Profiler   [kernel.kallsyms]            [k] entry_SYSRETQ_unsafe_stack                                                                                                                
     1.63%  Tracy Profiler   [kernel.kallsyms]            [k] do_poll.constprop.0                                                                                                                       
     1.43%  Tracy Profiler   libc.so.6                    [.] __GI___pthread_disable_asynccancel                                                                                                        
     1.33%  Tracy Profiler   libc.so.6                    [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                                         
     1.25%  Tracy Profiler   libc.so.6                    [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                                         
     1.24%  Tracy Profiler   [kernel.kallsyms]            [k] tcp_poll                                                                                                                                  
     1.16%  Tracy Profiler   [kernel.kallsyms]            [k] do_syscall_64                                                                                                                             
     1.13%  Tracy Profiler   libc.so.6                    [.] __GI___pthread_enable_asynccancel                                                                                                         
     1.10%  Tracy Profiler   [kernel.kallsyms]            [k] fput                                                                                                                                      
     0.92%  Tracy Profiler   [kernel.kallsyms]            [k] arch_exit_to_user_mode_prepare.isra.0                                                                                                     
     0.71%  Tracy Profiler   libc.so.6                    [.] __strlen_evex                                                                                                                             
     0.64%  Tracy Profiler   ambition_game_bin            [.] tracy::Profiler::DequeueSerial()                                                                                                          
     0.61%  Tracy Profiler   [kernel.kallsyms]            [k] __x64_sys_poll                                                                                                                            
     0.56%  Tracy Profiler   [kernel.kallsyms]            [k] entry_SYSCALL_64                                                                                                                          
     0.54%  Tracy Profiler   ambition_game_bin            [.] tracy::NormalizePath(char const*)                                                                                                         
     0.53%  ambition_game_b  ambition_game_bin            [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtNtNtBU
     0.51%  Tracy Profiler   [kernel.kallsyms]            [k] check_stack_object                                                                                                                        
     0.51%  Tracy Profiler   [kernel.kallsyms]            [k] entry_SYSCALL_64_after_hwframe                                                                                                            
     0.50%  Compute Task Po  libc.so.6                    [.] __memmove_evex_unaligned_erms                                                                                                             
     0.50%  Tracy Profiler   [kernel.kallsyms]            [k] sock_poll                                                                                                                                 
     0.48%  Tracy Profiler   ambition_game_bin            [.] tracy::rpmalloc(unsigned long)                                                                                                            
     0.48%  ambition_game_b  ambition_game_bin            [.] _mi_page_malloc_zero                                                                                                                      
     0.45%  Tracy Profiler   [kernel.kallsyms]            [k] its_return_thunk                                                                                                                          
     0.45%  Tracy Profiler   libc.so.6                    [.] __memmove_evex_unaligned_erms                                                                                                             
     0.40%  ambition_game_b  libc.so.6                    [.] __memmove_evex_unaligned_erms                                                                                                             
     0.39%  ambition_game_b  ambition_game_bin            [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20sort_unstabl
     0.38%  Tracy Profiler   [kernel.kallsyms]            [k] __check_object_size.part.0                                                                                                                
```

## Assets and render resources

- Decoded images: 0 → 270 (534.1 MP, 2136.5 MB of decode work).
- Images resident at end: 268.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **130 images (89.6 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 33 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **21 of 93 notable decodes landed during SETTLED play** (46.5 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
   5.0MP  at 10.756s  sprites/ninja_shadow_oni_leader_spritesheet.png
   4.9MP  at 10.617s  sprites/martin_cutta_spritesheet.png
   4.0MP  at 10.708s  sprites/ninja_shadow_duelist_spritesheet.png
   3.9MP  at 10.647s  sprites/niels_boar_spritesheet.png
   2.8MP  at 11.334s  sprites/ramen_nujan_spritesheet.png
   2.6MP  at 10.821s  sprites/paul_diracula_spritesheet.png
   2.6MP  at 11.315s  sprites/raid_enforcer_spritesheet.png
   2.3MP  at 11.059s  sprites/pulse_voyager_captain_spritesheet.png
   2.2MP  at 10.858s  sprites/pipi_tau_spritesheet.png
   1.9MP  at 11.364s  sprites/richard_duckling_spritesheet.png
```

93 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1082660 bytes

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

