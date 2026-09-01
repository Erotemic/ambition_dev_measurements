# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `3ee53ca0fdd5` on `main` |
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

Observed span of the game's own log: **30.6s**.

## Frame time

21 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     19.9       2   650.2   968.2   968.2   968.2    968.2
      6.4       9   109.2    16.4   658.0   658.0    658.0
     14.5       7   167.1    72.5   597.7   597.7    597.7
      5.3       1   295.0   295.0   295.0   295.0    295.0
     16.6      24    44.5    35.1    87.9   195.2    195.2
     18.7      17    69.2    63.4   132.7   176.5    176.5
     10.4      38    27.1    17.7    57.3   168.7    168.7
     15.5      17    52.9    35.2   154.0   160.9    160.9
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    9.134s     657.6 ms
   17.761s     597.6 ms
    8.477s     296.4 ms
   17.164s     237.7 ms
    9.352s     218.3 ms
   12.880s     168.6 ms
   17.922s     161.0 ms
   18.488s     154.0 ms
   16.593s      90.5 ms
   16.681s      89.9 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      4.3        4       3      1       0      1
      5.3        4       3      1       0      1
      6.4        4       3      1       0      1
      7.4        4       3      1       0      1
      8.4        4       3      1       0      1
      9.4        4       3      1       0      1
     10.4        4       3      1       0      1
     11.4        4       3      1       0      1
     12.4        4       3      1       0      1
     13.4        4       3      1       0      1
     14.5        4       3      1       0      1
     15.5        4       3      1       0      1
     16.6        4       3      1       0      1
     17.6        4       3      1       0      1
     18.7        4       3      1       0      1
     19.9        4       3      1       0      1
     20.9        4       3      1       0      1
     22.0        4       3      1       0      1
     23.0        4       3      1       0      1
     24.0        4       3      1       0      1
     25.0        4       4      1       0      1
     26.0        4       4      1       0      1
```

Peak world-rendering cameras: **1** at t=4.3s.

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

Peak active portal capture rigs: **0** of 0 at t=4.3s.

```text
        t  rigs  active  budget
      4.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      5.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      6.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      7.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      8.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     11.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     13.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     14.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     15.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     16.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     17.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     19.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     20.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     22.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     23.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     24.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     25.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     26.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=4.3s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1923      130        1
      peak       8192        1923      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **268** (68 visible), text2d 35, per-view projections 11 at t=12.4s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3234** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.151          0.151        1  render/clustering/elapsed_gpu
         0.097          0.097        1  render/main_opaque_pass_3d/elapsed_gpu
         0.053          0.074       20  render/msaa_writeback/elapsed_gpu
         0.052          0.076       20  render/ui/elapsed_gpu
         0.043          0.043        1  render/main_opaque_pass_3d/elapsed_cpu
         0.040          0.073       20  render/ui/elapsed_cpu
         0.028          0.028        1  render/clustering/elapsed_cpu
         0.026          0.026        1  render/early_mesh_preprocessing/elapsed_gpu
         0.025          0.034       20  render/upscaling/elapsed_gpu
         0.020          0.020        1  render/bin_unpacking/elapsed_gpu
         0.015          0.015        1  render/main_transparent_pass_3d/elapsed_gpu
         0.012          0.012        1  render/main_transparent_pass_3d/elapsed_cpu
         0.007          0.020       20  render/msaa_writeback/elapsed_cpu
         0.005          0.005        1  render/early_mesh_preprocessing/elapsed_cpu
         0.005          0.005        1  render/bin_unpacking/elapsed_cpu
         0.004          0.008       20  render/upscaling/elapsed_cpu
         0.003          0.004       20  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.005       20  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1340617.000    1340617.000        1  render/main_opaque_pass_3d/fragment_shader_invocations
    778518.650    4651720.000       20  render/ui/fragment_shader_invocations
     27067.000      27067.000        1  render/main_transparent_pass_3d/fragment_shader_invocations
      1177.000       1177.000        1  render/main_transparent_pass_3d/vertex_shader_invocations
       631.400        698.000       20  render/ui/vertex_shader_invocations
       581.000        581.000        1  render/main_transparent_pass_3d/clipper_invocations
       477.000        477.000        1  render/main_transparent_pass_3d/clipper_primitives_out
       314.700        348.000       20  render/ui/clipper_primitives_out
       314.700        348.000       20  render/ui/clipper_invocations
       164.000        164.000        1  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        1  render/early_mesh_preprocessing/compute_shader_invocations
        86.000         86.000        1  render/main_opaque_pass_3d/clipper_primitives_out
        82.000         82.000        1  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000        1  render/bin_unpacking/compute_shader_invocations
         0.000          0.000       20  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       20  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       20  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       20  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (9 spans).
