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

Observed span of the game's own log: **82.4s**.

## Frame time

78 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     49.2      28    49.9    18.8    95.2   767.3    767.3
     46.8      15    74.0    19.1   341.9   457.9    457.9
      1.6       2   207.2   298.9   298.9   298.9    298.9
     50.2      81    12.0     9.3    10.6    50.4    264.5
     36.7      72    13.9     8.9    17.6   166.7    179.7
     45.8     104    10.0     8.7    17.0    34.8     94.6
      2.6     133     7.5     7.0     7.6     9.8     85.5
     47.8      66    14.5    11.8    27.1    72.6     72.7
```

Full series: `frame_times.csv`.

24 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   50.717s     767.2 ms
   48.294s     457.9 ms
   47.836s     341.9 ms
    3.056s     298.9 ms
   50.981s     264.5 ms
   37.801s     179.7 ms
   37.967s     166.7 ms
    2.757s     115.8 ms
   49.950s      95.2 ms
   47.253s      94.6 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.5        4       3      1       0      1
      3.6        4       3      1       0      1
      6.6        4       3      1       0      1
      9.6        4       3      1       0      1
     12.6        4       3      1       0      1
     15.6        4       3      1       0      1
     18.6        4       3      1       0      1
     21.7        4       3      1       0      1
     24.7        4       3      1       0      1
     27.7        4       3      1       0      1
     30.7        4       3      1       0      1
     33.7        4       3      1       0      1
     36.7       18       5      3       2      1
     39.7       18       5      3       2      1
     42.7        4       3      1       0      1
     45.8        4       3      1       0      1
     49.2        4       3      1       0      1
     52.2        4       3      1       0      1
     55.2        4       3      1       0      1
     58.2        4       3      1       0      1
     61.3        4       3      1       0      1
     64.3        4       3      1       0      1
     67.3        4       3      1       0      1
     70.3        4       3      1       0      1
     73.3        4       3      1       0      1
     76.3        4       3      1       0      1
     79.4        4       4      1       0      1
```

Peak world-rendering cameras: **3** at t=36.7s.

⭐ **The world was drawn 3 times in one frame at peak.** Only
cameras that draw the SIMULATED WORLD are counted — main gameplay, a
split-screen local view, a portal capture rig — so the HUD is not one of
these. Each is a full pass over the visible population. Check
`camera_views.csv` for which roles were live together and at what
resolution each drew.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
           other  Cube pause camera, Cube scrim display camera
  portal_capture  Portal view capture (c128), Portal view capture (c129), Portal view capture (c130), Portal view capt
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **2** of 14 at t=36.7s.

