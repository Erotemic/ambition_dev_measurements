# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `c62122dfe5c0` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/smash_match_profile` |
| capture target | `direct-built-binary` |
| package / bin | `ambition_app_tools` / `smash_match_profile` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.99.1 (58cf8f9c3 2026-10-13)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/target/profiling/smash_match_profile --window --fighters 4 --seconds 90 ` |
| host | `toothbrush` |
| kernel | `Linux toothbrush 6.8.0-147-generic #147-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep 24 11:33:16 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `smash-match-4p` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

## Smash match coverage

- Entered live match: **yes**.
- All-CPU roster in log: **yes**.
- Completed measured window: **NO**.
- **INCOMPLETE:** the requested live-time budget did not reach
  the match driver's normal completion marker.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=16
- MemTotal:       131727480 kB

## Frame pacing

| observed frame cap | observed present mode |
| --- | --- |
| `Auto` | `Fifo` |

**REFRESH/CAP-LIMITED BENCHMARK:** VSync (`Fifo`) and/or a frame
cap were active. Steady FPS near the monitor refresh rate is
not the game's uncapped throughput. Native hotspot and stall
measurements are still informative.

## Renderer

```text
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.99.02", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **1266.0s**.

## Frame time

13 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      6.9      19    52.7    28.0    46.9   482.6    482.6
      5.9      18    53.5    31.9    83.6   380.1    380.1
      8.0      14    96.6    35.4   313.9   319.3    319.3
      9.1       6   150.4   135.6   213.2   213.2    213.2
      4.9       1   208.4   208.4   208.4   208.4    208.4
     15.4       8   135.9   143.6   178.7   178.7    178.7
     11.2       9   125.2   112.1   176.0   176.0    176.0
     17.5       8   130.6   130.5   158.4   158.4    158.4
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    9.904s     482.3 ms
    8.548s     385.6 ms
   10.744s     319.7 ms
   11.203s     313.5 ms
   11.480s     274.3 ms
   11.698s     221.5 ms
    8.163s     206.3 ms
   11.904s     202.7 ms
   14.135s     175.9 ms
   14.306s     171.7 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 811 images / 363.8 MP in total; 103 were notable enough to print.

```text
ordered by: frame
before smash_stage: 11 image(s), 29.6 MP
  unknown                  5    21.3 MP
  parallax                 4     4.6 MP
  boss-sheet               1     2.5 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      3.3        4       3      1       0      1
      4.9        4       3      1       0      1
      5.9        4       3      1       0      1
      6.9        4       3      1       0      1
      8.0        4       3      1       0      1
      9.1        8       5      1       2      1
     10.1        8       7      1       4      1
     11.2        8       7      1       4      1
     12.3        8       7      1       4      1
     13.3        8       7      1       4      1
     14.4        8       7      1       4      1
     15.4        8       7      1       4      1
     16.5        8       7      1       4      1
     17.5        8       7      1       4      1
```

Peak world-rendering cameras: **1** at t=3.3s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
       offscreen  rigged impostor camera, rigged impostor unpremultiply camera
           other  Cube scrim display camera, Developer Panel Camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=3.3s.

```text
        t  rigs  active  budget
      3.3     0       0  captures<=2
      4.9     0       0  captures<=2
      5.9     0       0  captures<=2
      6.9     0       0  captures<=2
      8.0     0       0  captures<=2
      9.1     0       0  captures<=2
     10.1     0       0  captures<=2
     11.2     0       0  captures<=2
     12.3     0       0  captures<=2
     13.3     0       0  captures<=2
     14.4     0       0  captures<=2
     15.4     0       0  captures<=2
     16.5     0       0  captures<=2
     17.5     0       0  captures<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **4** (largest dimension 1152px) at t=9.1s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1693        0        0
       end       4096        1966        4        0
      peak       4096        1966        4        0
