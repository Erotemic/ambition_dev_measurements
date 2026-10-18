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
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile --autoplay ` |
| host | `toothbrush` |
| kernel | `Linux toothbrush 6.8.0-147-generic #147-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep 24 11:33:16 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `ambition-autoplay` |

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

Observed span of the game's own log: **120.2s**.

## Frame time

113 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     15.7       2   484.1   851.1   851.1   851.1    851.1
      2.3       2   336.7   496.4   496.4   496.4    496.4
     14.5      51    26.6    16.8    63.5   375.5    375.5
     84.7      28    35.5    24.4    97.0   129.3    129.3
     16.7      25    36.9    29.5    67.6   122.0    122.0
     41.2      24    44.1    33.1    97.8   110.8    110.8
     17.7      17    60.9    58.3    79.6    98.7     98.7
    100.9      25    41.7    38.2    63.3    98.0     98.0
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   18.589s     852.1 ms
    5.258s     495.2 ms
   17.737s     375.9 ms
    4.763s     178.3 ms
   18.828s     123.2 ms
   17.264s     117.8 ms
   18.707s     116.1 ms
   19.761s      96.1 ms
   21.470s      95.1 ms
   21.269s      90.0 ms
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
    1       21     True  central_hub_complex -> hall_of_characters
    2       43     True  hall_of_characters -> central_hub_complex
    3       30     True  central_hub_complex -> primeval_trailhead
    4       25     True  primeval_trailhead -> primeval_run
    5       35     True  primeval_run -> primeval_rookery
    6       36     True  primeval_rookery -> primeval_ridge
    7       21     True  primeval_ridge -> primeval_canopy
    8       49     True  primeval_canopy -> primeval_tyrant_hall
    9       28     True  primeval_tyrant_hall -> primeval_canopy
   10       27     True  primeval_canopy -> primeval_ridge
   11       21     True  primeval_ridge -> primeval_rookery
   12       18     True  primeval_rookery -> primeval_run
   13       30     True  primeval_run -> primeval_trailhead
   14       31     True  primeval_trailhead -> central_hub_complex
   15       19     True  central_hub_complex -> hall_of_characters
   16       29     True  hall_of_characters -> central_hub_complex
   17       26     True  central_hub_complex -> primeval_trailhead
   18       20     True  primeval_trailhead -> primeval_run
   19       33     True  primeval_run -> primeval_rookery
```

Frames over 33.4 ms AFTER the last transition was logged (t=116.588s): **0**.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

⛔ **THE SPIKE LOG HIT ITS 60-LINE CAP, so this count is a FLOOR, not a total.** Percentile summaries continued; the per-frame lines did not.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1085 images / 444.5 MP in total; 111 were notable enough to print.

```text
ordered by: frame
before central_hub_complex: 7 image(s), 25.8 MP
  unknown                  2    16.4 MP
  character-sheet          2     3.2 MP
  vanity-card              1     3.0 MP
  boss-sheet               1     2.0 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.3        5       3      1       0      1
      6.4        5       3      1       0      1
     11.5        5       3      1       0      1
     16.7        5       3      1       0      1
     21.8       11       3      1       0      1
     26.9       11       5      1       2      1
     32.1       11       3      1       0      1
     37.1        5       3      1       0      1
     42.2        7       3      1       0      1
     47.2        5       3      1       0      1
     52.3        7       3      1       0      1
     57.3        7       5      1       2      1
     62.4        7       5      1       2      1
     67.5        5       3      1       0      1
     72.5        5       3      1       0      1
     77.6        7       3      1       0      1
     82.7        5       3      1       0      1
     87.7       11       5      1       2      1
     92.8       11       3      1       0      1
     97.8       11       5      1       2      1
    102.9        5       3      1       0      1
    108.0        5       3      1       0      1
    113.1        5       3      1       0      1
