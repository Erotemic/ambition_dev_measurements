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
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.84", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **624.3s**.

## Frame time

14 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.4      90     9.4     6.9     8.2    48.4    178.2
      8.5      54    19.5    13.1    54.1    93.8    138.2
      4.4     139     7.9     6.9     8.9    32.5     84.7
     12.5      42    24.1    22.1    37.3    44.9     44.9
     14.5      44    23.2    22.2    29.7    32.2     32.2
     13.5      44    22.6    22.3    28.8    30.9     30.9
      9.5      48    20.1    19.7    27.9    28.9     28.9
     11.5      48    20.9    20.1    26.8    28.8     28.8
```

Full series: `frame_times.csv`.

12 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    2.360s     178.3 ms
   10.014s     138.2 ms
    9.875s      93.8 ms
    9.781s      88.0 ms
    6.079s      83.2 ms
   10.087s      51.8 ms
   16.759s      50.3 ms
    2.182s      49.4 ms
   14.039s      45.0 ms
   13.956s      44.8 ms
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
    1     1718     True  central_hub_complex -> hall_of_characters
```

Frames over 33.4 ms AFTER the last transition was logged (t=9.768s): **8**, worst 138.2 ms.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 305 images / 178.8 MP in total; 34 were notable enough to print.

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
      0.4        4       3      1       0      1
      1.4        4       3      1       0      1
      2.4        4       3      1       0      1
      3.4        4       3      1       0      1
      4.4        6       5      1       2      1
      5.4        6       5      1       2      1
      6.4        6       5      1       2      1
      7.5        6       5      1       2      1
      8.5       18      17      1      14      1
      9.5       18      11      1       8      1
     10.5       18      15      1      12      1
     11.5       18      15      1      12      1
     12.5       18      13      1      10      1
     13.5       18      15      1      12      1
     14.5       18       9      1       6      1
```

Peak world-rendering cameras: **1** at t=0.4s.

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

Peak active portal capture rigs: **0** of 0 at t=0.4s.

```text
        t  rigs  active  budget
      0.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      1.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      2.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      3.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      4.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      5.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      6.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      7.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      8.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     11.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     13.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     14.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **14** (largest dimension 2304px) at t=8.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1492        0        0
       end      16384        2016      138        1
      peak      16384        2016      138        1