```

Entity count rose by 2048 and then held flat at 4096 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **200** (176 visible), text2d 20, per-view projections 9 at t=13.3s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3864** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.071          0.143       12  render/ui/elapsed_gpu
         0.070          0.158       12  render/msaa_writeback/elapsed_gpu
         0.033          0.073       12  render/upscaling/elapsed_gpu
         0.031          0.075       12  render/ui/elapsed_cpu
         0.024          0.034        4  render/main_opaque_pass_2d/elapsed_gpu
         0.018          0.021        4  render/main_opaque_pass_2d/elapsed_cpu
         0.006          0.016       12  render/msaa_writeback/elapsed_cpu
         0.005          0.012       12  render/upscaling/elapsed_cpu
         0.003          0.004       12  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.005       12  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   2217547.667   13365114.000       12  render/ui/fragment_shader_invocations
    248832.000     331776.000        4  render/main_opaque_pass_2d/fragment_shader_invocations
       293.667        910.000       12  render/ui/vertex_shader_invocations
       145.833        454.000       12  render/ui/clipper_primitives_out
       145.833        454.000       12  render/ui/clipper_invocations
         3.000          4.000        4  render/main_opaque_pass_2d/vertex_shader_invocations
         1.500          2.000        4  render/main_opaque_pass_2d/clipper_invocations
         1.500          2.000        4  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       12  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       12  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       12  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       12  render/main_transparent_pass_2d/clipper_primitives_out
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
   15912.1         1 15912052.0 15912052.0   100%  bevy_app
   15744.4         1 15744383.7 15744383.7   100%  render thread
   15356.5       144  106642.5 1997832.5    13%  update
   14256.0       144   98999.8 1880052.9    13%  main app
   14253.3       144   98981.6 1880043.0    13%  schedule{name=Main}
    8686.1       144   60319.9  263627.3     3%  schedule{name=PreUpdate}
    8325.3       144   57814.7  261512.2     3%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
    8258.9       642   12864.3  222791.2     3%  ggrs{name="HandleRequests"}
    8242.2       642   12838.3  222775.5     3%  schedule{name="AdvanceWorld"}
    8232.8       642   12823.7  222744.8     3%  schedule{name=AdvanceWorld}
    8180.0       642   12741.4  222561.5     3%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
    8170.0       642   12725.9  222549.5     3%  schedule{name=GgrsSchedule}
    3764.1       144   26139.4  411365.4    11%  sub app{name=RenderApp}
    3762.2       144   26126.5  411351.4    11%  schedule{name=RenderRecovery}
    3747.6       144   26024.9  411132.8    11%  system{name="bevy_render::run_render_schedule"}
    3741.4       144   25981.7  411039.5    11%  schedule{name=Render}
    3227.9       144   22416.1 1320937.3    41%  schedule{name=Update}
    2385.4         1 2385401.8 2385401.8   100%  plugin build{plugin="ambition_content::provider::AmbitionExperiencePlugin"}
    2342.3         1 2342309.5 2342309.5   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
    1963.3       642    3058.1   18519.2     1%  system{name="ambition_platformer2d_actor_monolith::body::actors::update::tick_actor_brains
    1436.3         1 1436321.4 1436321.4   100%  plugin build{plugin="ambition_content::plugin::AmbitionContentPlugin"}
    1165.4        49   23783.3 1165244.9   100%  system{name="bevy_yarnspinner::project::compilation::compile_loaded_yarn_files"}
    1097.1       144    7618.5  323414.6    29%  sub app{name=RenderExtractApp}
     991.4       144    6884.7   66355.8     7%  schedule{name=PostUpdate}
     970.2       144    6737.4   23659.5     2%  system{name="bevy_render::renderer::render_system"}
     969.6       144    6733.2   23652.5     2%  main_render_schedule
     939.0         2  469486.9  938972.3   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="content://amb
     893.3       144    6203.6   23210.7     3%  schedule{name=RenderGraph}
     833.6     85921       9.7    7474.0     1%  multithreaded executor
     652.5       144    4531.3   85019.7    13%  system{name="bevy_render::render_asset::prepare_assets<bevy_render::texture::gpu_image::Gp
     621.0       144    4312.7   14309.1     2%  system{name="bevy_core_pipeline::schedule::camera_driver"}
     607.3       144    4217.2   66158.8    11%  schedule{name=ExtractSchedule}
     600.7       759     791.4    9679.4     2%  schedule{name=Core2d}
     441.9         2  220946.4  441892.3   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="audio/music/g
     441.4         1  441415.2  441415.2   100%  schedule{name=Startup}
     348.7       144    2421.3  347888.0   100%  system{name="ambition_platformer2d_provider::lifecycle::prepare_candidate_platformer_sessi
     328.1         1  328068.1  328068.1   100%  system{name="ambition_app::app::setup_systems::setup_host_presentation_system"}
     323.4         1  323442.8  323442.8   100%  plugin build{plugin="bevy_render::RenderPlugin"}
     316.9       144    2200.5   33372.2    11%  schedule{name=Last}
     263.6       144    1830.9   10196.4     4%  schedule{name=RunFixedMainLoop}
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
   13358.7      93417.4       144   19743.7  update
   12375.9      86544.9       144   15799.4  main app
   12373.3      86526.6       144   15788.1  schedule{name=Main}
    8422.4      58898.2       144    1393.8  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw `tracy.trace`: local capture directory only.

## Which phase of the frame owned the time

Mean milliseconds per frame over 140 frames, summing to 102.38ms:

```text
   58.76 ms   57.4%  PreUpdate
   23.40 ms   22.9%  Update
    7.96 ms    7.8%  outside
    6.94 ms    6.8%  PostUpdate
    2.32 ms    2.3%  Last
    1.92 ms    1.9%  RunFixedMainLoop
    0.70 ms    0.7%  First
    0.21 ms    0.2%  SpawnScene
    0.17 ms    0.2%  StateTransition
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
   58.76   120.71     2.05   PreUpdate
   23.40    47.82     2.04   Update
    7.96    15.04     1.89   outside
    6.94    14.03     2.02   PostUpdate
    2.32     4.76     2.05   Last
    1.92     4.04     2.11   RunFixedMainLoop
    0.70     1.47     2.11   First
    0.21     0.43     2.03   SpawnScene
    0.17     0.40     2.42   StateTransition
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
  98.3%  profiler (Tracy)
   1.7%  the game itself
   0.0%  audio
