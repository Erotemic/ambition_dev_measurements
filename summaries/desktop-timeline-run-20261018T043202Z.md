# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `135a5077cbab` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --autoplay ` |
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

Observed span of the game's own log: **120.0s**.

## Frame time

111 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     14.9      61    21.8    10.6    55.9   207.6    345.4
      2.7      12    53.6    22.5   114.5   321.4    321.4
     41.3      50    22.4    17.1    72.8   126.0    126.0
     63.5      16    62.5    54.2   120.9   125.9    125.9
    103.9      59    17.1    14.0    27.4    48.1    122.3
     62.5      59    18.7    13.5    45.1   102.0    120.6
     42.3      19    52.6    59.5    96.0   106.2    106.2
    107.0      40    27.0    22.1    54.9   100.6    100.6
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   19.936s     345.5 ms
    7.566s     318.7 ms
   20.146s     209.1 ms
    7.247s     115.6 ms
   22.858s      90.8 ms
   19.510s      86.9 ms
   21.484s      75.0 ms
   22.234s      72.0 ms
   22.991s      68.6 ms
   20.213s      68.5 ms
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
    1       12     True  central_hub_complex -> hall_of_characters
    2       18     True  central_hub_complex -> primeval_trailhead
    3       12     True  primeval_trailhead -> primeval_run
    4       40     True  primeval_run -> primeval_rookery
    5       20     True  primeval_rookery -> primeval_ridge
    6      247     True  central_hub_complex -> primeval_trailhead
    7       11     True  primeval_trailhead -> primeval_run
    8       12     True  primeval_run -> primeval_rookery
    9        8     True  primeval_rookery -> primeval_ridge
   10       13     True  primeval_ridge -> primeval_canopy
   11       12     True  primeval_canopy -> primeval_tyrant_hall
   12       87     True  central_hub_complex -> primeval_trailhead
   13        9     True  primeval_trailhead -> primeval_run
   14       20     True  primeval_run -> primeval_rookery
   15        8     True  primeval_rookery -> primeval_ridge
   16       42     True  primeval_ridge -> primeval_canopy
   17       54     True  primeval_canopy -> primeval_tyrant_hall
   18       30     True  primeval_tyrant_hall -> primeval_canopy
   19        8     True  primeval_canopy -> primeval_ridge
   20        6     True  primeval_ridge -> primeval_rookery
   21       12     True  primeval_rookery -> primeval_run
   22       20     True  primeval_run -> primeval_trailhead
   23       27     True  primeval_trailhead -> central_hub_complex
   24       22     True  central_hub_complex -> hall_of_characters
```

Frames over 33.4 ms AFTER the last transition was logged (t=112.690s): **0**.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

⛔ **THE SPIKE LOG HIT ITS 60-LINE CAP, so this count is a FLOOR, not a total.** Percentile summaries continued; the per-frame lines did not.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1541 images / 580.3 MP in total; 157 were notable enough to print.

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
      1.7        5       3      1       0      1
      6.7        5       3      1       0      1
     11.7        5       3      1       0      1
     16.9        5       3      1       0      1
     22.0       11       3      1       0      1
     27.0       11       7      1       4      1
     32.1        5       3      1       0      1
     37.2        7       3      1       0      1
     42.3        7       3      1       0      1
     47.3        5       3      1       0      1
     52.4        5       3      1       0      1
     57.4        5       3      1       0      1
     62.5        5       3      1       0      1
     67.6        5       3      1       0      1
     72.6        5       3      1       0      1
     77.6        5       3      1       0      1
     82.7        7       3      1       0      1
     87.8        7       3      1       0      1
     92.8        5       3      1       0      1
     97.8        5       3      1       0      1
    102.9        5       3      1       0      1
    108.0       11       5      1       2      1
    113.0       11       3      1       0      1
```

Peak world-rendering cameras: **1** at t=1.7s.

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

