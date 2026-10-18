# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `5b011190f765` on `main` |
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

Observed span of the game's own log: **120.1s**.

## Frame time

116 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     13.5     240     5.6     2.0     3.1    82.1    391.1
      1.5     163     4.0     2.0     3.1     9.5    241.7
     76.6     327     3.2     2.8     4.1     4.7     64.5
     15.5     219     4.6     3.4     8.2    19.7     60.2
     79.6     230     4.4     3.3     8.8    22.6     54.5
     16.5     262     3.8     3.0     5.7    14.8     45.0
     80.7     231     4.3     3.7     6.3    17.2     34.3
     94.7     231     4.3     3.8     6.0    11.6     28.1
```

Full series: `frame_times.csv`.

12 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   15.675s     391.1 ms
   16.140s     345.5 ms
    3.436s     240.2 ms
   15.795s      82.1 ms
   78.979s      64.7 ms
    3.196s      62.2 ms
   17.073s      60.2 ms
   17.820s      56.1 ms
   81.829s      54.5 ms
   81.257s      48.1 ms
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
    1        3     True  central_hub_complex -> hall_of_characters
    2        3     True  hall_of_characters -> central_hub_complex
    3        3     True  central_hub_complex -> primeval_trailhead
    4        3     True  primeval_trailhead -> primeval_run
    5        3     True  primeval_run -> primeval_rookery
    6        3     True  primeval_rookery -> primeval_ridge
    7        3     True  primeval_ridge -> primeval_canopy
    8        3     True  primeval_canopy -> primeval_tyrant_hall
    9        3     True  primeval_tyrant_hall -> primeval_canopy
   10        3     True  primeval_canopy -> primeval_ridge
   11        3     True  primeval_ridge -> primeval_rookery
   12        3     True  primeval_rookery -> primeval_run
   13        3     True  primeval_run -> primeval_trailhead
   14        3     True  primeval_trailhead -> central_hub_complex
   15        3     True  central_hub_complex -> hall_of_characters
   16        4     True  hall_of_characters -> central_hub_complex
   17        3     True  central_hub_complex -> primeval_trailhead
   18        3     True  primeval_trailhead -> primeval_run
   19        3     True  primeval_run -> primeval_rookery
   20        3     True  primeval_rookery -> primeval_ridge
   21        3     True  primeval_ridge -> primeval_canopy
```

Frames over 33.4 ms AFTER the last transition was logged (t=117.748s): **0**.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 1103 images / 447.9 MP in total; 111 were notable enough to print.

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
      0.5        5       3      1       0      1
      5.5        5       3      1       0      1
     10.5        5       3      1       0      1
     15.5        5       3      1       0      1
     20.5       11       3      1       0      1
     25.6       11       3      1       0      1
     30.6       11       3      1       0      1
     35.6        5       3      1       0      1
     40.6        5       3      1       0      1
     45.6        5       3      1       0      1
     50.6        5       3      1       0      1
     55.6        7       3      1       0      1
     60.6        7       3      1       0      1
     65.6        5       3      1       0      1
     70.6        5       3      1       0      1
     75.6        5       3      1       0      1
     80.7       11       3      1       0      1
     85.7       11       3      1       0      1
     90.7       11       3      1       0      1
     95.7        5       3      1       0      1
    100.7        5       3      1       0      1
    105.7        5       3      1       0      1
    110.7        5       3      1       0      1
    115.7        7       3      1       0      1
```

Peak world-rendering cameras: **1** at t=0.5s.

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

Peak active portal capture rigs: **0** of 0 at t=0.5s.

```text
        t  rigs  active  budget
      0.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      9.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     27.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     36.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     45.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     54.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     63.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     72.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     81.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     90.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     99.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    108.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 576px) at t=16.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1643        0        0
       end      16384        2349       11        1
      peak      16384        2349      159        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **4716** (336 visible), text2d 35, per-view projections 12 at t=16.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3761** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.024          0.026      116  render/msaa_writeback/elapsed_gpu
         0.023          0.067      116  render/ui/elapsed_gpu
         0.013          0.034      101  render/main_opaque_pass_2d/elapsed_cpu
         0.012          0.070      116  render/ui/elapsed_cpu
         0.011          0.012      116  render/upscaling/elapsed_gpu
         0.005          0.007      101  render/main_opaque_pass_2d/elapsed_gpu
         0.003          0.034      116  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.005      116  render/msaa_writeback/elapsed_cpu
         0.001          0.011      116  render/upscaling/elapsed_cpu
         0.001          0.001      116  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    571962.190    4751038.000      116  render/ui/fragment_shader_invocations
    154390.812     331776.000      101  render/main_opaque_pass_2d/fragment_shader_invocations
       680.172        770.000      116  render/ui/vertex_shader_invocations
       339.086        384.000      116  render/ui/clipper_primitives_out
       339.086        384.000      116  render/ui/clipper_invocations
         4.000          4.000      101  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000      101  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000      101  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      116  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      116  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      116  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      116  render/main_transparent_pass_2d/clipper_primitives_out
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