```

```text
profiler (Tracy) overhead : 98.3%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           :  1.7%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 98% of sampled cycles.** Its symbol-resolution and
compression threads compete with the game for the same cores, so every frame
time, zone duration and plugin-build number here is inflated too.

Zone RATIOS remain usable — the instrumentation is uniform across systems.
Absolute per-frame costs are not. For an honest frame time, re-run:

```bash
scripts/measure/profile_desktop.sh --no-tracy
```

which drops `--features profile` (and with it the per-system zones), and
compare its frame census against this one to size the gap.

## Where the native time went

```text
  51.8%  kernel
  48.0%  game binary + its Rust/C deps
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
    95.00%     0.66%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Worker()                                                                                             
    94.50%     0.00%  Tracy Profiler   libc.so.6                              [.] start_thread                                                                                                          
    94.50%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Thread::Launch(void*)                                                                                          
    94.47%     0.00%  Tracy Profiler   libc.so.6                              [.] clone3                                                                                                                
    57.72%     0.50%  Tracy Profiler   smash_match_profile                    [.] tracy::Socket::HasData()                                                                                              
    53.38%     4.65%  Tracy Profiler   libc.so.6                              [.] __poll                                                                                                                
    45.37%     0.80%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSCALL_64_after_hwframe                                                                                        
    38.92%     2.02%  Tracy Profiler   [kernel.kallsyms]                      [k] do_syscall_64                                                                                                         
    33.72%     0.51%  Tracy Profiler   [kernel.kallsyms]                      [k] x64_sys_call                                                                                                          
    31.38%     1.30%  Tracy Profiler   [kernel.kallsyms]                      [k] __x64_sys_poll                                                                                                        
    28.86%    10.21%  Tracy Profiler   [kernel.kallsyms]                      [k] do_sys_poll                                                                                                           
    28.02%    27.25%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)                                                           
     9.90%     2.82%  Tracy Profiler   [kernel.kallsyms]                      [k] do_poll.constprop.0                                                                                                   
     6.39%     6.32%  Tracy Profiler   [kernel.kallsyms]                      [k] clear_bhb_loop                                                                                                        
     5.23%     5.10%  Tracy Profiler   [kernel.kallsyms]                      [k] _copy_from_user                                                                                                       
     4.82%     1.02%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::DequeueSerial()                                                                                      
     4.70%     0.82%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSCALL_64                                                                                                      
     3.56%     0.93%  Tracy Profiler   [kernel.kallsyms]                      [k] sock_poll                                                                                                             
     3.31%     3.02%  Tracy Profiler   [kernel.kallsyms]                      [k] __fdget                                                                                                               
     2.89%     2.82%  Tracy Profiler   [kernel.kallsyms]                      [k] entry_SYSRETQ_unsafe_stack                                                                                            
     2.69%     2.11%  Tracy Profiler   [kernel.kallsyms]                      [k] tcp_poll                                                                                                              
     2.68%     0.69%  Tracy Profiler   [kernel.kallsyms]                      [k] syscall_exit_to_user_mode                                                                                             
     2.46%     2.43%  Tracy Profiler   libc.so.6                              [.] __GI___pthread_disable_asynccancel                                                                                    
     2.06%     2.03%  Tracy Profiler   libc.so.6                              [.] pthread_mutex_unlock@@GLIBC_2.2.5                                                                                     
     2.00%     1.98%  Tracy Profiler   libc.so.6                              [.] pthread_mutex_trylock@@GLIBC_2.34                                                                                     
     1.91%     0.01%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::CommitData()                                                                                         
     1.90%     0.01%  Tracy Profiler   smash_match_profile                    [.] tracy::Profiler::SendData(char const*, unsigned long)                                                                 
     1.82%     0.36%  Tracy Profiler   [kernel.kallsyms]                      [k] __check_object_size                                                                                                   
     1.75%     1.35%  Tracy Profiler   [kernel.kallsyms]                      [k] arch_exit_to_user_mode_prepare.isra.0                                                                                 
     1.70%     1.63%  Tracy Profiler   [kernel.kallsyms]                      [k] fput                                                                                                                  
     1.67%     0.00%  Tracy Profiler   [unknown]                              [k] 0xffffffffffffffff                                                                                                    
     1.66%     1.65%  Tracy Profiler   libc.so.6                              [.] __GI___pthread_enable_asynccancel                                                                                     
     1.39%     0.68%  Tracy Profiler   [kernel.kallsyms]                      [k] its_return_thunk                                                                                                      
     1.33%     0.00%  Tracy Profiler   smash_match_profile                    [.] tracy::Socket::Send(void const*, int)                                                                                 
     1.32%     0.01%  Tracy Profiler   libc.so.6                              [.] __send                                                                                                                
```