```

⚠ Entity count rose by 14336 over the session and was **still
climbing in the second half** (+8192 after t=7.5s). Growth that never falls across room
transitions is the shape of a lifecycle leak; check `runtime_census.csv`
against the room markers in `timeline.md`.

Peak sprites: **3755** (2953 visible), text2d 35, per-view projections 11 at t=8.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3445** in 34 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.038          0.068       14  render/ui/elapsed_gpu
         0.028          0.035       14  render/msaa_writeback/elapsed_gpu
         0.024          0.049       14  render/ui/elapsed_cpu
         0.020          0.030       10  render/main_opaque_pass_2d/elapsed_gpu
         0.017          0.057       10  render/main_opaque_pass_2d/elapsed_cpu
         0.014          0.018       14  render/upscaling/elapsed_gpu
         0.005          0.035       14  render/msaa_writeback/elapsed_cpu
         0.004          0.005       14  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.004       14  render/upscaling/elapsed_cpu
         0.001          0.002       14  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   2189721.600    2985984.000       10  render/main_opaque_pass_2d/fragment_shader_invocations
   1055000.000    3919436.000       14  render/ui/fragment_shader_invocations
       698.286       1450.000       14  render/ui/vertex_shader_invocations
       348.143        724.000       14  render/ui/clipper_primitives_out
       348.143        724.000       14  render/ui/clipper_invocations
         4.000          4.000       10  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       10  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       10  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       14  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       14  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       14  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       14  render/main_transparent_pass_2d/clipper_primitives_out
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
   15264.0         1 15263989.3 15263989.3   100%  bevy_app
   15236.7         1 15236742.3 15236742.3   100%  render thread
   14943.7      1133   13189.5  479690.3     3%  update
   13661.9      1133   12058.1  449230.5     3%  main app
   13656.9      1133   12053.8  449224.4     3%  schedule{name=Main}
    7858.1      1133    6935.7  120057.9     2%  sub app{name=RenderApp}
    7850.1      1133    6928.6  120051.6     2%  schedule{name=RenderRecovery}
    7813.4      1133    6896.2  119908.2     2%  system{name="bevy_render::run_render_schedule"}
    7788.6      1133    6874.3  119847.7     2%  schedule{name=Render}
    5010.7      1133    4422.5   74003.9     1%  schedule{name=PreUpdate}
    3987.5      1133    3519.4  135015.5     3%  schedule{name=Update}
    3527.7      1133    3113.6   72438.5     2%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
    3465.3       645    5372.6   57213.7     2%  ggrs{name="HandleRequests"}
    3455.5       645    5357.3   57204.9     2%  schedule{name="AdvanceWorld"}
    3448.6       645    5346.7   57191.9     2%  schedule{name=AdvanceWorld}
    3419.8       645    5302.0   57096.8     2%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
    3414.8       645    5294.2   57092.3     2%  schedule{name=GgrsSchedule}
    3092.4      1133    2729.4   13678.9     0%  system{name="bevy_render::renderer::render_system"}
    3089.5      1133    2726.8   13675.3     0%  main_render_schedule
    2925.4      1133    2582.0   13485.6     0%  schedule{name=RenderGraph}
    2387.9      1133    2107.6   22373.4     1%  schedule{name=PostUpdate}
    2209.6      1133    1950.3   11466.4     1%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    2116.0      6626     319.3    4020.8     0%  schedule{name=Core2d}
    1664.7      1133    1469.3    6308.2     0%  system{name="bevy_render::view::window::prepare_windows"}
    1274.6      1133    1125.0  169986.4    13%  sub app{name=RenderExtractApp}
     843.7    576814       1.5     335.1     0%  multithreaded executor
     796.9      1133     703.4   67664.0     8%  schedule{name=ExtractSchedule}
     757.0      1133     668.1    5463.3     1%  schedule{name=RunFixedMainLoop}
     560.7      6626      84.6    1214.5     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
     547.4      1133     483.1    3086.5     1%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
     462.3      2266     204.0     474.8     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
     423.3      1133     373.6    5109.6     1%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
     412.5       942     437.9    3119.9     1%  schedule{name=FixedMain}
     369.0         1  369021.1  369021.1   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
     319.6      6626      48.2     584.5     0%  system{name="bevy_core_pipeline::core_2d::main_transparent_pass_2d_node::main_transparent_
     300.1      6624      45.3     580.6     0%  main_transparent_pass_2d
     299.7      1132     264.7    1737.7     1%  camera_schedule{camera="Camera 0 (398v0)"}
     293.9      1132     259.7    2215.6     1%  camera_schedule{camera="Camera 9 (399v0)"}
     284.3       942     301.8    2794.8     1%  schedule{name=FixedPostUpdate}
     282.2      1133     249.0    2951.1     1%  schedule{name=Last}
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
   14464.0      12777.4      1133    5490.1  update
   13212.6      11671.9      1133    4885.0  main app
   13207.7      11667.6      1133    4882.4  schedule{name=Main}
    7738.0       6835.7      1133    2268.6  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 1104 frames, summing to 12.81ms:

```text
    4.33 ms   33.8%  PreUpdate
    3.65 ms   28.5%  Update
    2.12 ms   16.5%  PostUpdate
    1.28 ms   10.0%  outside
    0.69 ms    5.4%  RunFixedMainLoop
    0.29 ms    2.3%  Last
    0.17 ms    1.4%  First
    0.16 ms    1.3%  StateTransition
    0.11 ms    0.9%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    4.33    12.70     2.93   PreUpdate
    3.65     7.96     2.18   Update
    2.12     5.69     2.68   PostUpdate
    1.28     4.05     3.17   outside
    0.69     1.95     2.82   RunFixedMainLoop
    0.29     0.66     2.29   Last
    0.17     0.57     3.30   First
    0.16     0.46     2.85   StateTransition
    0.11     0.26     2.27   SpawnScene
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
  95.5%  profiler (Tracy)
   4.4%  the game itself
   0.1%  build launcher (cargo, shell)
   0.0%  audio
