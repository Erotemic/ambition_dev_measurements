# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `70898d77b75e` on `main` |
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

Observed span of the game's own log: **57.1s**.

## Frame time

48 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      3.5       3   330.6   353.1   430.2   430.2    430.2
      4.5      56    17.3    15.0    19.5    21.5    116.7
     42.0      29    35.3    35.0    44.6    50.2     50.2
     43.0      29    34.5    34.0    41.4    47.5     47.5
     30.8      32    31.2    30.1    43.5    46.1     46.1
     35.9      36    27.6    26.3    36.6    45.9     45.9
     26.8      40    25.8    25.8    34.9    44.2     44.2
     44.0      36    27.7    26.7    41.8    44.1     44.1
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    7.529s     430.2 ms
    7.099s     353.1 ms
    6.746s     208.5 ms
    7.661s     116.7 ms
   34.008s      46.1 ms
   30.229s      44.3 ms
   34.406s      44.0 ms
   34.253s      43.5 ms
   35.604s      43.3 ms
   28.264s      41.8 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      2.1        4       3      1       0      1
      4.5        4       3      1       0      1
      6.5        4       3      1       0      1
      8.5        4       3      1       0      1
     10.5        4       3      1       0      1
     12.5        4       3      1       0      1
     14.6        4       3      1       0      1
     16.6        4       3      1       0      1
     18.6        4       3      1       0      1
     20.7        4       3      1       0      1
     22.7        4       3      1       0      1
     24.7        4       3      1       0      1
     26.8        4       3      1       0      1
     28.8        4       3      1       0      1
     30.8        4       3      1       0      1
     32.8        4       3      1       0      1
     34.9        4       3      1       0      1
     36.9        4       3      1       0      1
     38.9        4       3      1       0      1
     40.9        4       3      1       0      1
     43.0        4       3      1       0      1
     45.0        4       3      1       0      1
     47.0        4       3      1       0      1
     49.0        4       3      1       0      1
     51.0        4       3      1       0      1
```

Peak world-rendering cameras: **1** at t=2.1s.

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

Peak active portal capture rigs: **0** of 0 at t=2.1s.

```text
        t  rigs  active  budget
      2.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      6.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     14.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     22.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     26.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     30.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     34.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     38.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     43.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     47.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     51.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=2.1s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         110        0        0
       end       2048         184        0        0
      peak       2048         184        0        0
```

Peak sprites: **0** (0 visible), text2d 0, per-view projections 0 at t=2.1s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3171** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.144          0.225       48  render/ui/elapsed_gpu
         0.042          0.067       47  render/upscaling/elapsed_gpu
         0.037          0.060       47  render/msaa_writeback/elapsed_gpu
         0.026          0.074       48  render/ui/elapsed_cpu
         0.019          0.680       48  render/main_transparent_pass_2d/elapsed_cpu
         0.008          0.046       48  render/main_transparent_pass_2d/elapsed_gpu
         0.004          0.019       48  render/main_opaque_pass_2d/elapsed_gpu
         0.001          0.005       48  render/main_opaque_pass_2d/elapsed_cpu
         0.001          0.003       47  render/upscaling/elapsed_cpu
         0.001          0.003       47  render/msaa_writeback/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   2954789.188    4759403.000       48  render/ui/fragment_shader_invocations
   1440000.000    1440000.000       47  render/msaa_writeback/fragment_shader_invocations
   1440000.000    1440000.000       47  render/upscaling/fragment_shader_invocations
    210131.250    1440900.000       48  render/main_transparent_pass_2d/fragment_shader_invocations
       629.333        680.000       48  render/ui/vertex_shader_invocations
       314.667        340.000       48  render/ui/clipper_invocations
       286.292        310.000       48  render/ui/clipper_primitives_out
         3.000          3.000       47  render/msaa_writeback/vertex_shader_invocations
         3.000          3.000       47  render/upscaling/vertex_shader_invocations
         1.000          1.000       47  render/msaa_writeback/clipper_primitives_out
         1.000          1.000       47  render/upscaling/clipper_primitives_out
         1.000          1.000       47  render/msaa_writeback/clipper_invocations
         1.000          1.000       47  render/upscaling/clipper_invocations
         0.583          4.000       48  render/main_transparent_pass_2d/vertex_shader_invocations
         0.292          2.000       48  render/main_transparent_pass_2d/clipper_invocations
         0.292          2.000       48  render/main_transparent_pass_2d/clipper_primitives_out
         0.000          0.000       48  render/main_opaque_pass_2d/clipper_invocations
         0.000          0.000       48  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       48  render/main_opaque_pass_2d/vertex_shader_invocations
         0.000          0.000       48  render/main_opaque_pass_2d/fragment_shader_invocations
```

- CPU pass timings: **measured** (5 spans).
- GPU pass timings: **measured** (5 spans).
- Pipeline statistics: **measured** (20 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)


