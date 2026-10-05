# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `43ae232a7445` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile -- --start-room hall_of_characters ` |
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
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.84", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **684.0s**.

## Frame time

11 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.2      62    13.0     8.7    13.6   104.4    174.4
      3.2      85    12.0     7.8    24.7    89.4    101.4
     10.3      47    21.8    20.7    30.7    34.3     34.3
     12.3      40    25.0    24.9    29.4    30.6     30.6
     11.3      47    21.6    21.7    28.4    29.1     29.1
      9.3      53    19.1    18.9    25.9    27.1     28.6
      4.2      74    13.6    13.8    18.5    19.5     24.2
      7.3      67    15.2    15.3    20.8    21.2     21.9
```

Full series: `frame_times.csv`.

9 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    3.345s     174.4 ms
    3.170s     105.4 ms
    4.836s     101.4 ms
    4.734s      89.5 ms
    4.589s      81.5 ms
   14.252s      76.2 ms
    4.645s      55.7 ms
   11.673s      34.3 ms
   14.018s      33.5 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 293 images / 170.2 MP in total; 33 were notable enough to print.

```text
ordered by: frame
before hall_of_characters: 13 image(s), 80.1 MP
  character-sheet          4    39.9 MP
  character-parts          4    19.5 MP
  unknown                  2    16.5 MP
  fx-sheet                 2     2.2 MP
  boss-sheet               1     2.0 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.2        4       3      1       0      1
      2.2        4       3      1       0      1
      3.2       18      15      1      12      1
      4.2       18      11      1       8      1
      5.2       18       5      1       2      1
      6.2       18      15      1      12      1
      7.3       18       7      1       4      1
      8.3       18       9      1       6      1
      9.3       18       7      1       4      1
     10.3       18      11      1       8      1
     11.3       18      17      1      14      1
     12.3       18      11      1       8      1
