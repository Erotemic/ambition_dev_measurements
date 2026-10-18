# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `569abd9da128` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.95.0 (59807616e 2026-04-14)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile --jobs 6 --autoplay ` |
| host | `aivm-2404` |
| kernel | `Linux aivm-2404 6.8.0-110-generic #110-Ubuntu SMP PREEMPT_DYNAMIC Thu Mar 19 15:09:20 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `ambition-autoplay` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=12
- MemTotal:       65841160 kB

## Renderer

```text
AdapterInfo { name: "llvmpipe (LLVM 20.1.2, 256 bits)", vendor: 65541, device: 0, device_type: Cpu, device_pci_bus_id: "", driver: "llvmpipe", driver_info: "Mesa 25.2.8-0ubuntu0.24.04.2 (LLVM 20.1.2)", backend: Vulkan, subgroup_min_size: 8, subgroup_max_size: 8, transient_saves_memory: false }
```

**SOFTWARE RENDERING — READ THIS BEFORE THE NUMBERS BELOW.**

This run had no GPU: every pixel was rasterized on the CPU. Expect the
bulk of samples in llvmpipe/lavapipe threads and in unsymbolized `[JIT]`
frames, which are the rasterizer's runtime-compiled shaders -- perf can
never attribute those to a pass, a material, or a draw call. Game-code
symbols in this profile describe only the few percent left over.

This is NOT a measurement of GPU rendering performance, and it must not
be reported as one. Check `host-environment.txt` for why no GPU adapter
was selected (missing ICD, `VK_*`/`WGPU_*` override, no DRM render node,
headless session).

## Session

Observed span of the game's own log: **121.9s**.

## Frame time

83 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     41.3       1  1505.9  1505.9  1505.9  1505.9   1505.9
     16.8       7   366.0   143.7  1419.2  1419.2   1419.2
     40.0       2  1047.5  1279.8  1279.8  1279.8   1279.8
     45.9       2  1087.2  1267.1  1267.1  1267.1   1267.1
     36.6       2   928.6  1091.4  1091.4  1091.4   1091.4
     47.3       1  1023.9  1023.9  1023.9  1023.9   1023.9
     42.5       1   963.9   963.9   963.9   963.9    963.9
     44.3       2   820.4   903.9   903.9   903.9    903.9
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   21.088s     714.9 ms
   20.374s     471.7 ms
   19.901s     433.0 ms
   25.866s     378.9 ms
   21.692s     342.8 ms
   22.161s     339.9 ms
   28.021s     311.7 ms
   30.065s     311.3 ms
   26.385s     308.2 ms
   24.990s     305.1 ms
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
    1      271     True  central_hub_complex -> hall_of_characters
```

Frames over 33.4 ms AFTER the last transition was logged (t=44.020s): **0**.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

⛔ **THE SPIKE LOG HIT ITS 60-LINE CAP, so this count is a FLOOR, not a total.** Percentile summaries continued; the per-frame lines did not.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 556 images / 95.9 MP in total; 14 were notable enough to print.

```text
ordered by: frame
before central_hub_complex: 4 image(s), 20.5 MP
  unknown                  2    16.4 MP
  vanity-card              1     3.0 MP
  character-sheet          1     1.1 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.9        5       3      1       0      1
      5.5        5       3      1       0      1
     10.0        5       3      1       0      1
     14.3        5       3      1       0      1
     19.3        5       3      1       0      1
     23.6        5       3      1       0      1
     28.0       11       9      1       6      1
     32.7       11       9      1       6      1
     38.1       11       9      1       6      1
     44.3       11       9      1       6      1
     49.6       11       9      1       6      1
     54.6       11       9      1       6      1
     59.0       11       9      1       6      1
     63.9       11       9      1       6      1
     68.6       11       9      1       6      1
     73.7       11       9      1       6      1
     79.0       11       9      1       6      1
     83.7       11       9      1       6      1
     89.0       11       9      1       6      1
     93.8       11       9      1       6      1
     98.1       11       9      1       6      1
```

Peak world-rendering cameras: **1** at t=0.9s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
       offscreen  rigged impostor camera, rigged impostor unpremultiply camera
           other  Cube pause camera, Cube scrim display camera, Developer Panel Camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=0.9s.

