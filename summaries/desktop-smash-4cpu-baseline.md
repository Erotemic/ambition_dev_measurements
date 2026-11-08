# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `7d6d8e4017a2` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/smash_match_profile` |
| package / bin | `ambition_app_tools` / `smash_match_profile` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.99.1 (58cf8f9c3 2026-10-13)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile smash-match -- --fighters 4 --seconds 90 ` |
| host | `toothbrush` |
| kernel | `Linux toothbrush 6.8.0-147-generic #147-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep 24 11:33:16 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `smash-match-4p` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=16
- MemTotal:       131727480 kB

## Renderer

```text
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.99.02", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **132.3s**.

## Frame time

90 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      3.1      40    16.3     7.2    64.3   173.7    173.7
      4.1      63    15.9    13.2    27.3    60.2    131.6
     53.5      53    19.1    17.4    26.9    29.3     57.7
     60.6      33    32.0    31.3    44.6    56.8     56.8
     59.5      48    21.1    18.5    36.5    55.0     55.0
     61.6      42    23.4    22.9    31.0    50.5     50.5
     65.6      41    24.7    23.3    34.6    42.9     42.9
     35.3      48    21.1    18.8    35.0    42.5     42.5
```

Full series: `frame_times.csv`.

39 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   40.914s     173.6 ms
   41.259s     131.6 ms
   40.522s     128.3 ms
   40.394s      63.7 ms
   41.347s      60.2 ms
   91.126s      57.6 ms
   98.436s      56.8 ms
   97.205s      55.0 ms
   98.529s      52.3 ms
   97.723s      45.5 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 987 images / 466.8 MP in total; 155 were notable enough to print.

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
      2.1        4       3      1       0      1
      6.1        7       3      1       0      1
     10.2        9       5      1       2      1
     14.2        9       5      1       2      1
     18.2        9       3      1       0      1
     22.3       13       3      1       0      1
     26.3       13       5      1       2      1
     30.3       11       3      1       0      1
     34.3       13       5      1       2      1
     38.3       11       5      1       2      1
     42.4       13       5      1       2      1
     46.4       13       5      1       2      1
     50.5       13       5      1       2      1
     54.5       13       3      1       0      1
     58.5       13       3      1       0      1
     62.6       13       4      1       1      1
     66.6       13       7      1       4      1
     70.7       13       5      1       2      1
     74.7        9       4      1       1      1
     78.7        8       3      1       0      1
     82.7        8       3      1       0      1
     86.8        8       3      1       0      1
     90.8        8       3      1       0      1
```

Peak world-rendering cameras: **1** at t=2.1s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
       offscreen  Cast bubble camera, rigged impostor camera, rigged impostor unpremultiply camera
           other  Cube pause camera, Cube scrim display camera, Developer Panel Camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=2.1s.

```text
        t  rigs  active  budget
      2.1     0       0  captures<=2
      9.2     0       0  captures<=2
     16.2     0       0  captures<=2
     23.3     0       0  captures<=2
     30.3     0       0  captures<=2
     37.3     0       0  captures<=2
     44.4     0       0  captures<=2
     51.5     0       0  captures<=2
     58.5     0       0  captures<=2
     65.6     0       0  captures<=2
     72.7     0       0  captures<=2
     79.7     0       0  captures<=2
     86.8     0       0  captures<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **8** (largest dimension 1152px) at t=22.3s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1693        0        0
       end       4096        2140        0        0
      peak       4096        2140        4        0
