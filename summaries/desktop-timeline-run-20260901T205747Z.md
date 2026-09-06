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

Observed span of the game's own log: **36.7s**.

## Frame time

32 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     14.0      32    30.8    10.2    26.0   621.3    621.3
     10.9      11    92.0    23.6   468.4   468.4    468.4
     13.0      35    33.5    15.9    98.7   327.4    327.4
      1.8      26    24.6     7.1   185.6   226.2    226.2
     11.9      57    17.4    12.4    37.5    49.0    144.3
      9.8      90    11.4     9.0    18.3    34.8    140.6
      6.8     128     8.3     7.1     7.9    35.6    103.7
      7.8     119     8.3     8.5     9.7    24.7     27.3
```

Full series: `frame_times.csv`.

25 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   15.718s     621.3 ms
   12.811s     468.4 ms
   15.096s     327.4 ms
   12.342s     238.7 ms
    3.589s     226.2 ms
    3.362s     186.1 ms
   13.631s     144.3 ms
   11.919s     140.5 ms
   14.170s     106.2 ms
    8.863s     103.7 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.8        4       3      1       0      1
      1.8        4       3      1       0      1
      2.8        4       3      1       0      1
      3.8        4       3      1       0      1
      4.8        4       3      1       0      1
      5.8        4       3      1       0      1
      6.8        4       3      1       0      1
      7.8        4       3      1       0      1
      8.8        4       3      1       0      1
      9.8        4       3      1       0      1
     10.9        4       3      1       0      1
     11.9        4       3      1       0      1
     13.0        4       3      1       0      1
     14.0        4       3      1       0      1
     15.0        4       3      1       0      1
     16.0        4       3      1       0      1
     17.0        4       3      1       0      1
     18.0        4       3      1       0      1
     19.0        4       3      1       0      1
     20.1        4       3      1       0      1
     21.1        4       3      1       0      1
     22.1        4       3      1       0      1
     23.1        4       3      1       0      1
     24.1        4       3      1       0      1
     25.1        4       3      1       0      1
     26.1        4       3      1       0      1
     27.1        4       3      1       0      1
     28.1        4       3      1       0      1
     29.1        4       3      1       0      1
     30.1        4       3      1       0      1
     31.1        4       3      1       0      1
     32.1        4       4      1       0      1
     33.1        4       4      1       0      1
```

Peak world-rendering cameras: **1** at t=0.8s.

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

Peak active portal capture rigs: **0** of 0 at t=0.8s.