`perf` cannot produce this: a Bevy system is not a native symbol, and a
render pass is a graph node rather than a function. Counts matter as much
as totals -- a cheap zone entered ten thousand times is a scheduling
problem, not a slow function.

## Whole session, ranked by total time

```text
  total_ms     count   mean_us    max_us  worst  zone
   49701.7      2213   22459.0  929618.7     2%  update
   45716.6      2213   20658.2  872823.5     2%  main app
   45698.9      2213   20650.2  872818.1     2%  schedule{name=Main}
   20768.1      2213    9384.6  437100.2     2%  sub app{name=RenderApp}
   20737.6      2213    9370.8  437086.4     2%  schedule{name=Render}
   11399.3      2214    5148.7   43932.6     0%  schedule{name=RunFixedMainLoop}
   11367.0      2214    5134.1  327148.1     3%  schedule{name=Update}
   10082.1      2214    4553.8   43335.4     0%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
   10018.5      3141    3189.6   18982.7     0%  schedule{name=FixedMain}
    8670.3    917026       9.5   11340.5     0%  multithreaded executor
    8199.4      2214    3703.5  129451.1     2%  schedule{name=PostUpdate}
    7433.7      3141    2366.7   11307.1     0%  schedule{name=FixedPostUpdate}
    6198.8      2213    2801.1   93350.4     2%  system{name="bevy_render::renderer::render_system"}
    5221.2      2214    2358.3   11203.3     0%  schedule{name=PreUpdate}
    3957.1      2213    1788.1  381543.8    10%  sub app{name=RenderExtractApp}
    3741.7      3141    1191.2    5618.9     0%  system{name="avian2d::schedule::run_physics_schedule"}
    3662.8      3141    1166.1    4651.9     0%  schedule{name=PhysicsSchedule}
    3059.3      2213    1382.4  124614.7     4%  schedule{name=ExtractSchedule}
    2379.4      2213    1075.2   29843.4     1%  run_graph{name="main_graph"}
    2006.3      2215     905.8    9241.1     0%  schedule{name=StateTransition}
    1864.0      3141     593.4    4009.4     0%  system{name="avian2d::dynamics::solver::schedule::run_substep_schedule"}
    1837.5      4428     415.0    6551.7     0%  system{name="leafwing_input_manager::systems::update_action_state<ambition_input::actions:
    1778.6     18846      94.4    3417.0     0%  schedule{name=SubstepSchedule}
    1535.9      3141     489.0   11459.9     1%  schedule{name=FixedFirst}
    1365.3      2213     616.9   88201.3     6%  submit_graph_commands
    1316.3         1 1316338.2 1316338.2   100%  plugin cleanup
    1313.7         1 1313679.1 1313679.1   100%  plugin cleanup{plugin="bevy_rich_text3d::Text3dPlugin"}
    1133.2      2213     512.1   37082.4     3%  command_buffer_generation_tasks
    1084.2      2214     489.7    7296.8     1%  schedule{name=FramePhaseMark(2)}
     941.9      2214     425.4    5708.8     1%  schedule{name=ProcessLdtkApi}
     927.9      2213     419.3    4178.9     0%  system{name="bevy_render::apply_extract_commands"}
     924.0      6639     139.2    4756.5     1%  node{name="bevy_render::render_graph::node::ViewNodeRunner<bevy_core_pipeline::core_2d::ma
     867.5      3141     276.2    4912.7     1%  schedule{name=FixedLast}
     858.7         1  858748.2  858748.2   100%  plugin build{plugin="bevy_render::RenderPlugin"}
     824.3      2213     372.5    5148.2     1%  run_graph{name="Core2d" debug_group="Camera 0 (5v0)"}
     784.8      2213     354.6    4829.8     1%  run_graph{name="Core2d" debug_group="Camera 7 (3v0)"}
     702.8      2213     317.6    2796.6     0%  schedule{name=Last}
     697.0      2214     314.8    3664.1     1%  system{name="bevy_egui::run_egui_context_pass_loop_system"}
     689.0      2214     311.2    6794.8     1%  schedule{name=FramePhaseMark(0)}
     640.5         1  640547.7  640547.7   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
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
   48772.1      22048.8      2213    7854.8  update
   44843.7      20272.9      2213    7476.9  main app
   44826.1      20265.0      2213    7472.8  schedule{name=Main}
   20331.0       9191.2      2213    2786.8  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 2182 frames, summing to 22.46ms:

```text
    5.75 ms   25.6%  Update
    5.32 ms   23.7%  RunFixedMainLoop
    3.90 ms   17.4%  PostUpdate
    2.74 ms   12.2%  PreUpdate
    2.31 ms   10.3%  outside
    1.27 ms    5.6%  StateTransition
    0.48 ms    2.1%  First
    0.46 ms    2.0%  Last
    0.24 ms    1.1%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  71.4%  the game itself
  27.1%  profiler (Tracy)
   1.1%  build launcher (cargo, shell)
   0.4%  audio