Mean milliseconds per frame over 36129 frames, summing to 3.22ms:

```text
    1.27 ms   39.5%  Update
    0.65 ms   20.3%  PostUpdate
    0.55 ms   17.1%  PreUpdate
    0.50 ms   15.6%  outside
    0.09 ms    2.7%  Last
    0.05 ms    1.5%  First
    0.04 ms    1.4%  RunFixedMainLoop
    0.04 ms    1.2%  SpawnScene
    0.02 ms    0.8%  StateTransition
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    1.27     3.32     2.62   Update
    0.65     1.25     1.91   PostUpdate
    0.55     1.63     2.97   PreUpdate
    0.50     1.39     2.77   outside
    0.09     0.11     1.25   Last
    0.05     0.12     2.45   First
    0.04     0.12     2.69   RunFixedMainLoop
    0.04     0.07     1.78   SpawnScene
    0.02     0.08     3.41   StateTransition
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
  99.6%  the game itself
   0.2%  audio
   0.1%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           : 99.6%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  76.5%  game binary + its Rust/C deps
  18.2%  kernel
   5.2%  GPU driver / graphics stack
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
     1.79%  ambition_game_b  ambition_game_bin                    [.] _RNvXNtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run       
     1.54%  Compute Task Po  ambition_game_bin                    [.] _RNvMs1_NtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                               
     1.13%  ambition_game_b  ambition_game_bin                    [.] _mi_page_malloc_zero                                                                                                              
     0.95%  Compute Task Po  ambition_game_bin                    [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs97IECS7Fxk5_10bevy_tasks9task_poolNtB19_8TaskPool12n
     0.94%  Compute Task Po  ambition_game_bin                    [.] _mi_page_malloc_zero                                                                                                              
     0.82%  Compute Task Po  ambition_game_bin                    [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCs97IECS7Fxk5_
     0.71%  ambition_game_b  libc.so.6                            [.] __memcmp_evex_movbe                                                                                                               
     0.63%  Compute Task Po  ambition_game_bin                    [.] _RNvNtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                    
     0.58%  Compute Task Po  ambition_game_bin                    [.] _RNvMs0_CsbiKPD912mSu_11fixedbitsetNtB5_11FixedBitSet11is_disjoint                                                                
     0.57%  Compute Task Po  ambition_game_bin                    [.] _RNvMNtNtNtNtCs6ZjlLoI6YmX_3std3sys4sync5mutex5futexNtB2_5Mutex14lock_contended                                                   
     0.56%  Compute Task Po  ambition_game_bin                    [.] _RNvMs0_NtCsjIIPkFW7fiI_16concurrent_queue9unboundedINtB5_9UnboundedNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCs97IECS7
     0.55%  ambition_game_b  ambition_game_bin                    [.] _RNvMs0_NtNtCs8r7fi0y0JDi_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCsbOemfX7FPlS_16bevy
     0.55%  ambition_game_b  libc.so.6                            [.] __memmove_evex_unaligned_erms                                                                                                     
     0.49%  Compute Task Po  [kernel.kallsyms]                    [k] futex_wake                                                                                                                        
     0.49%  Compute Task Po  ambition_game_bin                    [.] _RNvMsd_CsaAXh38BfT2C_14async_executorNtB5_6Ticker5sleep                                                                          
     0.49%  Compute Task Po  libc.so.6                            [.] __memmove_evex_unaligned_erms                                                                                                     
     0.48%  Compute Task Po  ambition_game_bin                    [.] _mi_page_free_collect                                                                                                             
     0.47%  Compute Task Po  [kernel.kallsyms]                    [k] psi_group_change                                                                                                                  
     0.40%  Compute Task Po  [kernel.kallsyms]                    [k] clear_bhb_loop                                                                                                                    
     0.39%  ambition_game_b  ambition_game_bin                    [.] _RNvMs1_NtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                               
     0.38%  ambition_game_b  ambition_game_bin                    [.] mi_free                                                                                                                           
     0.37%  Compute Task Po  ambition_game_bin                    [.] _RNvMsb_CsaAXh38BfT2C_14async_executorNtB5_5State6active                                                                          
     0.33%  ambition_game_b  ambition_game_bin                    [.] _RNvXs8_NtCs14YtULSvDw6_10async_task4taskINtB5_12FallibleTaskINtNtCsc36rpYXAlPq_4core6result6ResultuINtNtCscHgRw1M2fX5_5alloc5boxe
     0.32%  ambition_game_b  [kernel.kallsyms]                    [k] psi_group_change                                                                                                                  
     0.32%  Compute Task Po  ambition_game_bin                    [.] _RNvNtNtCsjuExa4KhTqm_16bevy_ecs_tilemap6render7extract7extract.llvm.17903479772183190355                                         
     0.31%  Compute Task Po  ambition_game_bin                    [.] _RNvMNtCsjIIPkFW7fiI_16concurrent_queue7boundedINtB2_7BoundedINtNtCs14YtULSvDw6_10async_task4task12FallibleTaskINtNtCsc36rpYXAlPq_
     0.31%  Compute Task Po  [kernel.kallsyms]                    [k] try_to_wake_up                                                                                                                    
     0.31%  Compute Task Po  ambition_game_bin                    [.] _RINvMNtCsjIIPkFW7fiI_16concurrent_queue7boundedINtB3_7BoundedNtNtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor14multi_threaded12S
     0.30%  Compute Task Po  [kernel.kallsyms]                    [k] _raw_spin_lock                                                                                                                    
     0.29%  Compute Task Po  ambition_game_bin                    [.] _RNvMNtNtNtNtCs6ZjlLoI6YmX_3std3sys4sync7condvar5futexNtB2_7Condvar4wait                                                          
     0.29%  Compute Task Po  ambition_game_bin                    [.] _RNvMs1_NtCs14YtULSvDw6_10async_task3rawINtB5_7RawTaskINtCsaAXh38BfT2C_14async_executor15AsyncCallOnDropINtNtCsl3tN2pGYYNh_12futur
     0.28%  ambition_game_b  ambition_game_bin                    [.] _RNvMs0_NtCscDEOlKfKfF3_21ambition_sprite_sheet6framesNtB7_11SheetRecord18flat_index_in_page                                      
     0.27%  Compute Task Po  [kernel.kallsyms]                    [k] task_cputime                                                                                                                      
     0.27%  Compute Task Po  ambition_game_bin                    [.] _RINvCsaAXh38BfT2C_14async_executor5stealNtNtCs14YtULSvDw6_10async_task8runnable8RunnableECs97IECS7Fxk5_10bevy_tasks              
     0.27%  Compute Task Po  libc.so.6                            [.] syscall                                                                                                                           
```