```text
        t  rigs  active  budget
      0.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      2.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      4.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      6.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      8.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     13.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     15.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     17.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     19.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     21.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     23.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     25.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     27.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     29.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     31.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     33.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.8s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        1969        2        1
      peak       8192        1969      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **256** (44 visible), text2d 35, per-view projections 11 at t=9.8s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3235** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.118          0.118        2  render/clustering/elapsed_gpu
         0.052          0.058        2  render/main_opaque_pass_3d/elapsed_gpu
         0.042          0.054       32  render/msaa_writeback/elapsed_gpu
         0.039          0.069       32  render/ui/elapsed_gpu
         0.021          0.027       32  render/upscaling/elapsed_gpu
         0.021          0.022        2  render/early_mesh_preprocessing/elapsed_gpu
         0.018          0.018        2  render/bin_unpacking/elapsed_gpu
         0.017          0.017        2  render/clustering/elapsed_cpu
         0.016          0.016        2  render/main_opaque_pass_3d/elapsed_cpu
         0.013          0.022       32  render/ui/elapsed_cpu
         0.010          0.010        2  render/main_transparent_pass_3d/elapsed_gpu
         0.009          0.013        2  render/main_transparent_pass_3d/elapsed_cpu
         0.006          0.008        2  render/bin_unpacking/elapsed_cpu
         0.004          0.004       32  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.003        2  render/early_mesh_preprocessing/elapsed_cpu
         0.002          0.003       32  render/msaa_writeback/elapsed_cpu
         0.002          0.003       32  render/upscaling/elapsed_cpu
         0.001          0.004       32  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1408838.500    1465129.000        2  render/main_opaque_pass_3d/fragment_shader_invocations
    529633.219    4651828.000       32  render/ui/fragment_shader_invocations
     26149.000      27365.000        2  render/main_transparent_pass_3d/fragment_shader_invocations
      1293.000       1381.000        2  render/main_transparent_pass_3d/vertex_shader_invocations
       642.000        683.000        2  render/main_transparent_pass_3d/clipper_invocations
       616.250        702.000       32  render/ui/vertex_shader_invocations
       454.000        495.000        2  render/main_transparent_pass_3d/clipper_primitives_out
       307.125        350.000       32  render/ui/clipper_primitives_out
       307.125        350.000       32  render/ui/clipper_invocations
       152.000        164.000        2  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        2  render/early_mesh_preprocessing/compute_shader_invocations
        80.000         86.000        2  render/main_opaque_pass_3d/clipper_primitives_out
        76.000         82.000        2  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000        2  render/bin_unpacking/compute_shader_invocations
         0.000          0.000       32  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       32  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       32  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       32  render/main_transparent_pass_2d/clipper_primitives_out
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

Mean milliseconds per frame over 3351 frames, summing to 9.65ms:

```text
    2.67 ms   27.6%  Update
    2.60 ms   26.9%  PreUpdate
    1.66 ms   17.1%  outside
    1.63 ms   16.9%  PostUpdate
    0.52 ms    5.4%  RunFixedMainLoop
    0.21 ms    2.2%  Last
    0.14 ms    1.5%  StateTransition
    0.14 ms    1.4%  First
    0.09 ms    1.0%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    2.67     4.16    -1.50   Update
    2.60     7.01    -4.41   PreUpdate
    1.66     3.77    -2.12   outside
    1.63     2.45    -0.82   PostUpdate
    0.52     1.38    -0.85   RunFixedMainLoop
    0.21     0.24    -0.04   Last
    0.14     0.41    -0.27   StateTransition
    0.14     0.31    -0.17   First
    0.09     0.13    -0.03   SpawnScene
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
  97.9%  the game itself
   1.2%  profiler (Tracy)
   0.6%  build launcher (cargo, shell)
   0.3%  audio