Peak active portal capture rigs: **0** of 0 at t=1.7s.

```text
        t  rigs  active  budget
      1.7     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     10.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     20.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     29.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     38.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     47.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     56.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     65.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     74.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     83.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     92.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    101.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    111.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 576px) at t=17.9s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1644        0        0
       end      16384        2355      159        1
      peak      16384        2355      159        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **4716** (388 visible), text2d 35, per-view projections 12 at t=17.9s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3761** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.043          0.954       95  render/main_opaque_pass_2d/elapsed_cpu
         0.036          0.397      111  render/ui/elapsed_cpu
         0.025          0.067      111  render/ui/elapsed_gpu
         0.025          0.080      110  render/msaa_writeback/elapsed_gpu
         0.011          0.020      111  render/upscaling/elapsed_gpu
         0.008          0.035       95  render/main_opaque_pass_2d/elapsed_gpu
         0.006          0.020      110  render/msaa_writeback/elapsed_cpu
         0.005          0.037      111  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.010      111  render/upscaling/elapsed_cpu
         0.002          0.024      111  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    642386.288    4750926.000      111  render/ui/fragment_shader_invocations
    124852.547     331776.000       95  render/main_opaque_pass_2d/fragment_shader_invocations
       672.360        762.000      111  render/ui/vertex_shader_invocations
       335.189        380.000      111  render/ui/clipper_primitives_out
       335.189        380.000      111  render/ui/clipper_invocations
         4.000          4.000       95  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       95  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       95  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      111  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      111  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      111  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      111  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (5 spans).
- GPU pass timings: **measured** (5 spans).
- Pipeline statistics: **measured** (12 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
--no-tracy was passed; no per-system or per-render-pass timings were collected
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 7368 frames, summing to 15.25ms:

```text
    4.47 ms   29.3%  PreUpdate
    3.88 ms   25.5%  Update
    2.86 ms   18.7%  outside
    2.36 ms   15.5%  PostUpdate
    0.44 ms    2.9%  Last
    0.37 ms    2.4%  First
    0.36 ms    2.3%  RunFixedMainLoop
    0.31 ms    2.1%  StateTransition
    0.20 ms    1.3%  SpawnScene
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    4.47     7.22     1.61   PreUpdate
    3.88     5.87     1.51   Update
    2.86     3.31     1.16   outside
    2.36     3.16     1.33   PostUpdate
    0.44     0.49     1.11   Last
    0.37     0.51     1.39   First
    0.36     0.47     1.32   RunFixedMainLoop
    0.31     0.35     1.12   StateTransition
    0.20     0.22     1.11   SpawnScene
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
   0.8%  audio
   0.7%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.7%   (cargo and shell; NOT a compile)