```

```text
profiler (Tracy) overhead : 27.1%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  1.1%   (cargo and shell; NOT a compile)
the game itself           : 71.4%
native attribution        : PROFILER-CONTAMINATED
```

⚠⚠ **The native profile below is PROFILER-CONTAMINATED and must not be quoted.**

⚠ The game's own census is NOT a way around this. `frame_times.csv`,
`frame_spikes.csv` and `runtime_census.csv` are recorded by a process the
profiler is running inside, so they carry the same inflation the native
profile does. Only RATIOS between them survive.

**Tracy cost 27% of sampled cycles.** Its symbol-resolution and
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
  77.4%  game binary + its Rust/C deps
  19.0%  kernel
   3.4%  GPU driver / graphics stack
   0.2%  audio
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
     3.91%  Tracy Profiler   ambition_game_bin                      [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                      
     3.26%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtNtCs4NRVxsYgnAr_4core5slice4sort8unstable9quicksort9quicksortNtNtCscdodAO9FK5_5alloc6string6StringNCINvMB8_SB17_20sor
     2.47%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtCs4NRVxsYgnAr_4core5slice4sort8unstable7ipnsortNtNtCscdodAO9FK5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_k
     2.01%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     1.93%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     1.83%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_Cs55zrdcupEh6_13tracing_tracyNtB5_10TracyLayerINtNtCs6uRcRnqR4J_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layered
     1.61%  Tracy Symbol Wo  libc.so.6                              [.] _dl_addr                                                                                                                        
     1.57%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCs77wS6V9rGzO_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     1.23%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtCsfWB9K5UC3R_12sharded_slab4poolINtB4_4PoolNtNtNtCs6uRcRnqR4J_18tracing_subscriber8registry7sharded9DataInnerE3getBT_  
     1.04%  Tracy Profiler   libc.so.6                              [.] __strlen_evex                                                                                                                   
     1.02%  ambition_game_b  ambition_game_bin                      [.] mi_theap_umalloc                                                                                                                
     1.02%  ambition_game_b  [kernel.kallsyms]                      [k] perf_adjust_freq_unthr_context                                                                                                  
     1.01%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::NormalizePath(char const*)                                                                                               
     0.85%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::read_function_entry(tracy::backtrace_state*, tracy::dwarf_data*, tracy::unit*, unsigned long, tracy::dwarf_buf*, tracy::l
     0.85%  ambition_game_b  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.83%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::backtrace_qsort(void*, unsigned long, unsigned long, int (*)(void const*, void const*)) [clone .localalias]              
     0.80%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.70%  Tracy Sampling   ambition_game_bin                      [.] tracy::SysTraceWorker(void*)                                                                                                    
     0.65%  ambition_game_b  ambition_game_bin                      [.] _RNvXs4_NtNtCs6uRcRnqR4J_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscdodAO9FK5_5alloc5boxed3BoxDINtNtB9_5la
     0.65%  Compute Task Po  ambition_game_bin                      [.] _RNvXs1_Cs55zrdcupEh6_13tracing_tracyNtB5_10TracyLayerINtNtCs6uRcRnqR4J_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layered
     0.60%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                       
     0.59%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsigned long, 
     0.57%  Tracy Profiler   libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.55%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::read_attribute(tracy::dwarf_form, unsigned long, tracy::dwarf_buf*, int, int, int, tracy::dwarf_sections const*, tracy::d
     0.53%  Compute Task Po  ambition_game_bin                      [.] _RNvMs1_NtNtNtCs77wS6V9rGzO_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.49%  ambition_game_b  ambition_game_bin                      [.] _mi_theap_realloc_zero                                                                                                          
     0.49%  Tracy Symbol Wo  [kernel.kallsyms]                      [k] clear_page_erms                                                                                                                 
     0.46%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.45%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy::Que
     0.43%  Tracy Symbol Wo  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.42%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::_rpmalloc_allocate_from_heap_fallback(tracy::heap_t*, tracy::heap_size_class_t*, unsigned int)                           
     0.41%  ambition_game_b  ambition_game_bin                      [.] mi_free                                                                                                                         
     0.40%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::line_compare(void const*, void const*)                                                                                   
     0.40%  ambition_game_b  ambition_game_bin                      [.] _RNvNtCs4NRVxsYgnAr_4core3fmt5write                                                                                             
     0.39%  ambition_game_b  ambition_game_bin                      [.] _RNvXsh_NtCs4NRVxsYgnAr_4core3fmteNtB5_5Debug3fmt                                                                               
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
- `perf-record`: 143
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 528596 bytes

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
| `schedule_phases.csv` | per-frame milliseconds in each main-schedule phase | yes |
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | yes |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | yes |
| `tracy.trace` | the raw Tracy trace, for the GUI | yes |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

