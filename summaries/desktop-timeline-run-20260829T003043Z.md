# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `106313ac0ecf` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.97.1 (8bab26f4f 2026-07-14)` |
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
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, driver: "NVIDIA", driver_info: "595.84", backend: Vulkan }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **5111.6s**.

## Frame time

3533 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
   1627.4       1  1042.7  1042.7  1042.7  1042.7   1042.7
   4485.4       1  1039.4  1039.4  1039.4  1039.4   1039.4
   3278.4       2  1009.4  1036.2  1036.2  1036.2   1036.2
   3958.4       1  1036.1  1036.1  1036.1  1036.1   1036.1
   4241.4       2  1001.3  1035.9  1035.9  1035.9   1035.9
   4360.4       2  1015.2  1034.9  1034.9  1034.9   1034.9
   3845.4       1  1033.8  1033.8  1033.8  1033.8   1033.8
   3631.4       1  1033.3  1033.3  1033.3  1033.3   1033.3
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
  781.048s    1024.7 ms
  793.042s    1018.0 ms
  762.040s    1017.6 ms
  760.042s    1015.8 ms
  784.034s    1014.7 ms
  754.039s    1014.6 ms
  738.034s    1013.9 ms
  743.037s    1013.5 ms
  756.040s    1012.4 ms
  752.036s    1011.1 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.5        4       3      1       0      1
    177.2        4       3      1       0      1
    353.9        4       3      1       0      1
    530.5        4       3      1       0      1
    707.2        4       3      1       0      1
    972.4        4       3      1       0      1
   1249.4        4       3      1       0      1
   1532.4        4       3      1       0      1
   1801.4        4       3      1       0      1
   2077.4        4       3      1       0      1
   2357.4        4       3      1       0      1
   2633.4        4       3      1       0      1
   2904.4        4       3      1       0      1
   3174.4        4       3      1       0      1
   3451.4        4       3      1       0      1
   3715.4        4       3      1       0      1
   3993.4        4       3      1       0      1
   4260.4        4       3      1       0      1
   4539.4        4       3      1       0      1
   4820.4        4       3      1       0      1
   5088.4        4       3      1       0      1
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
      0.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
    295.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    590.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    975.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
   1443.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
   1899.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
   2363.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
   2822.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
   3276.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
   3725.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
   4179.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
   4647.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
   5101.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         110        0        0
       end       2048         189        0        0
      peak       2048         189        0        0
```

Peak sprites: **0** (0 visible), text2d 0, per-view projections 0 at t=0.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3161** in 28 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.275          0.337     3532  render/ui/elapsed_gpu
         0.095          0.124     3532  render/msaa_writeback/elapsed_gpu
         0.077          0.245     3532  render/main_transparent_pass_2d/elapsed_gpu
         0.068          0.085     3532  render/upscaling/elapsed_gpu
         0.015          0.184     3532  render/ui/elapsed_cpu
         0.013          0.170     3532  render/main_transparent_pass_2d/elapsed_cpu
         0.007          0.031     3532  render/main_opaque_pass_2d/elapsed_gpu
         0.003          0.124     3532  render/main_opaque_pass_2d/elapsed_cpu
         0.001          0.064     3532  render/upscaling/elapsed_cpu
         0.001          0.054     3532  render/msaa_writeback/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   2391405.409    4010010.000     3532  render/ui/fragment_shader_invocations
   1296163.080    1440000.000     3532  render/msaa_writeback/fragment_shader_invocations
   1296163.080    1440000.000     3532  render/upscaling/fragment_shader_invocations
    538743.007    1440900.000     3532  render/main_transparent_pass_2d/fragment_shader_invocations
       681.762        684.000     3532  render/ui/vertex_shader_invocations
       340.881        342.000     3532  render/ui/clipper_invocations
       309.611        342.000     3532  render/ui/clipper_primitives_out
         3.000          3.000     3532  render/msaa_writeback/vertex_shader_invocations
         3.000          3.000     3532  render/upscaling/vertex_shader_invocations
         1.661          4.000     3532  render/main_transparent_pass_2d/vertex_shader_invocations
         1.000          1.000     3532  render/msaa_writeback/clipper_primitives_out
         1.000          1.000     3532  render/upscaling/clipper_primitives_out
         1.000          1.000     3532  render/msaa_writeback/clipper_invocations
         1.000          1.000     3532  render/upscaling/clipper_invocations
         0.831          2.000     3532  render/main_transparent_pass_2d/clipper_invocations
         0.831          2.000     3532  render/main_transparent_pass_2d/clipper_primitives_out
         0.000          0.000     3532  render/main_opaque_pass_2d/clipper_invocations
         0.000          0.000     3532  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000     3532  render/main_opaque_pass_2d/vertex_shader_invocations
         0.000          0.000     3532  render/main_opaque_pass_2d/fragment_shader_invocations
```

