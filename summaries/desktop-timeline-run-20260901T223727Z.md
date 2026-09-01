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

Observed span of the game's own log: **304.7s**.

## Frame time

25 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.5      61    13.7     8.2    12.0    74.0    199.7
      6.5      93    10.8     9.7    14.2    32.3     81.9
     10.5      54    18.7    16.6    30.6    34.9     50.7
     16.6      53    19.1    18.1    23.6    29.1     37.9
     23.6      52    19.6    18.9    24.7    25.9     35.3
     20.6      53    19.1    18.1    24.9    28.2     29.8
     14.6      55    18.6    18.2    23.1    27.1     29.6
     19.6      53    19.0    18.3    24.4    25.4     28.8
```

Full series: `frame_times.csv`.

8 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    2.544s     199.7 ms
    7.416s      81.9 ms
    2.618s      74.0 ms
    2.344s      71.1 ms
   11.449s      50.6 ms
   17.660s      38.0 ms
   24.405s      35.2 ms
   11.483s      35.0 ms
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
     13.6        4       3      1       0      1
     14.6        4       4      1       0      1
     15.6        4       3      1       0      1
     16.6        4       3      1       0      1
     17.6        4       3      1       0      1
     18.6        4       3      1       0      1
     19.6        4       3      1       0      1
     20.6        4       3      1       0      1
     21.6        4       3      1       0      1
     22.6        4       3      1       0      1
     23.6        4       4      1       0      1
     24.7        4       4      1       0      1
     25.7        4       3      1       0      1
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
      2.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      4.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      6.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
      8.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     10.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     12.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     14.6     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     16.6     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     18.6     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     20.6     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     22.6     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     24.7     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1965        0        0
      peak       8192        1965      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **248** (47 visible), text2d 205, per-view projections 41 at t=16.6s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3234** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.122          0.127       12  render/clustering/elapsed_gpu
         0.039          0.084       25  render/ui/elapsed_gpu
         0.038          0.052       12  render/main_opaque_pass_3d/elapsed_cpu
         0.031          0.052       12  render/clustering/elapsed_cpu
         0.026          0.050       25  render/ui/elapsed_cpu
         0.022          0.023       12  render/early_mesh_preprocessing/elapsed_gpu
         0.020          0.054       12  render/main_opaque_pass_3d/elapsed_gpu
         0.019          0.024       25  render/upscaling/elapsed_gpu
         0.018          0.019       12  render/bin_unpacking/elapsed_gpu
         0.014          0.017       12  render/main_transparent_pass_3d/elapsed_cpu
         0.008          0.011       12  render/bin_unpacking/elapsed_cpu
         0.008          0.009       12  render/main_transparent_pass_3d/elapsed_gpu
         0.004          0.006       12  render/early_mesh_preprocessing/elapsed_cpu
         0.004          0.005       25  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.007       25  render/upscaling/elapsed_cpu
         0.001          0.003       25  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    731769.640    4651911.000       25  render/ui/fragment_shader_invocations
    472858.583    1552726.000       12  render/main_opaque_pass_3d/fragment_shader_invocations
      6726.250      21633.000       12  render/main_transparent_pass_3d/fragment_shader_invocations
       842.333       1349.000       12  render/main_transparent_pass_3d/vertex_shader_invocations
       605.840        758.000       25  render/ui/vertex_shader_invocations
       418.667        667.000       12  render/main_transparent_pass_3d/clipper_invocations
       358.917        479.000       12  render/main_transparent_pass_3d/clipper_primitives_out
       301.920        378.000       25  render/ui/clipper_primitives_out
       301.920        378.000       25  render/ui/clipper_invocations
       128.000        128.000       12  render/early_mesh_preprocessing/compute_shader_invocations
       121.667        164.000       12  render/main_opaque_pass_3d/vertex_shader_invocations
        64.000         64.000       12  render/bin_unpacking/compute_shader_invocations
        62.000         86.000       12  render/main_opaque_pass_3d/clipper_primitives_out
        60.833         82.000       12  render/main_opaque_pass_3d/clipper_invocations
         0.000          0.000       25  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       25  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       25  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       25  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (8 spans).
- GPU pass timings: **measured** (8 spans).
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
   26566.6         1 26566565.3 26566565.3   100%  bevy_app
   26539.1         1 26539090.7 26539090.7   100%  render thread
   26112.9      1959   13329.7  497061.8     2%  update
   23999.2      1959   12250.7  452431.0     2%  main app
   23988.7      1959   12245.4  452422.6     2%  schedule{name=Main}
   11102.4      1959    5667.4  181564.6     2%  sub app{name=RenderApp}
   11084.7      1959    5658.3  181558.3     2%  schedule{name=RenderRecovery}
   11001.2      1959    5615.7  181467.3     2%  system{name="bevy_render::run_render_schedule"}
   10946.9      1959    5588.0  181412.8     2%  schedule{name=Render}
    8596.5      1959    4388.2   64653.1     1%  schedule{name=PreUpdate}
    6630.1      1959    3384.4  143700.6     2%  schedule{name=Update}
    5855.7      1959    2989.1   63326.2     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
    5733.8      1150    4985.9   60610.6     1%  ggrs{name="HandleRequests"}
    5716.0      1150    4970.4   60602.7     1%  schedule{name="AdvanceWorld"}
    5702.5      1150    4958.7   60576.3     1%  schedule{name=AdvanceWorld}
    5647.8      1150    4911.1   60421.2     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
    5637.4      1150    4902.1   60416.8     1%  schedule{name=GgrsSchedule}
    4509.2      1959    2301.8   17824.3     0%  schedule{name=PostUpdate}
    3705.3      1959    1891.4   10994.6     0%  system{name="bevy_render::renderer::render_system"}
    3699.0      1959    1888.2   10992.6     0%  main_render_schedule
    3244.1      1959    1656.0    8599.6     0%  schedule{name=RenderGraph}
    2147.7      1959    1096.3    7997.4     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    2097.6      1959    1070.8  189549.8     9%  sub app{name=RenderExtractApp}
    1803.7      5874     307.1    6070.1     0%  schedule{name=Core2d}
    1564.7    901239       1.7    2934.1     0%  multithreaded executor
    1559.5      1959     796.1    4266.1     0%  system{name="bevy_render::view::window::prepare_windows"}
    1465.1      1959     747.9    5505.0     0%  schedule{name=RunFixedMainLoop}
    1309.1      1959     668.3   40209.2     3%  schedule{name=ExtractSchedule}
     861.1      1958     439.8    6085.1     1%  camera_schedule{camera="Camera 0 (375v0)"}
     847.9      3918     216.4     738.2     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
     843.4      1959     430.5    5034.8     1%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
     821.3      1666     493.0    3828.0     0%  schedule{name=FixedMain}
     785.0      1959     400.7    2032.3     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
     560.5      1666     336.4    3621.3     1%  schedule{name=FixedPostUpdate}
     547.2      1958     279.5    4410.8     1%  camera_schedule{camera="Camera 9 (376v0)"}
     502.4      1959     256.4    3097.7     1%  schedule{name=Last}
     474.6      1959     242.3     742.8     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
     458.2      5874      78.0     605.5     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
     423.6      1958     216.3    3211.8     1%  camera_schedule{camera="Camera 7 (373v0)"}
     415.2      1959     212.0    3258.1     1%  system{name="bevy_render::render_asset::prepare_assets<bevy_sprite_render::mesh2d::materia
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
   25615.9      13082.7      1959    4991.2  update
   23546.8      12025.9      1959    4471.0  main app
   23536.2      12020.6      1959    4466.3  schedule{name=Main}
   10920.9       5577.6      1959    2192.1  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 1875 frames, summing to 13.43ms:

```text
    4.57 ms   34.0%  PreUpdate
    3.62 ms   27.0%  Update
    2.35 ms   17.5%  PostUpdate
    1.26 ms    9.4%  outside
    0.79 ms    5.9%  RunFixedMainLoop
    0.31 ms    2.3%  Last
    0.21 ms    1.6%  First
    0.19 ms    1.4%  StateTransition
    0.14 ms    1.0%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    4.57    12.95    -8.38   PreUpdate
    3.62     6.59    -2.97   Update
    2.35     4.91    -2.56   PostUpdate
    1.26     3.90    -2.64   outside
    0.79     2.04    -1.26   RunFixedMainLoop
    0.31     0.60    -0.29   Last
    0.21     0.61    -0.40   First
    0.19     0.49    -0.30   StateTransition
    0.14     0.24    -0.11   SpawnScene
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
  86.2%  profiler (Tracy)
  13.6%  the game itself
   0.1%  build launcher (cargo, shell)
   0.1%  audio