```

Peak world-rendering cameras: **1** at t=1.3s.

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

Peak active portal capture rigs: **0** of 0 at t=1.3s.

```text
        t  rigs  active  budget
      1.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     10.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     19.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     29.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     38.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     47.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     56.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     65.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     74.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     83.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     92.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    101.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    111.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 576px) at t=18.7s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1643        0        0
       end      16384        2345       10        1
      peak      16384        2345      159        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **4720** (415 visible), text2d 35, per-view projections 12 at t=18.7s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3761** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.040          0.081      112  render/ui/elapsed_cpu
         0.039          0.078       96  render/main_opaque_pass_2d/elapsed_cpu
         0.025          0.067      112  render/ui/elapsed_gpu
         0.024          0.037      112  render/msaa_writeback/elapsed_gpu
         0.012          0.046      112  render/upscaling/elapsed_gpu
         0.007          0.045       96  render/main_opaque_pass_2d/elapsed_gpu
         0.005          0.034      112  render/msaa_writeback/elapsed_cpu
         0.005          0.037      112  render/main_transparent_pass_2d/elapsed_gpu
         0.004          0.016      112  render/upscaling/elapsed_cpu
         0.001          0.006      112  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    583903.911    4751010.000      112  render/ui/fragment_shader_invocations
    155520.000     331776.000       96  render/main_opaque_pass_2d/fragment_shader_invocations
       669.429        766.000      112  render/ui/vertex_shader_invocations
       333.714        382.000      112  render/ui/clipper_primitives_out
       333.714        382.000      112  render/ui/clipper_invocations
         4.000          4.000       96  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       96  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       96  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      112  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      112  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      112  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      112  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (5 spans).