```text
        t  rigs  active  budget
      0.9     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
      8.9     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     16.8     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     24.8     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     32.7     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     42.5     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     51.9     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     60.0     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     68.6     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     77.6     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     86.5     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
     94.9     0       0  res<=384 depth=0 captures<=1 updates/frame<=1
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 576px) at t=27.0s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1644        0        0
       end      16384        2233      159        1
      peak      16384        2233      159        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **4713** (395 visible), text2d 35, per-view projections 9 at t=26.0s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3755** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
        16.489         76.486       82  render/ui/elapsed_gpu
        15.996         68.090       81  render/upscaling/elapsed_gpu
         8.916         32.378       60  render/main_opaque_pass_2d/elapsed_gpu
         2.235          9.894       82  render/main_transparent_pass_2d/elapsed_gpu
         0.113          0.582       60  render/main_opaque_pass_2d/elapsed_cpu
         0.076          0.691       82  render/ui/elapsed_cpu
         0.010          0.283       82  render/main_transparent_pass_2d/elapsed_cpu
         0.008          0.077       81  render/upscaling/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    900734.537    4752090.000       82  render/ui/fragment_shader_invocations
    331776.000     331776.000       60  render/main_opaque_pass_2d/fragment_shader_invocations
       733.634       1480.000       82  render/ui/vertex_shader_invocations
       365.805        738.000       82  render/ui/clipper_primitives_out
       365.805        738.000       82  render/ui/clipper_invocations
         4.000          4.000       60  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       60  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       60  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       82  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       82  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       82  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       82  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (4 spans).
- GPU pass timings: **measured** (4 spans).
- Pipeline statistics: **measured** (12 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

> **Timer caveat.** this CPU does not advertise an invariant TSC (constant_tsc + nonstop_tsc); TRACY_NO_INVARIANT_CHECK=1 was set so the game could run. Tracy zone durations here come from a timer the kernel does not vouch for: treat their RATIOS as sound and their absolute microseconds as approximate.


`perf` cannot produce this: a Bevy system is not a native symbol, and a
render pass is a graph node rather than a function. Counts matter as much
as totals -- a cheap zone entered ten thousand times is a scheduling
problem, not a slow function.

## Whole session, ranked by total time

```text
  total_ms     count   mean_us    max_us  worst  zone
  102510.2         1 102510215.6 102510215.6   100%  bevy_app
  102357.2         1 102357188.4 102357188.4   100%  render thread
  101506.1       269  377346.0 1394898.2     1%  update
   86084.6       269  320017.3 1329143.1     2%  main app
   86076.5       269  319987.2 1329136.1     2%  schedule{name=Main}
   85780.1       269  318885.0 1338321.9     2%  sub app{name=RenderApp}
   85750.2       269  318774.1 1338296.7     2%  schedule{name=RenderRecovery}
   85456.9       269  317683.5 1338161.5     2%  system{name="bevy_render::run_render_schedule"}
   85416.0       269  317531.4 1338016.8     2%  schedule{name=Render}
   74155.2       269  275669.8 1177239.6     2%  system{name="bevy_render::renderer::render_system"}
   74136.2       269  275599.3 1177228.2     2%  main_render_schedule
   67239.7       269  249961.6 1108120.2     2%  schedule{name=PreUpdate}
   65820.3       269  244685.1 1093959.3     2%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   58251.4      2987   19501.6  744207.2     1%  ggrs{name="HandleRequests"}
   58100.6      2987   19451.2  744183.1     1%  schedule{name="AdvanceWorld"}
   57966.4      2987   19406.2  744132.0     1%  schedule{name=AdvanceWorld}
   57476.1      2987   19242.1  743931.6     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_rollback_g
   57389.4      2987   19213.1  743919.8     1%  schedule{name=GgrsSchedule}
   15404.0       269   57263.8  523164.2     3%  sub app{name=RenderExtractApp}
   13011.3         1 13011334.2 13011334.2   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
    9210.9         1 9210946.6 9210946.6   100%  plugin build{plugin="ambition_content::plugin::AmbitionContentPlugin"}
    7127.9      2987    2386.3   46443.7     1%  schedule{name=ReadInputs}
    6740.8       269   25058.6  635402.1     9%  schedule{name=Update}
    6420.0       269   23866.3  336099.0     5%  schedule{name=RenderGraph}
    4251.2       269   15803.7  108243.8     3%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    4065.8      1860    2185.9   61981.6     2%  schedule{name=Core2d}
    3391.5      2987    1135.4   16335.8     0%  system{name="ambition_platformer2d_actor_monolith::features::ecs::actors::update::integrat
    3329.4       269   12376.9  120076.7     4%  schedule{name=PostUpdate}
    2634.5      2987     882.0   27970.5     1%  system{name="ambition_platformer2d_actor_monolith::features::ecs::actors::update::tick_act
    2511.0    176649      14.2   18540.9     1%  multithreaded executor
    2405.2       269    8941.3   60684.5     3%  schedule{name=Last}
    2136.8       269    7943.6   84702.7     4%  schedule{name=RunFixedMainLoop}
    1984.0       269    7375.5   83055.2     4%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
    1947.2      4042     481.7   19943.7     1%  schedule{name=FixedMain}
    1875.3       269    6971.5  138794.2     7%  schedule{name=ExtractSchedule}
    1351.7       269    5024.9  307862.8    23%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    1302.4         1 1302392.7 1302392.7   100%  plugin build{plugin="bevy_render::RenderPlugin"}
    1265.1         3  421708.2 1264341.9   100%  asset loading{loader="bevy_kira_audio::source::ogg_loader::OggLoader" asset="audio/music/g
    1134.7         1 1134670.8 1134670.8   100%  plugin build{plugin="bevy_winit::WinitPlugin"}
    1044.5      4042     258.4   16048.7     2%  schedule{name=FixedPostUpdate}
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
  100111.2     373549.2       269  121679.9  update
   84755.5     316251.9       269   24335.1  main app
   84747.4     316221.7       269   24326.6  schedule{name=Main}
   84441.7     315081.1       269  116822.8  sub app{name=RenderApp}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 265 frames, summing to 379.73ms:

```text
  252.29 ms   66.4%  PreUpdate
   60.97 ms   16.1%  outside
   27.69 ms    7.3%  Update
   14.55 ms    3.8%  PostUpdate
   10.25 ms    2.7%  Last
    9.21 ms    2.4%  RunFixedMainLoop
    2.02 ms    0.5%  First
    1.47 ms    0.4%  StateTransition
    1.27 ms    0.3%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
  252.29   999.68     3.96   PreUpdate
   60.97   284.49     4.67   outside
   27.69   109.94     3.97   Update
   14.55    58.01     3.99   PostUpdate
   10.25    35.57     3.47   Last
    9.21    34.85     3.78   RunFixedMainLoop
    2.02     6.11     3.02   First
    1.47     5.39     3.65   StateTransition
    1.27     4.72     3.70   SpawnScene
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
  76.3%  the game itself
  23.2%  profiler (Tracy)
   0.4%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead : 23.2%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.4%   (cargo and shell; NOT a compile)
the game itself           : 76.3%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  47.1%  game binary + its Rust/C deps
  35.2%  software rasterizer (CPU emulating a GPU)
  17.7%  kernel
   0.0%  GPU driver / graphics stack
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     2.18%  Tracy Profiler   ambition_game_bin          [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                                  
     1.11%  ambition_game_b  ambition_game_bin          [.] _mi_page_malloc_zero                                                                                                                        
     1.00%  Tracy Symbol Wo  ambition_game_bin          [.] tracy::NormalizePath(char const*)                                                                                                           
     0.87%  ambition_game_b  ambition_game_bin          [.] <tracing_tracy::TracyLayer<C> as tracing_subscriber::layer::Layer<S>>::on_enter                                                             
     0.84%  Tracy Sampling   ambition_game_bin          [.] tracy::SysTraceWorker(void*)                                                                                                                
     0.83%  Tracy Symbol Wo  ambition_game_bin          [.] tracy::backtrace_qsort(void*, unsigned long, unsigned long, int (*)(void const*, void const*)) [clone .localalias]                          
     0.76%  Tracy Symbol Wo  ambition_game_bin          [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsigned long, char const*,
     0.76%  Tracy Symbol Wo  ambition_game_bin          [.] tracy::rpmalloc(unsigned long)                                                                                                              
     0.74%  Tracy Symbol Wo  ambition_game_bin          [.] tracy::read_function_entry(tracy::backtrace_state*, tracy::dwarf_data*, tracy::unit*, unsigned long, tracy::dwarf_buf*, tracy::line_header c
     0.72%  ambition_game_b  ambition_game_bin          [.] mi_theap_umalloc                                                                                                                            
     0.57%  ambition_game_b  [kernel.kallsyms]          [k] perf_adjust_freq_unthr_context                                                                                                              
     0.56%  ambition_game_b  ambition_game_bin          [.] core::slice::sort::unstable::quicksort::quicksort                                                                                           
     0.52%  ambition_game_b  ambition_game_bin          [.] sharded_slab::pool::Pool<T,C>::get                                                                                                          
     0.52%  ambition_game_b  ambition_game_bin          [.] core::slice::sort::unstable::ipnsort                                                                                                        
     0.50%  ambition_game_b  libc.so.6                  [.] 0x0000000000198953                                                                                                                          
     0.44%  Tracy Symbol Wo  ambition_game_bin          [.] ___tracy_demangle                                                                                                                           
     0.44%  ambition_game_b  ambition_game_bin          [.] <bevy_ecs::schedule::executor::single_threaded::SingleThreadedExecutor as bevy_ecs::schedule::executor::SystemExecutor>::run                
     0.42%  Tracy Symbol Wo  ambition_game_bin          [.] tracy::report_inlined_functions(unsigned long, tracy::function*, char const*, int (*)(void*, unsigned long, unsigned long, char const*, int,
     0.41%  ambition_game_b  ambition_game_bin          [.] tracy::rpmalloc(unsigned long)                                                                                                              
     0.38%  Tracy Symbol Wo  ambition_game_bin          [.] tracy::read_attribute(tracy::dwarf_form, unsigned long, tracy::dwarf_buf*, int, int, int, tracy::dwarf_sections const*, tracy::dwarf_data*, 
     0.37%  Tracy Profiler   ambition_game_bin          [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy::QueueItem*, uns
     0.36%  ambition_game_b  libc.so.6                  [.] 0x000000000019839e                                                                                                                          
     0.33%  Tracy Symbol Wo  ambition_game_bin          [.] tracy::_rpmalloc_allocate_from_heap_fallback(tracy::heap_t*, tracy::heap_size_class_t*, unsigned int)                                       
     0.33%  Tracy Symbol Wo  [kernel.kallsyms]          [k] asm_exc_page_fault                                                                                                                          
     0.32%  Tracy Profiler   ambition_game_bin          [.] tracy::rpfree(void*)                                                                                                                        
     0.31%  Tracy Profiler   libc.so.6                  [.] 0x000000000019b962                                                                                                                          
     0.30%  Tracy Symbol Wo  ambition_game_bin          [.] tracy::read_uleb128(tracy::dwarf_buf*)                                                                                                      
     0.27%  Tracy Symbol Wo  [kernel.kallsyms]          [k] perf_adjust_freq_unthr_context                                                                                                              
     0.26%  llvmpipe-2       [JIT] tid 501864           [.] 0x00007c192be7307e                                                                                                                          
     0.26%  ambition_game_b  libc.so.6                  [.] 0x00000000001983a5                                                                                                                          
     0.26%  Tracy Profiler   ambition_game_bin          [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                                   
     0.25%  ambition_game_b  ambition_game_bin          [.] tracing_subscriber::layer::context::Context<S>::if_enabled_for                                                                              
```

## Assets and render resources

- Decoded images: 127 → 556 (95.9 MP, 383.5 MB of decode work).
- Images resident at end: 411.
- **Resident image BYTES at end: 383.5 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **309 images (55.1 MP)** at 34.2s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 3 GENERATED image(s) were allocated during gameplay (10.7 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

✔ FIRST ROOM: no notable decode landed in the second after the first `room-loaded` (frame 66) with a LATER frame stamp — the first room's art, the player's sheet included, was in before the route activated.

⛔ **7 of 14 notable decodes landed during SETTLED play** (24.5 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  13.5MP  at 33.326s  sprites_0_5x/gnu_ton_boss/giant_gnu_spritesheet.png
   3.9MP  at 32.990s  sprites_0_5x/gnu_ton_scholar_fighter_spritesheet.png
   2.2MP  at 32.991s  sprites_0_5x/perfect_cellular_automaton_parts.png
   1.4MP  at 32.990s  sprites_0_5x/noether_parts.png
   1.4MP  at 32.990s  sprites_0_5x/flying_spaghetti_monster_boss_parts.png
   1.1MP  at 32.990s  sprites_0_5x/imperfect_cellular_automaton_parts.png
   1.0MP  at 32.990s  sprites_0_5x/wolfenstein_parts.png
```

Textures decoded more than once:

```text
   4x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 124
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 3436488 bytes

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