```

```text
profiler (Tracy) overhead : 86.2%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           : 13.6%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 86% of sampled cycles.** Its symbol-resolution and
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
  55.9%  game binary + its Rust/C deps
  43.5%  kernel
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
    23.57%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                          
     8.01%  Tracy Profiler   [kernel.kallsyms]                 [k] do_sys_poll                                                                                                                          
     4.62%  Tracy Profiler   [kernel.kallsyms]                 [k] clear_bhb_loop                                                                                                                       
     4.23%  Tracy Profiler   [kernel.kallsyms]                 [k] _copy_from_user                                                                                                                      
     4.06%  Tracy Profiler   libc.so.6                         [.] __poll                                                                                                                               
     2.44%  Tracy Profiler   [kernel.kallsyms]                 [k] __fdget                                                                                                                              
     2.35%  Tracy Profiler   [kernel.kallsyms]                 [k] do_poll.constprop.0                                                                                                                  
     2.32%  Tracy Profiler   [kernel.kallsyms]                 [k] entry_SYSRETQ_unsafe_stack                                                                                                           
     2.01%  Tracy Profiler   libc.so.6                         [.] __GI___pthread_disable_asynccancel                                                                                                   
     1.73%  Tracy Profiler   libc.so.6                         [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                                    
     1.72%  Tracy Profiler   libc.so.6                         [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                                    
     1.70%  Tracy Profiler   [kernel.kallsyms]                 [k] tcp_poll                                                                                                                             
     1.56%  Tracy Profiler   [kernel.kallsyms]                 [k] do_syscall_64                                                                                                                        
     1.47%  Tracy Profiler   ambition_game_bin                 [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                           
     1.31%  Tracy Profiler   libc.so.6                         [.] __GI___pthread_enable_asynccancel                                                                                                    
     1.26%  Tracy Profiler   [kernel.kallsyms]                 [k] fput                                                                                                                                 
     1.11%  Tracy Profiler   [kernel.kallsyms]                 [k] arch_exit_to_user_mode_prepare.isra.0                                                                                                
     0.83%  Tracy Profiler   [kernel.kallsyms]                 [k] __x64_sys_poll                                                                                                                       
     0.82%  Tracy Profiler   [kernel.kallsyms]                 [k] check_stack_object                                                                                                                   
     0.82%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::DequeueSerial()                                                                                                     
     0.72%  Tracy Profiler   ambition_game_bin                 [.] tracy::rpmalloc(unsigned long)                                                                                                       
     0.70%  Tracy Profiler   [kernel.kallsyms]                 [k] its_return_thunk                                                                                                                     
     0.67%  Tracy Profiler   [kernel.kallsyms]                 [k] entry_SYSCALL_64                                                                                                                     
     0.64%  Tracy Profiler   [kernel.kallsyms]                 [k] sock_poll                                                                                                                            
     0.59%  Tracy Profiler   [kernel.kallsyms]                 [k] entry_SYSCALL_64_after_hwframe                                                                                                       
     0.54%  Tracy Profiler   [kernel.kallsyms]                 [k] __check_object_size.part.0                                                                                                           
     0.52%  Tracy Profiler   [kernel.kallsyms]                 [k] syscall_exit_to_user_mode                                                                                                            
     0.52%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::Worker()                                                                                                            
     0.50%  Tracy Profiler   [kernel.kallsyms]                 [k] syscall_return_via_sysret                                                                                                            
     0.48%  Tracy Profiler   libc.so.6                         [.] __strlen_evex                                                                                                                        
     0.47%  Tracy Profiler   [kernel.kallsyms]                 [k] tcp_stream_memory_free                                                                                                               
     0.47%  ambition_game_b  ambition_game_bin                 [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     0.43%  Tracy Profiler   ambition_game_bin                 [.] tracy::Socket::HasData()                                                                                                             
     0.43%  Tracy Profiler   [kernel.kallsyms]                 [k] fpregs_assert_state_consistent                                                                                                       
     0.42%  Tracy Profiler   ambition_game_bin                 [.] tracy::NormalizePath(char const*)                                                                                                    
```

## Assets and render resources

- Decoded images: 0 → 275 (91.7 MP, 366.7 MB of decode work).
- Images resident at end: 265.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **141 images (86.1 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 19 decode(s) landed before the first `room-loaded` (70.5 MP) — boot. Not a gameplay hitch.

✔ No notable texture decoded while gameplay was live.

19 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1487072 bytes

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