## Assets and render resources

- Decoded images: 139 → 812 (364.0 MP, 1456.3 MB of decode work).
- Images resident at end: 754.
- **Resident image BYTES at end: 1455.2 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **629 images (324.4 MP)** at 10.4s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 2 GENERATED image(s) were allocated during gameplay (2.6 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 12 notable decode(s) (31.0 MP) landed AFTER the first `room-loaded` (frame 48) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.1MP  f     51  content://ambition_shared/__art/bundles/c4e6e4eed3b6b5dd19bc8ea687bb61e3e2e280cd78771c3899aeecddd2cc05c0/runtime/full/sprites/eureka_range_tower_spritesheet.png
   3.1MP  f     51  content://ambition_shared/__art/bundles/484e16e92a33daf86f65e91dbbdb93c942e2b48b92e48fe8d28aeafa9ea4e959/runtime/full/sprites/cove_cave_spritesheet.png
   1.4MP  f     51  content://ambition_shared/__art/bundles/d62b73e891247880a6344b9a51711468a6cd2a889877bbd586694cf339b695dc/runtime/full/sprites/bakery_room_spritesheet.png
   1.4MP  f     51  content://ambition_shared/__art/bundles/657bfc8837e52c9c0496bc318551d7e97e3a935e61d569dca99e9ff05e6f7100/runtime/full/sprites/cove_ledges_spritesheet.png
   2.4MP  f     51  content://ambition_shared/__art/bundles/48d101a50c2a45a674358860dcf34cf8eedcebbbd9ff253d2d3abbc444bd85ac/runtime/full/sprites/catacombs_east_spritesheet.png
   8.6MP  f     51  content://ambition_shared/__art/bundles/15cfe4be6bb89b0383fc5e7f67c65a10b8ac5875ced89bdd8f6eb25f0314eddb/runtime/full/sprites/eureka_tether_hall_spritesheet.png
   1.3MP  f     51  content://ambition_shared/__art/bundles/7b2e18c59fd353969aa1ef3a7dab32478b27a46fb01246109215292bfac1350c/runtime/full/sprites/castle_cells_room_spritesheet.png
   3.0MP  f     51  content://ambition_shared/__art/bundles/c65249382c210ad10d443378d2fb881003e7e5a92ba22bbd242775d1884e1f38/runtime/full/sprites/crypt_room_spritesheet.png
```

⚠ 85 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   3x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 547656236 bytes

## Files in the local capture directory and shareable archive

Raw `perf.data` and `tracy.trace` are retained in the local capture
directory but omitted from the default shareable archive. Use
`--include-raw-data` to include them in the archive.

| file | contents | local | archived |
| --- | --- | --- | --- |
| `summary.md` | this file | yes | yes |
| `metadata.txt / metadata.json` | build, commit, host, and capture settings | yes | yes |
| `host-environment.txt` | CPU, GPU, DRM nodes, Vulkan ICDs, graphics env overrides | yes | yes |
| `timeline.md` | per-window perf symbols labelled with the game's own log markers | yes | yes |
| `frame_times.csv` | per-census-window frame-time percentiles | yes | yes |
| `frame_spikes.csv` | every frame over 33.4ms, with its wall-clock second | yes | yes |
| `frame_windows.csv` | the always-on 5s frame census | yes | yes |
| `camera_views.csv` | one row per camera per sample: role, target, size, layers | yes | yes |
| `view_totals.csv` | camera/active/world-rendering/offscreen counts per sample | yes | yes |
| `runtime_census.csv` | entity, archetype, component, body, and player counts | yes | yes |
| `draw_census.csv` | sprite/text/projection population and visibility | yes | yes |
| `render_target_census.csv` | offscreen image targets and their bytes | yes | yes |
| `render_diagnostics.csv` | Bevy per-pass CPU/GPU times and pipeline statistics | yes | yes |
| `portal_activity.csv` | portal capture rigs and the budget bounding them | yes | yes |
| `asset_activity.csv` | cumulative decode work and resident images | yes | yes |
| `image_decodes.csv` | every notable texture decode, with its path | yes | yes |
| `image_arrivals.csv` | images reaching Assets<Image> per census window | yes | yes |
| `world_events.csv` | room loads and session starts/ends, with game time | yes | yes |
| `schedule_census.csv` | registered system counts per sample | yes | yes |
| `schedule_phases.csv` | per-frame milliseconds in each main-schedule phase | yes | yes |
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | yes | yes |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | yes | yes |
| `tracy.trace` | the raw Tracy trace, for the GUI | yes | no |
| `alloc_by_span.csv` | perf_probe: allocations per Bevy system / tracing span | no | no |
| `churn_by_site.csv` | perf_probe: spawns, despawns, inserts, removes per source line | no | no |
| `changes_by_site.csv` | perf_probe: component and resource values marked changed, per source line | no | no |
| `perf_windows/` | one flat perf report per time slice | yes | yes |
| `perf_report.txt` | whole-run flat perf report | yes | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes | yes |
| `perf.data` | the raw perf capture | yes | no |