```

Peak world-rendering cameras: **1** at t=1.2s.

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

Peak active portal capture rigs: **0** of 0 at t=1.2s.

```text
        t  rigs  active  budget
      1.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      2.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      3.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      4.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      5.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      6.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      7.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      8.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     11.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **14** (largest dimension 2304px) at t=3.2s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1506        0        0
       end      16384        1882      138        1
      peak      16384        1882      138        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **3760** (2894 visible), text2d 35, per-view projections 11 at t=3.2s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3447** in 34 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.040          0.056       11  render/msaa_writeback/elapsed_gpu
         0.036          0.049       11  render/ui/elapsed_gpu
         0.033          0.048       10  render/main_opaque_pass_2d/elapsed_gpu
         0.021          0.031       11  render/ui/elapsed_cpu
         0.019          0.027       11  render/upscaling/elapsed_gpu
         0.015          0.031       10  render/main_opaque_pass_2d/elapsed_cpu
         0.004          0.010       11  render/msaa_writeback/elapsed_cpu
         0.004          0.004       11  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.006       11  render/upscaling/elapsed_cpu
         0.001          0.003       11  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   3053568.000    3211264.000       10  render/main_opaque_pass_2d/fragment_shader_invocations
    425493.909    3031733.000       11  render/ui/fragment_shader_invocations
       770.364       1726.000       11  render/ui/vertex_shader_invocations
       384.182        862.000       11  render/ui/clipper_primitives_out
       384.182        862.000       11  render/ui/clipper_invocations
         4.000          4.000       10  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       10  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       10  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       11  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       11  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       11  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       11  render/main_transparent_pass_2d/clipper_primitives_out
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
   12653.4         1 12653437.4 12653437.4   100%  bevy_app
   12627.6         1 12627621.9 12627621.9   100%  render thread
   11567.6       701   16501.5  514665.8     4%  update
   10719.1       701   15291.2  474038.9     4%  main app
   10716.0       701   15286.7  474033.3     4%  schedule{name=Main}
    5678.1       701    8099.9  219918.1     4%  sub app{name=RenderApp}
    5672.4       701    8091.9  219910.5     4%  schedule{name=RenderRecovery}
    5644.0       701    8051.3  219836.1     4%  system{name="bevy_render::run_render_schedule"}
    5627.0       701    8027.1  219772.9     4%  schedule{name=Render}
    4258.2       701    6074.5   69563.7     2%  schedule{name=PreUpdate}
    3270.0       701    4664.8   58286.9     2%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
    3221.2       581    5544.2   58140.4     2%  ggrs{name="HandleRequests"}
    3213.3       581    5530.6   58130.1     2%  schedule{name="AdvanceWorld"}
    3207.6       581    5520.8   58113.7     2%  schedule{name=AdvanceWorld}
    3183.2       581    5478.8   58015.4     2%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
    3179.1       581    5471.7   58007.6     2%  schedule{name=GgrsSchedule}
    3088.1       701    4405.3  163542.5     5%  schedule{name=Update}
    2925.2       701    4172.9   13364.8     0%  system{name="bevy_render::renderer::render_system"}
    2923.3       701    4170.1   13363.2     0%  main_render_schedule
    2811.2       701    4010.3   13255.1     0%  schedule{name=RenderGraph}
    2199.8       701    3138.1   11982.4     1%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    2121.1      6132     345.9    2124.8     0%  schedule{name=Core2d}
    1707.5       701    2435.8   26749.1     2%  schedule{name=PostUpdate}
     862.4         1  862422.2  862422.2   100%  plugin cleanup
     860.7         1  860718.7  860718.7   100%  plugin cleanup{plugin="bevy_rich_text3d::Text3dPlugin"}
     844.0       701    1204.0  156959.6    19%  sub app{name=RenderExtractApp}
     610.9      6132      99.6     857.4     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
     582.4    396463       1.5     274.5     0%  multithreaded executor
     553.1       701     789.0    8790.9     2%  schedule{name=RunFixedMainLoop}
     545.7       701     778.5   46567.3     9%  schedule{name=ExtractSchedule}
     488.3       701     696.6    2490.3     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
     432.8       533     812.1    2130.0     0%  camera_schedule{camera="Camera -100000 (3373v0)"}
     386.9      6132      63.1     624.1     0%  system{name="bevy_core_pipeline::core_2d::main_transparent_pass_2d_node::main_transparent_
     383.0         1  382975.9  382975.9   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
     368.9      6131      60.2     616.6     0%  main_transparent_pass_2d
     362.8     59373       6.1     131.1     0%  par_for_each{query="(bevy_ecs::entity::Entity, &bevy_camera::visibility::InheritedVisibili
     335.8       701     479.0    8235.1     2%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
     328.4       723     454.2    3325.6     1%  schedule{name=FixedMain}
     319.7      2576     124.1     574.9     0%  transparent_main_pass_2d
     305.3       701     435.5    1748.1     1%  system{name="bevy_sprite_render::render::prepare_sprite_image_bind_groups"}
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
   11052.9      15789.8       701    6465.5  update
   10245.1      14635.8       701    5920.8  main app
   10242.0      14631.4       701    5918.0  schedule{name=Main}
    5458.1       7797.3       701    2611.3  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 693 frames, summing to 16.05ms:

```text
    6.05 ms   37.7%  PreUpdate
    4.58 ms   28.5%  Update
    2.47 ms   15.4%  PostUpdate
    1.33 ms    8.3%  outside
    0.82 ms    5.1%  RunFixedMainLoop
    0.32 ms    2.0%  Last
    0.19 ms    1.2%  First
    0.17 ms    1.1%  StateTransition
    0.12 ms    0.8%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    6.05    19.42     3.21   PreUpdate
    4.58    11.21     2.45   Update
    2.47     5.94     2.41   PostUpdate
    1.33     4.38     3.28   outside
    0.82     2.74     3.35   RunFixedMainLoop
    0.32     0.65     2.00   Last
    0.19     0.61     3.16   First
    0.17     0.55     3.23   StateTransition
    0.12     0.29     2.35   SpawnScene
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
  96.5%  profiler (Tracy)
   3.4%  the game itself
   0.1%  build launcher (cargo, shell)
   0.0%  audio
```

```text
profiler (Tracy) overhead : 96.5%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           :  3.4%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 96% of sampled cycles.** Its symbol-resolution and
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
  51.2%  kernel
  48.5%  game binary + its Rust/C deps
   0.2%  GPU driver / graphics stack
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
    24.77%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                          
     9.66%  Tracy Profiler   [kernel.kallsyms]                 [k] do_sys_poll                                                                                                                          
     6.11%  Tracy Profiler   [kernel.kallsyms]                 [k] clear_bhb_loop                                                                                                                       
     5.37%  Tracy Profiler   [kernel.kallsyms]                 [k] _copy_from_user                                                                                                                      
     5.02%  Tracy Profiler   libc.so.6                         [.] __poll                                                                                                                               
     3.34%  Tracy Profiler   [kernel.kallsyms]                 [k] __fdget                                                                                                                              
     2.97%  Tracy Profiler   [kernel.kallsyms]                 [k] entry_SYSRETQ_unsafe_stack                                                                                                           
     2.91%  Tracy Profiler   [kernel.kallsyms]                 [k] do_poll.constprop.0                                                                                                                  
     2.79%  Tracy Profiler   libc.so.6                         [.] __GI___pthread_disable_asynccancel                                                                                                   
     2.27%  Tracy Profiler   [kernel.kallsyms]                 [k] do_syscall_64                                                                                                                        
     2.26%  Tracy Profiler   libc.so.6                         [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                                    
     2.11%  Tracy Profiler   [kernel.kallsyms]                 [k] tcp_poll                                                                                                                             
     2.05%  Tracy Profiler   libc.so.6                         [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                                    
     1.75%  Tracy Profiler   libc.so.6                         [.] __GI___pthread_enable_asynccancel                                                                                                    
     1.60%  Tracy Profiler   [kernel.kallsyms]                 [k] fput                                                                                                                                 
     1.34%  Tracy Profiler   [kernel.kallsyms]                 [k] arch_exit_to_user_mode_prepare.isra.0                                                                                                
     1.05%  Tracy Profiler   [kernel.kallsyms]                 [k] __x64_sys_poll                                                                                                                       
     1.02%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::DequeueSerial()                                                                                                     
     0.88%  Tracy Profiler   [kernel.kallsyms]                 [k] sock_poll                                                                                                                            
     0.87%  Tracy Profiler   [kernel.kallsyms]                 [k] entry_SYSCALL_64                                                                                                                     
     0.83%  Tracy Profiler   [kernel.kallsyms]                 [k] check_stack_object                                                                                                                   
     0.80%  Tracy Profiler   [kernel.kallsyms]                 [k] entry_SYSCALL_64_after_hwframe                                                                                                       
     0.76%  Tracy Profiler   ambition_game_bin                 [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                           
     0.73%  Tracy Profiler   [kernel.kallsyms]                 [k] syscall_exit_to_user_mode                                                                                                            
     0.70%  Tracy Profiler   [kernel.kallsyms]                 [k] __check_object_size.part.0                                                                                                           
     0.65%  Tracy Profiler   [kernel.kallsyms]                 [k] syscall_return_via_sysret                                                                                                            
     0.60%  Tracy Profiler   ambition_game_bin                 [.] tracy::Profiler::Worker()                                                                                                            
     0.58%  Tracy Profiler   [kernel.kallsyms]                 [k] tcp_stream_memory_free                                                                                                               
     0.56%  Tracy Profiler   [kernel.kallsyms]                 [k] poll_select_set_timeout                                                                                                              
     0.53%  Tracy Profiler   [kernel.kallsyms]                 [k] x64_sys_call                                                                                                                         
     0.51%  Tracy Profiler   ambition_game_bin                 [.] tracy::Socket::HasData()                                                                                                             
     0.51%  Tracy Profiler   [kernel.kallsyms]                 [k] poll_freewait                                                                                                                        
     0.49%  Tracy Profiler   ambition_game_bin                 [.] tracy::rpmalloc(unsigned long)                                                                                                       
     0.47%  Tracy Profiler   [kernel.kallsyms]                 [k] fpregs_assert_state_consistent                                                                                                       
     0.45%  Tracy Profiler   [kernel.kallsyms]                 [k] its_return_thunk                                                                                                                     
```

## Assets and render resources

- Decoded images: 105 → 293 (170.2 MP, 680.8 MB of decode work).
- Images resident at end: 265.
- **Resident image BYTES at end: 680.8 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **293 images (170.2 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 20 GENERATED image(s) were allocated during gameplay (48.0 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  21x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 2901488 bytes

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