```

Entity count rose by 2048 and then held flat at 4096 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **425** (283 visible), text2d 20, per-view projections 9 at t=62.6s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3870** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.068          0.193       90  render/ui/elapsed_gpu
         0.061          0.102       90  render/msaa_writeback/elapsed_gpu
         0.028          0.043       90  render/upscaling/elapsed_gpu
         0.026          0.059       90  render/ui/elapsed_cpu
         0.022          0.052       88  render/main_opaque_pass_2d/elapsed_cpu
         0.016          0.038       88  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.010       90  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.009       90  render/msaa_writeback/elapsed_cpu
         0.003          0.011       90  render/upscaling/elapsed_cpu
         0.002          0.016       90  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    843197.844   11916590.000       90  render/ui/fragment_shader_invocations
    679356.443    1327104.000       88  render/main_opaque_pass_2d/fragment_shader_invocations
       957.644      17219.000       90  render/main_transparent_pass_2d/fragment_shader_invocations
       734.978       2446.000       90  render/ui/vertex_shader_invocations
       366.489       1222.000       90  render/ui/clipper_primitives_out
       366.489       1222.000       90  render/ui/clipper_invocations
        23.545        140.000       88  render/main_opaque_pass_2d/vertex_shader_invocations
        20.341        128.000       88  render/main_opaque_pass_2d/clipper_invocations
        20.341        128.000       88  render/main_opaque_pass_2d/clipper_primitives_out
         3.556         64.000       90  render/main_transparent_pass_2d/vertex_shader_invocations
         3.333         60.000       90  render/main_transparent_pass_2d/clipper_invocations
         3.333         60.000       90  render/main_transparent_pass_2d/clipper_primitives_out
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
   92848.5         1 92848546.0 92848546.0   100%  bevy_app
   92792.6         1 92792618.5 92792618.5   100%  render thread
   80283.0      5492   14618.2  717543.3     1%  update
   74611.2      5492   13585.4  680114.8     1%  main app
   74583.1      5492   13580.3  680108.5     1%  schedule{name=Main}
   34815.0      5492    6339.2  117301.9     0%  schedule{name=PreUpdate}
   28534.6      5492    5195.7  115580.3     0%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   28229.7      4449    6345.2  115424.7     0%  ggrs{name="HandleRequests"}
   28169.4      4449    6331.6  115415.3     0%  schedule{name="AdvanceWorld"}
   28120.0      4449    6320.5  115400.3     0%  schedule{name=AdvanceWorld}
   27924.8      4449    6276.6  115280.2     0%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   27889.1      4449    6268.6  115273.2     0%  schedule{name=GgrsSchedule}
   26202.4      5492    4771.0  143266.7     1%  sub app{name=RenderApp}
   26156.5      5492    4762.7  143261.2     1%  schedule{name=RenderRecovery}
   25953.3      5492    4725.7  143181.5     1%  system{name="bevy_render::run_render_schedule"}
   25813.1      5492    4700.1  143143.6     1%  schedule{name=Render}
   20123.3      5492    3664.1  325535.8     2%  schedule{name=Update}
   11470.3      5492    2088.5   22499.7     0%  schedule{name=PostUpdate}
   10729.7      5492    1953.7   22075.8     0%  system{name="bevy_render::renderer::render_system"}
   10714.6      5492    1951.0   22073.0     0%  main_render_schedule
    9883.2      5492    1799.6    9998.7     0%  schedule{name=RenderGraph}
    9871.6      4449    2218.8    6031.2     0%  system{name="ambition_platformer2d_actor_monolith::body::actors::update::tick_actor_brains
    6923.4      5492    1260.6    8532.1     0%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    6528.1     21116     309.2    4290.9     0%  schedule{name=Core2d}
    5617.1      5492    1022.8  118452.7     2%  sub app{name=RenderExtractApp}
    5047.8      5492     919.1   38706.7     1%  schedule{name=ExtractSchedule}
    4604.1   2769225       1.7    3151.5     0%  multithreaded executor
    3465.0         1 3464975.3 3464975.3   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
    3127.0      5492     569.4    5418.0     0%  schedule{name=RunFixedMainLoop}
    2756.5      5492     501.9    5339.4     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    2699.4      5843     462.0    4217.5     0%  schedule{name=FixedMain}
    2373.4      5843     406.2    4141.2     0%  schedule{name=FixedPostUpdate}
    2175.2      5492     396.1    2115.2     0%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    2007.6      5491     365.6    3162.6     0%  camera_schedule{camera="Camera 0 (500v0)"}
    1941.7      5492     353.5    6286.0     0%  schedule{name=Last}
    1748.9      5491     318.5    4164.1     0%  camera_schedule{camera="Camera 9 (501v0)"}
    1459.5      5843     249.8    3631.7     0%  system{name="avian2d::schedule::run_physics_schedule"}
    1420.5      1667     852.1    3605.7     0%  schedule{name=PhysicsSchedule}
    1384.3      5492     252.1     759.2     0%  system{name="bevy_render::extract_plugin::apply_extract_commands"}
    1370.7     21116      64.9     835.2     0%  RenderContextState::apply{system=bevy_core_pipeline::core_2d::main_transparent_pass_2d_nod
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
   79565.4      14490.2      5492    5271.4  update
   73931.1      13464.1      5492    4683.3  main app
   73903.0      13458.9      5492    4679.5  schedule{name=Main}
   34697.7       6319.0      5492     688.8  schedule{name=PreUpdate}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 5445 frames, summing to 16.66ms:

```text
    6.40 ms   38.5%  PreUpdate
    3.72 ms   22.4%  Update
    3.10 ms   18.6%  outside
    2.12 ms   12.7%  PostUpdate
    0.58 ms    3.5%  RunFixedMainLoop
    0.37 ms    2.2%  Last
    0.20 ms    1.2%  First
    0.08 ms    0.5%  StateTransition
    0.07 ms    0.4%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    6.40    16.72     2.61   PreUpdate
    3.72     6.36     1.71   Update
    3.10     5.81     1.88   outside
    2.12     3.97     1.87   PostUpdate
    0.58     1.15     1.97   RunFixedMainLoop
    0.37     0.60     1.60   Last
    0.20     0.48     2.44   First
    0.08     0.15     1.77   StateTransition
    0.07     0.11     1.51   SpawnScene
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
  56.4%  compiler / codegen / linker
  33.5%  the game itself
   9.6%  profiler (Tracy)
   0.2%  audio
   0.1%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  9.6%