## Assets and render resources

- Decoded images: 70 → 1100 (447.5 MP, 1790.1 MB of decode work).
- Images resident at end: 233.
- **Resident image BYTES at end: 1791.4 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **227 images (145.3 MP)** at 20.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 6 GENERATED image(s) were allocated during gameplay (21.4 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 16 notable decode(s) (18.4 MP) landed AFTER the first `room-loaded` (frame 5884) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.0MP  f   5886  backgrounds/parallax_layers/cove_foreground.png
   1.0MP  f   5886  backgrounds/parallax_layers/cove_far_backplate.png
   1.0MP  f   5886  backgrounds/parallax_layers/boss_far_backplate.png
   1.6MP  f   5886  backgrounds/parallax_layers/boss_near_background.png
   1.0MP  f   5886  backgrounds/parallax_layers/cove_sky.png
   1.6MP  f   5886  backgrounds/parallax_layers/cove_near_background.png
   1.0MP  f   5886  backgrounds/parallax_layers/boss_sky.png
   1.0MP  f   5886  backgrounds/parallax_layers/lab_foreground.png
```

⚠ 98 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   7x  <runtime-generated>
   4x  backgrounds/parallax_layers/cove_foreground.png
   4x  backgrounds/parallax_layers/cove_far_backplate.png
   4x  backgrounds/parallax_layers/cove_sky.png
   4x  backgrounds/parallax_layers/cove_near_background.png
   4x  backgrounds/parallax_layers/lab_foreground.png
   4x  backgrounds/parallax_layers/basement_sky.png
   4x  backgrounds/parallax_layers/lab_far_backplate.png
   4x  backgrounds/parallax_layers/lab_near_background.png
   4x  backgrounds/parallax_layers/lab_sky.png
   4x  backgrounds/parallax_layers/basement_foreground.png
   4x  backgrounds/parallax_layers/basement_far_backplate.png
   4x  backgrounds/parallax_layers/basement_near_background.png
   3x  backgrounds/parallax_layers/primeval_sky.png
   3x  backgrounds/parallax_layers/primeval_far_backplate.png
   3x  backgrounds/parallax_layers/primeval_near_background.png
   2x  backgrounds/parallax_layers/hub_far_backplate.png
   2x  backgrounds/parallax_layers/hub_foreground.png
   2x  backgrounds/parallax_layers/hub_near_background.png
   2x  sprites/goblin_parts.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 124
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1411288 bytes

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