the game itself           : 98.5%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  76.2%  game binary + its Rust/C deps
  18.7%  kernel
   4.9%  GPU driver / graphics stack
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
     1.99%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     1.69%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.76%  Compute Task Po  ambition_game_bin                      [.] _RNvMs1_NtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.75%  ambition_game_b  libc.so.6                              [.] __memcmp_evex_movbe                                                                                                             
     0.71%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.69%  Compute Task Po  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.69%  Compute Task Po  ambition_game_bin                      [.] _RNvMNtNtNtNtCs6ZjlLoI6YmX_3std3sys4sync5mutex5futexNtB2_5Mutex14lock_contended                                                 
     0.53%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.48%  Compute Task Po  ambition_game_bin                      [.] _RNvNtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                  
     0.47%  ambition_game_b  ambition_game_bin                      [.] _RNvMs0_NtNtCs8r7fi0y0JDi_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCsbOemfX7FPlS_16be
     0.44%  ambition_game_b  ambition_game_bin                      [.] mi_free                                                                                                                         
     0.42%  ambition_game_b  ambition_game_bin                      [.] _RNvMs1_NtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.42%  Compute Task Po  ambition_game_bin                      [.] _RNvMs0_CsbiKPD912mSu_11fixedbitsetNtB5_11FixedBitSet11is_disjoint                                                              
     0.40%  Compute Task Po  [kernel.kallsyms]                      [k] task_cputime                                                                                                                    
     0.38%  Compute Task Po  [kernel.kallsyms]                      [k] psi_group_change                                                                                                                
     0.38%  Compute Task Po  ambition_game_bin                      [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs97IECS7Fxk5_10bevy_tasks9task_poolNtB19_8TaskPool1
     0.32%  Compute Task Po  ambition_game_bin                      [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCs97IECS7Fxk
     0.31%  Compute Task Po  ambition_game_bin                      [.] _mi_page_free_collect                                                                                                           
     0.31%  IO Task Pool (1  ambition_game_bin                      [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                            
     0.28%  ambition_game_b  [kernel.kallsyms]                      [k] __update_blocked_fair                                                                                                           
     0.28%  IO Task Pool (3  ambition_game_bin                      [.] _RNvMs_NtCskjVoq6ovQAS_8fdeflate10decompressNtB4_12Decompressor4read                                                            
     0.26%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtNtCs8r7fi0y0JDi_8bevy_ecs6bundle6insertNtNtB6_4info10BundleInfo28insert_bundle_into_archetype                          
     0.26%  Compute Task Po  ambition_game_bin                      [.] _RNvNtNtNtCsOJRvmF496Z_16ambition_content12presentation9room_look12architecture9colour_at                                       
```

## Assets and render resources

- Decoded images: 91 → 1541 (580.3 MP, 2321.4 MB of decode work).
- Images resident at end: 409.
- **Resident image BYTES at end: 2321.4 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **349 images (182.4 MP)** at 20.1s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 12 GENERATED image(s) were allocated during gameplay (42.8 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 16 notable decode(s) (18.4 MP) landed AFTER the first `room-loaded` (frame 1064) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.0MP  f   1066  backgrounds/parallax_layers/cove_foreground.png
   1.0MP  f   1066  backgrounds/parallax_layers/cove_far_backplate.png
   1.0MP  f   1066  backgrounds/parallax_layers/boss_foreground.png
   1.6MP  f   1066  backgrounds/parallax_layers/cove_near_background.png
   1.0MP  f   1066  backgrounds/parallax_layers/cove_sky.png
   1.6MP  f   1066  backgrounds/parallax_layers/boss_near_background.png
   1.0MP  f   1066  backgrounds/parallax_layers/basement_sky.png
   1.0MP  f   1066  backgrounds/parallax_layers/lab_sky.png
```

⚠ 138 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  13x  <runtime-generated>
   5x  backgrounds/parallax_layers/cove_foreground.png
   5x  backgrounds/parallax_layers/cove_far_backplate.png
   5x  backgrounds/parallax_layers/cove_near_background.png
   5x  backgrounds/parallax_layers/cove_sky.png
   5x  backgrounds/parallax_layers/basement_sky.png
   5x  backgrounds/parallax_layers/lab_sky.png
   5x  backgrounds/parallax_layers/basement_far_backplate.png
   5x  backgrounds/parallax_layers/lab_far_backplate.png
   5x  backgrounds/parallax_layers/basement_near_background.png
   5x  backgrounds/parallax_layers/basement_foreground.png
   5x  backgrounds/parallax_layers/lab_foreground.png
   5x  backgrounds/parallax_layers/lab_near_background.png
   4x  backgrounds/parallax_layers/boss_foreground.png
   4x  backgrounds/parallax_layers/boss_near_background.png
   4x  backgrounds/parallax_layers/boss_sky.png
   4x  backgrounds/parallax_layers/boss_far_backplate.png
   4x  backgrounds/parallax_layers/hub_far_backplate.png
   4x  backgrounds/parallax_layers/hub_sky.png
   4x  backgrounds/parallax_layers/hub_near_background.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 124
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 979792 bytes

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