- CPU pass timings: **measured** (5 spans).
- GPU pass timings: **measured** (5 spans).
- Pipeline statistics: **measured** (20 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy artifacts in this bundle.

## Which phase of the frame owned the time

UNAVAILABLE — no `[census] phases` rows in this bundle. The phase marks
are registered only when `AMBITION_PROFILE_CENSUS` is set at App build
time, so a run that enabled the census later has none.

## Observer effect (what the profiler itself cost)

```text
  84.3%  the game itself
  14.3%  profiler (Tracy)
   1.4%  audio
   0.0%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead : 14.3%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.0%   (cargo and shell; NOT a compile)
the game itself           : 84.3%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  80.3%  game binary + its Rust/C deps
  15.2%  kernel
   4.0%  GPU driver / graphics stack
   0.5%  audio
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
     5.73%  Tracy Profiler   ambition_game_bin                      [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                      
     5.34%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtNtCs4NRVxsYgnAr_4core5slice4sort8unstable9quicksort9quicksortNtNtCscdodAO9FK5_5alloc6string6StringNCINvMB8_SB17_20sor
     3.57%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtCs4NRVxsYgnAr_4core5slice4sort8unstable7ipnsortNtNtCscdodAO9FK5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_k
     2.44%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_Cs55zrdcupEh6_13tracing_tracyNtB5_10TracyLayerINtNtCs6uRcRnqR4J_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layered
     1.98%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     1.68%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtCsfWB9K5UC3R_12sharded_slab4poolINtB4_4PoolNtNtNtCs6uRcRnqR4J_18tracing_subscriber8registry7sharded9DataInnerE3getBT_  
     1.51%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCs77wS6V9rGzO_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     1.20%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.90%  Compute Task Po  ambition_game_bin                      [.] _RNvXs1_Cs55zrdcupEh6_13tracing_tracyNtB5_10TracyLayerINtNtCs6uRcRnqR4J_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layered
     0.80%  ambition_game_b  ambition_game_bin                      [.] _RNvMs2_NtCskOO3K8vaNOI_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCs5U6yvC0EKwv_14ambition_input7actions31Platformer2
     0.75%  ambition_game_b  ambition_game_bin                      [.] mi_free                                                                                                                         
     0.73%  ambition_game_b  ambition_game_bin                      [.] mi_theap_umalloc                                                                                                                
     0.73%  ambition_game_b  ambition_game_bin                      [.] ___tracy_emit_zone_begin_alloc                                                                                                  
     0.70%  Compute Task Po  ambition_game_bin                      [.] _RNvMs1_NtNtNtCs77wS6V9rGzO_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.67%  ambition_game_b  ambition_game_bin                      [.] _RNvXs4_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscdodAO9FK5_5alloc5boxed3BoxDINtNtB9_5la
     0.66%  Tracy Sampling   ambition_game_bin                      [.] tracy::SysTraceWorker(void*)                                                                                                    
     0.66%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                       
     0.61%  Tracy Profiler   libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.57%  Compute Task Po  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.56%  ambition_game_b  ambition_game_bin                      [.] _RNvXsh_NtCs4NRVxsYgnAr_4core3fmteNtB5_5Debug3fmt                                                                               
     0.56%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_NtNtCs6uRcRnqR4J_18tracing_subscriber8registry7shardedNtB5_8RegistryNtB7_10LookupSpan9span_data                         
     0.55%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.53%  ambition_game_b  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.50%  ambition_game_b  ambition_game_bin                      [.] _RINvMs_NtCsfWB9K5UC3R_12sharded_slab4poolINtB5_4PoolNtNtNtCs6uRcRnqR4J_18tracing_subscriber8registry7sharded9DataInnerE11create
     0.49%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                        
     0.46%  ambition_game_b  ambition_game_bin                      [.] _RNvNtCs4NRVxsYgnAr_4core3fmt5write                                                                                             
     0.44%  Compute Task Po  ambition_game_bin                      [.] _RNvMs_NtCsfWB9K5UC3R_12sharded_slab4poolINtB4_4PoolNtNtNtCs6uRcRnqR4J_18tracing_subscriber8registry7sharded9DataInnerE3getBT_  
     0.40%  Compute Task Po  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.38%  Compute Task Po  ambition_game_bin                      [.] _RNvXs5_NtCs2DDHhGaqmAk_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCslpdWifUVxKZ_10bevy_tasks9task_poolNtB19_8TaskPool1
     0.38%  ambition_game_b  ambition_game_bin                      [.] mi_theap_malloc_aligned                                                                                                         
     0.38%  ambition_game_b  ambition_game_bin                      [.] _mi_theap_realloc_zero                                                                                                          
     0.36%  ambition_game_b  ambition_game_bin                      [.] _RINvMs_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter3envNtB5_9EnvFilter8on_enterINtNtNtB9_5layer7layered7LayeredINtNtCs4NRVxsYgn
     0.35%  Compute Task Po  ambition_game_bin                      [.] _RNvMs_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter3envNtB4_9EnvFilter16cares_about_span                                        
     0.35%  Tracy Profiler   ambition_game_bin                      [.] tracy::rpfree(void*)                                                                                                            
     0.33%  Compute Task Po  ambition_game_bin                      [.] _RNvMs2_CshYE0FKR1MA6_16concurrent_queueINtB5_15ConcurrentQueueNtNtCsdye8XfgCOIZ_10async_task8runnable8RunnableE3popCslpdWifUVxK
```

## Assets and render resources

- Decoded images: 0 → 132 (97.5 MP, 390.1 MB of decode work).
- Images resident at end: 131.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

⚠ This bundle predates late-decode marking, so whether any decode landed during gameplay is UNKNOWN here, not zero.

Textures decoded more than once:

```text
   2x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 10475776 bytes

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
| `image_arrivals.csv` | images reaching Assets<Image> per census window | no |
| `world_events.csv` | room loads and session starts/ends, with game time | no |
| `schedule_census.csv` | registered system counts per sample | yes |
| `schedule_phases.csv` | per-frame milliseconds in each main-schedule phase | no |
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | no |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | no |
| `tracy.trace` | the raw Tracy trace, for the GUI | yes |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