- GPU pass timings: **measured** (5 spans).
- Pipeline statistics: **measured** (12 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
tracy-capture produced no trace (no trace; tracy-capture said: Connecting to 127.0.0.1:8086...)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 4165 frames, summing to 27.58ms:

```text
   12.94 ms   46.9%  PreUpdate
    6.63 ms   24.0%  Update
    3.16 ms   11.4%  PostUpdate
    2.74 ms    9.9%  outside
    0.71 ms    2.6%  Last
    0.69 ms    2.5%  RunFixedMainLoop
    0.34 ms    1.2%  First
    0.20 ms    0.7%  StateTransition
    0.16 ms    0.6%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
   12.94    28.76     2.22   PreUpdate
    6.63    11.05     1.67   Update
    3.16     5.71     1.81   PostUpdate
    2.74     7.45     2.72   outside
    0.71     1.06     1.49   Last
    0.69     1.10     1.59   RunFixedMainLoop
    0.34     0.93     2.71   First
    0.20     0.34     1.67   StateTransition
    0.16     0.23     1.44   SpawnScene
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
  79.9%  the game itself
  19.2%  profiler (Tracy)
   0.6%  audio
   0.3%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead : 19.2%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.3%   (cargo and shell; NOT a compile)
the game itself           : 79.9%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  79.4%  game binary + its Rust/C deps
  17.9%  kernel
   2.6%  GPU driver / graphics stack
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
     3.97%  Tracy Profiler   ambition_game_bin                      [.] tracy::LZ4_compress_fast_continue(tracy::LZ4_stream_u*, char const*, char*, int, int, int)                                      
     2.50%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layere
     2.06%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     1.76%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCsejQFhAU0ddf_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     1.73%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     1.46%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20so
     1.26%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCsi7WNBsjk6LE_18tracing_subscriber8registry7sharded9DataInnerE3getBU_
     1.21%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_
     1.10%  ambition_game_b  ambition_game_bin                      [.] mi_theap_umalloc                                                                                                                
     0.80%  Tracy Profiler   libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.79%  Tracy Profiler   libc.so.6                              [.] __strlen_evex                                                                                                                   
     0.77%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::NormalizePath(char const*)                                                                                               
     0.71%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.69%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::dwarf_lookup_pc(tracy::backtrace_state*, tracy::dwarf_data*, unsigned long, int (*)(void*, unsigned long, unsigned long, 
     0.68%  ambition_game_b  ambition_game_bin                      [.] _RNvXs4_NtNtCsi7WNBsjk6LE_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5
     0.67%  ambition_game_b  ambition_game_bin                      [.] ___tracy_emit_zone_begin_alloc                                                                                                  
     0.66%  ambition_game_b  libc.so.6                              [.] __memcmp_evex_movbe                                                                                                             
     0.64%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::SendSourceLocationPayload(unsigned long)                                                                       
     0.58%  ambition_game_b  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.57%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.53%  Compute Task Po  ambition_game_bin                      [.] _RNvXs1_Csj0VNxpjt0sv_13tracing_tracyNtB5_10TracyLayerINtNtCsi7WNBsjk6LE_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layere
     0.51%  ambition_game_b  ambition_game_bin                      [.] _mi_theap_realloc_zero                                                                                                          
     0.50%  Tracy Profiler   ambition_game_bin                      [.] tracy::rpfree(void*)                                                                                                            
     0.48%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::backtrace_qsort(void*, unsigned long, unsigned long, int (*)(void const*, void const*)) [clone .localalias]              
     0.47%  ambition_game_b  [kernel.kallsyms]                      [k] perf_adjust_freq_unthr_context                                                                                                  
     0.47%  Compute Task Po  ambition_game_bin                      [.] tracy::rpmalloc(unsigned long)                                                                                                  
     0.46%  Tracy Symbol Wo  libc.so.6                              [.] _dl_addr                                                                                                                        
     0.45%  Compute Task Po  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.45%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::report_inlined_functions(unsigned long, tracy::function*, char const*, int (*)(void*, unsigned long, unsigned long, char 
     0.43%  Compute Task Po  [kernel.kallsyms]                      [k] perf_adjust_freq_unthr_context                                                                                                  
     0.41%  Tracy Sampling   ambition_game_bin                      [.] tracy::SysTraceWorker(void*)                                                                                                    
     0.41%  Tracy Profiler   ambition_game_bin                      [.] tracy::Profiler::Dequeue(tracy::moodycamel::ConsumerToken&)::{lambda(tracy::QueueItem*, unsigned long)#1}::operator()(tracy::Que
     0.39%  Compute Task Po  ambition_game_bin                      [.] _RNvMs1_NtNtNtCsejQFhAU0ddf_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.38%  Tracy Symbol Wo  ambition_game_bin                      [.] tracy::read_function_entry(tracy::backtrace_state*, tracy::dwarf_data*, tracy::unit*, unsigned long, tracy::dwarf_buf*, tracy::l
     0.33%  Tracy Symbol Wo  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
```

## Assets and render resources

- Decoded images: 41 → 1085 (444.5 MP, 1778.1 MB of decode work).
- Images resident at end: 231.
- **Resident image BYTES at end: 1778.1 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **349 images (182.4 MP)** at 20.1s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 6 GENERATED image(s) were allocated during gameplay (21.4 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

✔ FIRST ROOM: no notable decode landed in the second after the first `room-loaded` (frame 737) with a LATER frame stamp — the first room's art, the player's sheet included, was in before the route activated.

⚠ 94 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **4 of 111 notable decodes landed during SETTLED play** (56.7 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  53.1MP  at 18.974s  sprites/gnu_ton_boss/giant_gnu_spritesheet.png
   1.6MP  at 18.840s  backgrounds/parallax_layers/primeval_near_background.png
   1.0MP  at 18.800s  backgrounds/parallax_layers/primeval_sky.png
   1.0MP  at 18.800s  backgrounds/parallax_layers/primeval_far_backplate.png
```

Textures decoded more than once:

```text
   7x  <runtime-generated>
   4x  backgrounds/parallax_layers/cove_foreground.png
   4x  backgrounds/parallax_layers/cove_sky.png
   4x  backgrounds/parallax_layers/cove_far_backplate.png
   4x  backgrounds/parallax_layers/basement_far_backplate.png
   4x  backgrounds/parallax_layers/lab_far_backplate.png
   4x  backgrounds/parallax_layers/lab_sky.png
   4x  backgrounds/parallax_layers/lab_foreground.png
   4x  backgrounds/parallax_layers/basement_foreground.png
   4x  backgrounds/parallax_layers/cove_near_background.png
   4x  backgrounds/parallax_layers/lab_near_background.png
   4x  backgrounds/parallax_layers/basement_sky.png
   4x  backgrounds/parallax_layers/basement_near_background.png
   3x  backgrounds/parallax_layers/primeval_sky.png
   3x  backgrounds/parallax_layers/primeval_far_backplate.png
   3x  backgrounds/parallax_layers/primeval_near_background.png
   2x  backgrounds/parallax_layers/hub_foreground.png
   2x  backgrounds/parallax_layers/hub_far_backplate.png
   2x  backgrounds/parallax_layers/hub_sky.png
   2x  backgrounds/parallax_layers/hub_near_background.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 124
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1243876 bytes

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
| `alloc_by_span.csv` | perf_probe: allocations per Bevy system / tracing span | no |
| `churn_by_site.csv` | perf_probe: spawns, despawns, inserts, removes per source line | no |
| `changes_by_site.csv` | perf_probe: component and resource values marked changed, per source line | no |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