```text
        t  rigs  active  budget
      0.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      6.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     24.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     30.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     36.7    14       2  res<=2048 depth=1 captures<=4 updates/frame<=4
     42.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     49.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     55.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     61.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     67.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     73.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     79.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **14** (largest dimension 2048px) at t=36.7s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        2014      130        1
      peak       8192        2014      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **263** (51 visible), text2d 35, per-view projections 11 at t=45.8s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3235** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.126          0.126        1  render/clustering/elapsed_gpu
         0.070          0.070        1  render/main_opaque_pass_3d/elapsed_gpu
         0.050          0.088       77  render/msaa_writeback/elapsed_gpu
         0.044          0.096       77  render/ui/elapsed_gpu
         0.024          0.029       77  render/upscaling/elapsed_gpu
         0.023          0.023        1  render/early_mesh_preprocessing/elapsed_gpu
         0.021          0.021        1  render/clustering/elapsed_cpu
         0.017          0.017        1  render/bin_unpacking/elapsed_gpu
         0.016          0.016        1  render/main_opaque_pass_3d/elapsed_cpu
         0.014          0.048       77  render/ui/elapsed_cpu
         0.011          0.011        1  render/main_transparent_pass_3d/elapsed_gpu
         0.009          0.012       43  render/main_opaque_pass_2d/elapsed_cpu
         0.006          0.006        1  render/main_transparent_pass_3d/elapsed_cpu
         0.005          0.011       43  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.005       77  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.003        1  render/bin_unpacking/elapsed_cpu
         0.003          0.003        1  render/early_mesh_preprocessing/elapsed_cpu
         0.002          0.010       77  render/upscaling/elapsed_cpu
         0.002          0.006       77  render/msaa_writeback/elapsed_cpu
         0.001          0.002       77  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1430710.000    1430710.000        1  render/main_opaque_pass_3d/fragment_shader_invocations
    396079.675    4746363.000       77  render/ui/fragment_shader_invocations
     86494.581     520238.000       43  render/main_opaque_pass_2d/fragment_shader_invocations
     27236.000      27236.000        1  render/main_transparent_pass_3d/fragment_shader_invocations
      1125.000       1125.000        1  render/main_transparent_pass_3d/vertex_shader_invocations
       683.662        982.000       77  render/ui/vertex_shader_invocations
       555.000        555.000        1  render/main_transparent_pass_3d/clipper_invocations
       483.000        483.000        1  render/main_transparent_pass_3d/clipper_primitives_out
       340.831        490.000       77  render/ui/clipper_primitives_out
       340.831        490.000       77  render/ui/clipper_invocations
       144.000        144.000        1  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        1  render/early_mesh_preprocessing/compute_shader_invocations
        76.000         76.000        1  render/main_opaque_pass_3d/clipper_primitives_out
        72.000         72.000        1  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000        1  render/bin_unpacking/compute_shader_invocations
         8.279         12.000       43  render/main_opaque_pass_2d/vertex_shader_invocations
         4.279          8.000       43  render/main_opaque_pass_2d/clipper_invocations
         4.279          8.000       43  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       77  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       77  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       77  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       77  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (10 spans).
- GPU pass timings: **measured** (10 spans).
- Pipeline statistics: **measured** (22 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
tracy-capture produced no trace (PROTOCOL MISMATCH — tracy-capture and the game's compiled tracy-client are different Tracy versions. The game speaks whatever tracy-client-sys vendors (check TracyVersion.hpp in the vendored copy); install matching Tracy tools.)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 9185 frames, summing to 8.59ms:

```text
    2.58 ms   30.0%  Update
    2.40 ms   28.0%  PreUpdate
    1.58 ms   18.4%  PostUpdate
    1.00 ms   11.6%  outside
    0.48 ms    5.6%  RunFixedMainLoop
    0.20 ms    2.3%  Last
    0.13 ms    1.6%  StateTransition
    0.13 ms    1.5%  First
    0.09 ms    1.0%  SpawnScene
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    2.58     3.79    -1.21   Update
    2.40     6.37    -3.97   PreUpdate
    1.58     2.29    -0.70   PostUpdate
    1.00     2.36    -1.36   outside
    0.48     1.18    -0.70   RunFixedMainLoop
    0.20     0.22    -0.02   Last
    0.13     0.39    -0.25   StateTransition
    0.13     0.28    -0.15   First
    0.09     0.11    -0.02   SpawnScene
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
  98.5%  the game itself
   1.0%  profiler (Tracy)
   0.3%  audio
   0.3%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  1.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.3%   (cargo and shell; NOT a compile)