- GPU pass timings: **measured** (9 spans).
- Pipeline statistics: **measured** (18 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
tracy-capture produced no trace (PROTOCOL MISMATCH — tracy-capture and the game's compiled tracy-client are different Tracy versions. The game speaks whatever tracy-client-sys vendors (check TracyVersion.hpp in the vendored copy); install matching Tracy tools.)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 711 frames, summing to 30.57ms:

```text
   10.36 ms   33.9%  PreUpdate
    6.74 ms   22.1%  outside
    6.07 ms   19.8%  Update
    3.37 ms   11.0%  PostUpdate
    2.29 ms    7.5%  RunFixedMainLoop
    0.55 ms    1.8%  Last
    0.51 ms    1.7%  StateTransition
    0.41 ms    1.3%  First
    0.28 ms    0.9%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
   10.36    22.36   -12.00   PreUpdate
    6.74    13.26    -6.51   outside
    6.07    11.50    -5.43   Update
    3.37     5.45    -2.08   PostUpdate
    2.29     3.99    -1.70   RunFixedMainLoop
    0.55     0.84    -0.29   Last
    0.51     0.94    -0.43   StateTransition
    0.41     0.88    -0.48   First
    0.28     0.38    -0.11   SpawnScene
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
  95.3%  the game itself
   2.7%  profiler (Tracy)
   1.4%  build launcher (cargo, shell)
   0.6%  audio
```

```text
profiler (Tracy) overhead :  2.7%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  1.4%   (cargo and shell; NOT a compile)
the game itself           : 95.3%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  54.4%  game binary + its Rust/C deps
  42.4%  kernel
   3.1%  GPU driver / graphics stack
   0.1%  audio
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     1.82%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     1.34%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     1.29%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     1.24%  ambition_game_b  ambition_game_bin                 [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     1.12%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     1.08%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20sort_un
     0.87%  IO Task Pool (1  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.85%  ambition_game_b  ambition_game_bin                 [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_     
     0.83%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_keyjN
     0.78%  IO Task Pool (0  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.76%  ambition_game_b  ambition_game_bin                 [.] mi_theap_umalloc                                                                                                                     
     0.73%  IO Task Pool (0  ambition_game_bin                 [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                 
     0.67%  IO Task Pool (2  ambition_game_bin                 [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                 
     0.65%  ambition_game_b  [kernel.kallsyms]                 [k] perf_adjust_freq_unthr_context                                                                                                       
     0.62%  Compute Task Po  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.59%  IO Task Pool (2  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.58%  ambition_game_b  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.53%  ambition_game_b  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     0.52%  ambition_game_b  [nvidia]                          [k] _nv046188rm                                                                                                                          
     0.52%  Tracy Sampling   ambition_game_bin                 [.] tracy::SysTraceWorker(void*)                                                                                                         
     0.50%  IO Task Pool (3  ambition_game_bin                 [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                 
     0.48%  IO Task Pool (3  [kernel.kallsyms]                 [k] isolate_freepages_block                                                                                                              
     0.47%  ambition_game_b  [kernel.kallsyms]                 [k] clear_page_erms                                                                                                                      
     0.47%  ambition_game_b  ambition_game_bin                 [.] _RNvXs4_NtNtCshkcqoohQOEC_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5layer
     0.46%  IO Task Pool (0  ambition_game_bin                 [.] _RNvNtNtCsiOYQA2dFZzS_3png6filter5paeth8unfilter                                                                                     
     0.45%  IO Task Pool (2  ambition_game_bin                 [.] _RNvNtNtCsiOYQA2dFZzS_3png6filter5paeth8unfilter                                                                                     
     0.45%  Compute Task Po  [kernel.kallsyms]                 [k] isolate_migratepages_block                                                                                                           
     0.44%  IO Task Pool (1  ambition_game_bin                 [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                 
     0.43%  IO Task Pool (1  [kernel.kallsyms]                 [k] __reset_isolation_pfn                                                                                                                
     0.42%  IO Task Pool (3  [kernel.kallsyms]                 [k] __reset_isolation_pfn                                                                                                                
     0.40%  ambition_game_b  [kernel.kallsyms]                 [k] copy_page                                                                                                                            
     0.38%  Compute Task Po  [kernel.kallsyms]                 [k] copy_page                                                                                                                            
     0.36%  ambition_game_b  ambition_game_bin                 [.] ___tracy_emit_zone_begin_alloc                                                                                                       
     0.35%  IO Task Pool (3  ambition_game_bin                 [.] _RNvNtNtCsiOYQA2dFZzS_3png6filter5paeth8unfilter                                                                                     
     0.35%  IO Task Pool (0  [kernel.kallsyms]                 [k] native_queued_spin_lock_slowpath                                                                                                     
```

## Assets and render resources

- Decoded images: 0 → 267 (533.5 MP, 2134.0 MB of decode work).
- Images resident at end: 266.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **81 images (262.6 MP)** at 20.2s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 24 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **48 of 93 notable decodes landed during SETTLED play** (227.5 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  16.8MP  at 19.107s  sprites/perfect_cellular_automaton_spritesheet.1.png
  16.7MP  at 18.778s  sprites/perfect_cellular_automaton_spritesheet.2.png
  16.7MP  at 19.107s  sprites/perfect_cellular_automaton_spritesheet.5.png
  16.5MP  at 19.107s  sprites/perfect_cellular_automaton_spritesheet.3.png
  15.9MP  at 19.107s  sprites/perfect_cellular_automaton_spritesheet.4.png
  15.7MP  at 18.642s  sprites/perfect_cellular_automaton_spritesheet.png
  14.9MP  at 15.400s  sprites/gnu_ton_boss/giant_gnu_hands_spritesheet.png
   9.4MP  at 19.107s  sprites/perfect_cellular_automaton_spritesheet.6.png
   7.2MP  at 20.078s  sprites/patent_clerk_spritesheet.png
   6.5MP  at 19.107s  sprites/player_robot_v2_spritesheet.png
```

93 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 352976 bytes

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
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | no |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | no |
| `tracy.trace` | the raw Tracy trace, for the GUI | no |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