```

```text
profiler (Tracy) overhead :  1.2%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.6%   (cargo and shell; NOT a compile)
the game itself           : 97.9%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  65.0%  game binary + its Rust/C deps
  31.7%  kernel
   3.2%  GPU driver / graphics stack
   0.0%  software rasterizer (CPU emulating a GPU)
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
     2.00%  ambition_game_b  ambition_game_bin                  [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20sort_u
     1.89%  ambition_game_b  ambition_game_bin                  [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINt
     1.74%  ambition_game_b  libc.so.6                          [.] __memmove_evex_unaligned_erms                                                                                                       
     1.45%  ambition_game_b  ambition_game_bin                  [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_    
     1.45%  Compute Task Po  libc.so.6                          [.] __memmove_evex_unaligned_erms                                                                                                       
     1.21%  ambition_game_b  ambition_game_bin                  [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_keyj
     1.07%  ambition_game_b  ambition_game_bin                  [.] _RNvXNtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run         
     1.05%  Compute Task Po  [kernel.kallsyms]                  [k] native_queued_spin_lock_slowpath                                                                                                    
     0.98%  ambition_game_b  ambition_game_bin                  [.] _mi_page_malloc_zero                                                                                                                
     0.71%  Compute Task Po  ambition_game_bin                  [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINt
     0.67%  Compute Task Po  [kernel.kallsyms]                  [k] isolate_freepages_block                                                                                                             
     0.58%  ambition_game_b  ambition_game_bin                  [.] _RNvMs2_NtCsbH7joPaZV8S_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCs43kaUkgDg0C_14ambition_input7actions31Platformer2dInp
     0.58%  ambition_game_b  ambition_game_bin                  [.] mi_free                                                                                                                             
     0.54%  Compute Task Po  [kernel.kallsyms]                  [k] isolate_migratepages_block                                                                                                          
     0.51%  Compute Task Po  ambition_game_bin                  [.] _mi_page_malloc_zero                                                                                                                
     0.48%  IO Task Pool (3  [kernel.kallsyms]                  [k] isolate_migratepages_block                                                                                                          
     0.48%  Compute Task Po  ambition_game_bin                  [.] _RNvMs1_NtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                 
     0.46%  ambition_game_b  ambition_game_bin                  [.] mi_theap_umalloc                                                                                                                    
     0.46%  ambition_game_b  ambition_game_bin                  [.] _RNvXs1_NtNtCshkcqoohQOEC_18tracing_subscriber8registry7shardedNtB5_8RegistryNtB7_10LookupSpan9span_data                            
     0.46%  Async Compute T  [kernel.kallsyms]                  [k] native_queued_spin_lock_slowpath                                                                                                    
     0.44%  IO Task Pool (1  [kernel.kallsyms]                  [k] isolate_migratepages_block                                                                                                          
     0.43%  Compute Task Po  ambition_game_bin                  [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_    
     0.43%  ambition_game_b  ambition_game_bin                  [.] _RNvMs_NtNtCshkcqoohQOEC_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                           
     0.42%  ambition_game_b  ambition_game_bin                  [.] _RNvXs4_NtNtCshkcqoohQOEC_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5laye
     0.40%  ambition_game_b  ambition_game_bin                  [.] ___tracy_emit_zone_begin_alloc                                                                                                      
     0.40%  ambition_game_b  libc.so.6                          [.] __memcmp_evex_movbe                                                                                                                 
     0.40%  ambition_game_b  ambition_game_bin                  [.] _RNvXsh_NtCsc36rpYXAlPq_4core3fmteNtB5_5Debug3fmt                                                                                   
     0.36%  Compute Task Po  ambition_game_bin                  [.] ___tracy_emit_zone_begin_alloc                                                                                                      
     0.34%  IO Task Pool (0  ambition_game_bin                  [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                
     0.34%  IO Task Pool (3  ambition_game_bin                  [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                                
     0.32%  Compute Task Po  ambition_game_bin                  [.] _RNvMs_NtNtCshkcqoohQOEC_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                           
     0.32%  IO Task Pool (1  [kernel.kallsyms]                  [k] native_queued_spin_lock_slowpath                                                                                                    
     0.32%  ambition_game_b  ambition_game_bin                  [.] mi_theap_malloc_aligned                                                                                                             
     0.32%  Compute Task Po  ambition_game_bin                  [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCshswzoL9RzC8_10bevy_tasks9task_poolNtB19_8TaskPool12new
     0.32%  ambition_game_b  [nvidia]                           [k] _nv046188rm                                                                                                                         
```

## Assets and render resources

- Decoded images: 0 → 270 (535.3 MP, 2141.4 MB of decode work).
- Images resident at end: 268.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **127 images (88.8 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 59 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **13 of 94 notable decodes landed during SETTLED play** (133.0 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  16.8MP  at 12.715s  sprites/perfect_cellular_automaton_spritesheet.1.png
  16.7MP  at 13.044s  sprites/perfect_cellular_automaton_spritesheet.5.png
  16.7MP  at 13.044s  sprites/perfect_cellular_automaton_spritesheet.2.png
  16.5MP  at 13.044s  sprites/perfect_cellular_automaton_spritesheet.3.png
  15.9MP  at 13.044s  sprites/perfect_cellular_automaton_spritesheet.4.png
  15.7MP  at 12.715s  sprites/perfect_cellular_automaton_spritesheet.png
   9.4MP  at 12.554s  sprites/perfect_cellular_automaton_spritesheet.6.png
   7.2MP  at 13.044s  sprites/patent_clerk_spritesheet.png
   6.5MP  at 13.044s  sprites/player_robot_v2_spritesheet.png
   5.7MP  at 13.044s  sprites/robot_spritesheet.png
```

94 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 443096 bytes

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