the game itself           : 98.5%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  74.3%  game binary + its Rust/C deps
  22.2%  kernel
   3.5%  GPU driver / graphics stack
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
     2.53%  ambition_game_b  ambition_game_bin                 [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     2.42%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20sort_un
     2.26%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     1.55%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_keyjN
     1.54%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     1.53%  ambition_game_b  ambition_game_bin                 [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_     
     1.27%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     1.26%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.78%  Compute Task Po  ambition_game_bin                 [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7LayeredINtN
     0.75%  ambition_game_b  ambition_game_bin                 [.] mi_free                                                                                                                              
     0.68%  ambition_game_b  ambition_game_bin                 [.] _RNvXs4_NtNtCshkcqoohQOEC_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5layer
     0.65%  ambition_game_b  ambition_game_bin                 [.] _RNvMs2_NtCsbH7joPaZV8S_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCs43kaUkgDg0C_14ambition_input7actions31Platformer2dInpu
     0.61%  Compute Task Po  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.58%  ambition_game_b  ambition_game_bin                 [.] mi_theap_umalloc                                                                                                                     
     0.58%  ambition_game_b  ambition_game_bin                 [.] _RNvMs_NtNtCshkcqoohQOEC_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                            
     0.58%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.54%  ambition_game_b  ambition_game_bin                 [.] _RNvNtCsc36rpYXAlPq_4core3fmt5write                                                                                                  
     0.50%  ambition_game_b  ambition_game_bin                 [.] ___tracy_emit_zone_begin_alloc                                                                                                       
     0.49%  Compute Task Po  ambition_game_bin                 [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_     
     0.45%  Compute Task Po  ambition_game_bin                 [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCshswzoL9RzC8_10bevy_tasks9task_poolNtB19_8TaskPool12new_
     0.44%  ambition_game_b  ambition_game_bin                 [.] _RNvXs1_NtNtCshkcqoohQOEC_18tracing_subscriber8registry7shardedNtB5_8RegistryNtB7_10LookupSpan9span_data                             
     0.43%  ambition_game_b  ambition_game_bin                 [.] _RINvMs_NtNtCshkcqoohQOEC_18tracing_subscriber6filter3envNtB5_9EnvFilter8on_enterINtNtNtB9_5layer7layered7LayeredINtNtCsc36rpYXAlPq_4
     0.43%  ambition_game_b  ambition_game_bin                 [.] _RNvXsh_NtCsc36rpYXAlPq_4core3fmteNtB5_5Debug3fmt                                                                                    
     0.39%  ambition_game_b  ambition_game_bin                 [.] mi_theap_malloc_aligned                                                                                                              
     0.39%  ambition_game_b  ambition_game_bin                 [.] _RINvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB5_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE11create_wi
     0.37%  Compute Task Po  [kernel.kallsyms]                 [k] perf_adjust_freq_unthr_context                                                                                                       
     0.35%  Compute Task Po  ambition_game_bin                 [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC8_10b
     0.31%  ambition_game_b  [kernel.kallsyms]                 [k] native_irq_return_iret                                                                                                               
     0.31%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort6shared9smallsort18small_sort_generalNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB1f_2
     0.31%  ambition_game_b  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     0.31%  ambition_game_b  [kernel.kallsyms]                 [k] perf_adjust_freq_unthr_context                                                                                                       
     0.29%  ambition_game_b  ambition_game_bin                 [.] _mi_theap_realloc_zero                                                                                                               
     0.29%  Compute Task Po  ambition_game_bin                 [.] _RNvNtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                       
     0.28%  ambition_game_b  ambition_game_bin                 [.] _RNvXs0_NtNtCshkcqoohQOEC_18tracing_subscriber8registry7shardedNtB5_8RegistryNtNtCseHOddNEP2U7_12tracing_core10subscriber10Subscriber
     0.28%  Compute Task Po  ambition_game_bin                 [.] _RNvMs_NtNtCshkcqoohQOEC_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                            
```

## Assets and render resources

- Decoded images: 0 → 308 (568.6 MP, 2274.4 MB of decode work).
- Images resident at end: 276.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **127 images (88.8 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 87 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **13 of 122 notable decodes landed during SETTLED play** (133.0 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  16.8MP  at 48.485s  sprites/perfect_cellular_automaton_spritesheet.1.png
  16.7MP  at 48.390s  sprites/perfect_cellular_automaton_spritesheet.2.png
  16.7MP  at 49.254s  sprites/perfect_cellular_automaton_spritesheet.5.png
  16.5MP  at 49.254s  sprites/perfect_cellular_automaton_spritesheet.3.png
  15.9MP  at 48.485s  sprites/perfect_cellular_automaton_spritesheet.4.png
  15.7MP  at 48.485s  sprites/perfect_cellular_automaton_spritesheet.png
   9.4MP  at 49.254s  sprites/perfect_cellular_automaton_spritesheet.6.png
   7.2MP  at 49.254s  sprites/patent_clerk_spritesheet.png
   6.5MP  at 49.254s  sprites/player_robot_v2_spritesheet.png
   5.7MP  at 49.254s  sprites/robot_spritesheet.png
```

Textures decoded more than once:

```text
  29x  <runtime-generated> — allocated during gameplay. No asset path, so this is generated (an atlas or a render target), not content that could have been demanded earlier.
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 806264 bytes

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