```

```text
profiler (Tracy) overhead : 95.5%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           :  4.4%
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
  51.1%  kernel
  48.6%  game binary + its Rust/C deps
   0.3%  GPU driver / graphics stack
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
    24.18%  Tracy Profiler   ambition_game_bin              [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                                             
     9.48%  Tracy Profiler   [kernel.kallsyms]              [k] do_sys_poll                                                                                                                             
     6.00%  Tracy Profiler   [kernel.kallsyms]              [k] clear_bhb_loop                                                                                                                          
     5.33%  Tracy Profiler   [kernel.kallsyms]              [k] _copy_from_user                                                                                                                         
     4.91%  Tracy Profiler   libc.so.6                      [.] __poll                                                                                                                                  
     4.20%  Tracy Profiler   [kernel.kallsyms]              [k] __fdget                                                                                                                                 
     2.89%  Tracy Profiler   [kernel.kallsyms]              [k] entry_SYSRETQ_unsafe_stack                                                                                                              
     2.75%  Tracy Profiler   [kernel.kallsyms]              [k] do_poll.constprop.0                                                                                                                     
     2.73%  Tracy Profiler   libc.so.6                      [.] __GI___pthread_disable_asynccancel                                                                                                      
     2.10%  Tracy Profiler   [kernel.kallsyms]              [k] do_syscall_64                                                                                                                           
     2.06%  Tracy Profiler   libc.so.6                      [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                                       
     2.04%  Tracy Profiler   [kernel.kallsyms]              [k] tcp_poll                                                                                                                                
     2.03%  Tracy Profiler   libc.so.6                      [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                                       
     1.74%  Tracy Profiler   libc.so.6                      [.] __GI___pthread_enable_asynccancel                                                                                                       
     1.67%  Tracy Profiler   [kernel.kallsyms]              [k] fput                                                                                                                                    
     1.40%  Tracy Profiler   [kernel.kallsyms]              [k] arch_exit_to_user_mode_prepare.isra.0                                                                                                   
     1.04%  Tracy Profiler   [kernel.kallsyms]              [k] __x64_sys_poll                                                                                                                          
     0.97%  Tracy Profiler   ambition_game_bin              [.] tracy::Profiler::DequeueSerial()                                                                                                        
     0.88%  Tracy Profiler   [kernel.kallsyms]              [k] entry_SYSCALL_64                                                                                                                        
     0.86%  Tracy Profiler   [kernel.kallsyms]              [k] check_stack_object                                                                                                                      
     0.78%  Tracy Profiler   [kernel.kallsyms]              [k] sock_poll                                                                                                                               
     0.77%  Tracy Profiler   ambition_game_bin              [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                              
     0.72%  Tracy Profiler   [kernel.kallsyms]              [k] entry_SYSCALL_64_after_hwframe                                                                                                          
     0.64%  Tracy Profiler   ambition_game_bin              [.] tracy::Profiler::Worker()                                                                                                               
     0.62%  Tracy Profiler   [kernel.kallsyms]              [k] syscall_exit_to_user_mode                                                                                                               
     0.60%  Tracy Profiler   [kernel.kallsyms]              [k] syscall_return_via_sysret                                                                                                               
     0.59%  Tracy Profiler   [kernel.kallsyms]              [k] __check_object_size.part.0                                                                                                              
     0.58%  Tracy Profiler   [kernel.kallsyms]              [k] tcp_stream_memory_free                                                                                                                  
     0.54%  Tracy Profiler   ambition_game_bin              [.] tracy::Socket::HasData()                                                                                                                
     0.52%  Tracy Profiler   [kernel.kallsyms]              [k] x64_sys_call                                                                                                                            
     0.51%  Tracy Profiler   [kernel.kallsyms]              [k] poll_select_set_timeout                                                                                                                 
     0.48%  Tracy Profiler   [kernel.kallsyms]              [k] its_return_thunk                                                                                                                        
     0.47%  Tracy Profiler   [kernel.kallsyms]              [k] fpregs_assert_state_consistent                                                                                                          
     0.44%  Tracy Profiler   ambition_game_bin              [.] tracy::rpmalloc(unsigned long)                                                                                                          
     0.44%  Tracy Profiler   [kernel.kallsyms]              [k] poll_freewait                                                                                                                           
```

## Assets and render resources

- Decoded images: 102 → 305 (178.8 MP, 715.0 MB of decode work).
- Images resident at end: 268.
- **Resident image BYTES at end: 715.0 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **174 images (137.4 MP)** at 10.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 20 GENERATED image(s) were allocated during gameplay (48.0 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

✔ FIRST ROOM: no notable decode landed in the second after the first `room-loaded` (frame 515) with a LATER frame stamp — the first room's art, the player's sheet included, was in before the route activated.

⚠ 7 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **1 of 34 notable decodes landed during SETTLED play** (8.0 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
   8.0MP  at 7.997s  sprites/perfect_cellular_automaton_parts.png
```

Textures decoded more than once:

```text
  21x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 2690132 bytes

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