codegen inside the capture: 56.4%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           : 33.5%
native attribution        : COMPILE-CONTAMINATED
```

⚠⚠ **The native profile below is COMPILE-CONTAMINATED and must not be quoted.**

⭐ Everything keyed to GAME TIME is unaffected — `frame_times.csv`,
`frame_spikes.csv`, `runtime_census.csv` and the image censuses come from the
game's own stderr census, not from `perf` samples, and a compile competes for
cores mostly before the game starts.

**A compile ran inside this capture** — 56% of sampled cycles in
rustc, LLVM codegen and linker threads, against the game's own 34%.
Check `warm-build.status` and the gap between `wall_s` and `game_s` in
`frame_spikes.csv`: a first frame tens of seconds into the capture is the
build. If the warm build ran and the launch rebuilt anyway, the two are
asking cargo for different fingerprints — see the `build_env` rows in
`run_game.sh --print-plan`.

## Where the native time went

```text
  89.8%  game binary + its Rust/C deps
   9.1%  kernel
   1.1%  GPU driver / graphics stack
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
     2.20%  Tracy Profiler   smash_match_profile                     [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                     
     0.96%  smash_match_pro  smash_match_profile                     [.] _RNvXs1_CsauKbtVXGAam_13tracing_tracyNtB5_10TracyLayerINtNtCshdbjT2rFsCC_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layer
     0.82%  smash_match_pro  smash_match_profile                     [.] _RNvXNtNtNtCs6STjheAsjvA_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run    
     0.79%  sprite-sheet-wa  libc.so.6                               [.] __memcmp_evex_movbe                                                                                                            
     0.78%  smash_match_pro  smash_match_profile                     [.] _mi_page_malloc_zero                                                                                                           
     0.74%  smash_match_pro  smash_match_profile                     [.] _RINvNtNtNtNtCskADBYZwJmSU_4core5slice4sort8unstable9quicksort9quicksortNtNtCsgFX3jaOk65H_5alloc6string6StringNCINvMB8_SB17_20s
     0.74%  smash_match_pro  libc.so.6                               [.] __memmove_evex_unaligned_erms                                                                                                  
     0.64%  smash_match_pro  smash_match_profile                     [.] _RNvXsb_NtCsgFX3jaOk65H_5alloc3vecINtB5_3VecNtCsd9yQwWwqCrt_21ambition_sprite_sheet8SheetRowENtNtCskADBYZwJmSU_4core5clone5Clon
     0.61%  Tracy Symbol Wo  smash_match_profile                     [.] tracy::NormalizePath(char const*)                                                                                              
     0.53%  smash_match_pro  smash_match_profile                     [.] _RNvMs_NtCs80ifbNxEow_12sharded_slab4poolINtB4_4PoolNtNtNtCshdbjT2rFsCC_18tracing_subscriber8registry7sharded9DataInnerE3getBT_
     0.50%  ld.mold          libc.so.6                               [.] __memmove_evex_unaligned_erms                                                                                                  
     0.48%  smash_match_pro  smash_match_profile                     [.] _RINvNtNtNtCskADBYZwJmSU_4core5slice4sort8unstable7ipnsortNtNtCsgFX3jaOk65H_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by
     0.39%  rustc            librustc_driver-7ed0375ae352bd75.so     [.] _RNvMs2_NtNtCsdSHT1xazwJC_12rustc_middle2ty7contextNtB5_13CtxtInterners9intern_ty                                              
     0.35%  Tracy Symbol Wo  smash_match_profile                     [.] tracy::rpmalloc(unsigned long)                                                                                                 
     0.34%  Compute Task Po  libc.so.6                               [.] __memmove_evex_unaligned_erms                                                                                                  
     0.34%  smash_match_pro  smash_match_profile                     [.] _RNvMNtNtCshdbjT2rFsCC_18tracing_subscriber5layer7contextINtB2_7ContextINtNtB4_7layered7LayeredINtNtCs7BIg33Flzay_13tracing_err
     0.34%  Tracy Symbol Wo  smash_match_profile                     [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsigned long,
     0.34%  Tracy Profiler   libc.so.6                               [.] __strlen_evex                                                                                                                  
     0.33%  rustc            librustc_driver-7ed0375ae352bd75.so     [.] _RINvNtCshycNWnLvI7D_17rustc_codegen_ssa3mir11codegen_mirINtNtCs9fqAePeZKqF_18rustc_codegen_llvm7builder14GenericBuilderNtNtBX_
     0.33%  ld.mold          libc.so.6                               [.] __strlen_evex                                                                                                                  
     0.32%  Compute Task Po  smash_match_profile                     [.] _RNvXs1_CsauKbtVXGAam_13tracing_tracyNtB5_10TracyLayerINtNtCshdbjT2rFsCC_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layer
     0.32%  sprite-sheet-wa  smash_match_profile                     [.] _RINvMs_NtNtNtCsgFX3jaOk65H_5alloc11collections5btree6searchINtNtB7_4node7NodeRefNtNtBY_6marker3MutNtNtBb_6string6StringINtNtB7
     0.30%  ld.mold          mold                                    [.] 0x0000000000da1341                                                                                                             
     0.29%  Tracy Symbol Wo  smash_match_profile                     [.] tracy::report_inlined_functions(unsigned long, tracy::function*, char const*, int (*)(void*, unsigned long, unsigned long, char
     0.29%  ld.mold          [kernel.kallsyms]                       [k] native_queued_spin_lock_slowpath                                                                                               
     0.29%  Tracy Profiler   libc.so.6                               [.] __memmove_evex_unaligned_erms                                                                                                  
     0.29%  smash_match_pro  smash_match_profile                     [.] mi_theap_umalloc                                                                                                               
     0.28%  Tracy Profiler   smash_match_profile                     [.] tracy::rpfree(void*)                                                                                                           
     0.27%  Tracy Profiler   smash_match_profile                     [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                      
```

## Assets and render resources

- Decoded images: 139 → 987 (466.8 MP, 1867.1 MB of decode work).
- Images resident at end: 867.
- **Resident image BYTES at end: 1867.1 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **829 images (368.5 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 42 GENERATED image(s) were allocated during gameplay (54.6 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 89 notable decode(s) (223.1 MP) landed AFTER the first `room-loaded` (frame 54) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.7MP  f     57  content://ambition_shared/__art/bundles/4771c5923143e4ecf2a9ba53dc3f275034d15d63cf44003ae2880cd4573a0e2a/runtime/full/sprites/tea_room_spritesheet.png
   1.4MP  f     57  content://ambition_shared/__art/bundles/d62b73e891247880a6344b9a51711468a6cd2a889877bbd586694cf339b695dc/runtime/full/sprites/bakery_room_spritesheet.png
   4.1MP  f     57  content://ambition_shared/__art/bundles/c3bb3e114f14bf51680fc3db1f9e41af05c66e05cc6445b5cf61529692431ef2/runtime/full/sprites/hanoi_temple_spritesheet.png
   1.1MP  f     57  content://ambition_shared/__art/bundles/e9b2b9177a17424f4d9bbe814290d8f3555e4969d9345fdb84aab56d14186d12/runtime/full/sprites/hoard_room_spritesheet.png
   1.8MP  f     57  content://ambition_shared/__art/bundles/6a3532230602c226a3c430262a0d45ddd79d228769e0672be71804af8116b09c/runtime/full/sprites/hill_east_spritesheet.png
   1.3MP  f     57  content://ambition_shared/__art/bundles/7b2e18c59fd353969aa1ef3a7dab32478b27a46fb01246109215292bfac1350c/runtime/full/sprites/castle_cells_room_spritesheet.png
   1.6MP  f     57  content://ambition_shared/__art/bundles/111c06cf11ae5067bd28e6003d7d00b16b1b43b595ff6ef2bb006ad46f25ac35/runtime/full/sprites/hill_west_spritesheet.png
   1.7MP  f     57  content://ambition_shared/__art/bundles/cae0fd7bc351b2c87cd37b630551ea98a1d6f9b3e01db40e58db4a7a57bcf11c/runtime/full/sprites/inn_room_spritesheet.png
```

⚠ 89 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  43x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 2062780 bytes

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
| `alloc_by_span.csv` | perf_probe: allocations per Bevy system / tracing span | no |
| `churn_by_site.csv` | perf_probe: spawns, despawns, inserts, removes per source line | no |
| `changes_by_site.csv` | perf_probe: component and resource values marked changed, per source line | no |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

