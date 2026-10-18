# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `e1e5baf50ad4` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling ` |
| host | `toothbrush` |
| kernel | `Linux toothbrush 6.8.0-146-generic #146-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep  3 16:12:30 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
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
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.99.02", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **85.6s**.

## Frame time

79 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      7.9      67    16.0     6.8    16.4   140.4    459.1
      3.8      11    58.0     9.7   412.1   412.1    412.1
      8.9      26    38.7    11.4   179.6   399.3    399.3
     18.0      70    14.4    10.4    37.7    80.4    112.3
     11.0      83    12.2    10.6    20.2    33.3     68.7
     19.0      92    10.9    10.1    13.7    31.6     65.9
     22.0      66    15.2    12.8    38.3    55.8     65.7
     10.0      62    16.3    10.6    36.8    47.8     60.7
```

Full series: `frame_times.csv`.

41 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   10.284s     459.1 ms
    6.124s     412.1 ms
   10.752s     399.3 ms
   10.932s     179.6 ms
    5.712s     148.1 ms
    9.395s     140.4 ms
   20.050s     112.3 ms
   19.889s      80.4 ms
   12.867s      68.7 ms
   20.462s      65.9 ms
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
    1       44     True  central_hub_complex -> central_hub_complex
    2        7     True  central_hub_complex -> vertical_shaft
    3        9     True  vertical_shaft -> central_hub_complex
    4       10     True  central_hub_complex -> hall_of_characters
```

Frames over 33.4 ms AFTER the last transition was logged (t=23.750s): **18**, worst 65.7 ms.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 830 images / 381.6 MP in total; 88 were notable enough to print.

```text
ordered by: frame
before central_hub_complex: 9 image(s), 27.9 MP
  unknown                  5    20.1 MP
  vanity-card              1     3.0 MP
  boss-sheet               1     2.0 MP
  character-sheet          1     1.6 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      2.8        5       3      1       0      1
      6.9        5       3      1       0      1
     11.0        5       3      1       0      1
     15.0       11       9      1       6      1
     19.0        5       3      1       0      1
     23.0       11       3      1       0      1
     27.0       11       3      1       0      1
     31.1       11       3      1       0      1
     35.1       11       3      1       0      1
     39.1       11       3      1       0      1
     43.2       11       3      1       0      1
     47.2       11       3      1       0      1
     51.2       11       3      1       0      1
     55.3       11       3      1       0      1
     59.3       11       3      1       0      1
     63.3       11       7      1       4      1
     67.3       11       3      1       0      1
     71.3       11       3      1       0      1
     75.4       11       3      1       0      1
     79.4       11       3      1       0      1
```

Peak world-rendering cameras: **1** at t=2.8s.

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

Peak active portal capture rigs: **0** of 0 at t=2.8s.

```text
        t  rigs  active  budget
      2.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      8.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     15.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     21.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     27.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     33.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     39.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     45.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     51.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     57.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     63.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     69.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     75.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     81.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 1152px) at t=13.0s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1604        0        0
       end      16384        2466      155        1
      peak      16384        2466      155        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **6075** (429 visible), text2d 175, per-view projections 40 at t=28.1s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3735** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.101          0.101        1  render/clustering/elapsed_gpu
         0.046          0.046        1  render/main_opaque_pass_3d/elapsed_gpu
         0.032          0.042       78  render/msaa_writeback/elapsed_gpu
         0.030          0.104       79  render/ui/elapsed_gpu
         0.027          0.049       79  render/ui/elapsed_cpu
         0.024          0.053       74  render/main_opaque_pass_2d/elapsed_cpu
         0.022          0.022        1  render/main_opaque_pass_3d/elapsed_cpu
         0.022          0.022        1  render/clustering/elapsed_cpu
         0.020          0.020        1  render/early_mesh_preprocessing/elapsed_gpu
         0.016          0.016        1  render/bin_unpacking/elapsed_gpu
         0.014          0.019       78  render/upscaling/elapsed_gpu
         0.009          0.009        1  render/main_transparent_pass_3d/elapsed_cpu
         0.009          0.009        1  render/main_transparent_pass_3d/elapsed_gpu
         0.008          0.012       74  render/main_opaque_pass_2d/elapsed_gpu
         0.004          0.018       78  render/msaa_writeback/elapsed_cpu
         0.004          0.004        1  render/bin_unpacking/elapsed_cpu
         0.004          0.004        1  render/early_mesh_preprocessing/elapsed_cpu
         0.004          0.006       79  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.009       78  render/upscaling/elapsed_cpu
         0.001          0.005       79  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1316540.000    1316540.000        1  render/main_opaque_pass_3d/fragment_shader_invocations
    546136.190   10482709.000       79  render/ui/fragment_shader_invocations
    368764.541    1327104.000       74  render/main_opaque_pass_2d/fragment_shader_invocations
     27142.000      27142.000        1  render/main_transparent_pass_3d/fragment_shader_invocations
      1125.000       1125.000        1  render/main_transparent_pass_3d/vertex_shader_invocations
       697.367       1062.000       79  render/ui/vertex_shader_invocations
       555.000        555.000        1  render/main_transparent_pass_3d/clipper_invocations
       483.000        483.000        1  render/main_transparent_pass_3d/clipper_primitives_out
       347.696        530.000       79  render/ui/clipper_primitives_out
       347.696        530.000       79  render/ui/clipper_invocations
       144.000        144.000        1  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        1  render/early_mesh_preprocessing/compute_shader_invocations
        76.000         76.000        1  render/main_opaque_pass_3d/clipper_primitives_out
        72.000         72.000        1  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000        1  render/bin_unpacking/compute_shader_invocations
         4.000          4.000       74  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       74  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       74  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       79  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       79  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       79  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       79  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (10 spans).
- GPU pass timings: **measured** (10 spans).
- Pipeline statistics: **measured** (22 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
--no-tracy was passed; no per-system or per-render-pass timings were collected
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 5882 frames, summing to 13.53ms:

```text
    4.04 ms   29.8%  PreUpdate
    3.95 ms   29.2%  Update
    2.30 ms   17.0%  PostUpdate
    1.43 ms   10.6%  outside
    0.89 ms    6.6%  RunFixedMainLoop
    0.33 ms    2.4%  Last
    0.25 ms    1.9%  StateTransition
    0.18 ms    1.3%  SpawnScene
    0.17 ms    1.2%  First
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    4.04     8.73     2.16   PreUpdate
    3.95     5.36     1.36   Update
    2.30     3.16     1.37   PostUpdate
    1.43     2.62     1.83   outside
    0.89     1.72     1.93   RunFixedMainLoop
    0.33     0.33     1.02   Last
    0.25     0.34     1.34   StateTransition
    0.18     0.16     0.90   SpawnScene
    0.17     0.30     1.80   First
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
  99.1%  the game itself
   0.5%  audio
   0.4%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.4%   (cargo and shell; NOT a compile)
the game itself           : 99.1%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  78.2%  game binary + its Rust/C deps
  17.3%  kernel
   4.4%  GPU driver / graphics stack
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
     2.89%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     1.73%  ambition_game_b  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     1.41%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     0.94%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.82%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.75%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCs8r7fi0y0JDi_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithINtNtCsjiDLM6nEe1A_16virtua
     0.66%  ambition_game_b  ambition_game_bin                 [.] mi_free                                                                                                                              
     0.65%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCs8r7fi0y0JDi_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCslJuOcG9b2qi_11bevy_ca
     0.62%  ambition_game_b  ambition_game_bin                 [.] _RNvMsi_NtNtNtCscHgRw1M2fX5_5alloc11collections5btree3mapINtB5_8BTreeMapNtCsl5aZAE4yZGy_12ambition_sfx5SfxIdNtNtB7_7set_val9SetValZST
     0.58%  Compute Task Po  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.52%  ambition_game_b  ambition_game_bin                 [.] _RNvNtNtNtCs4rrmoRF14i9_15ambition_render9rendering6actors6rigged26drive_rigged_presentations                                        
     0.52%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.51%  ambition_game_b  ambition_game_bin                 [.] _RNCINvMNtNtCs8r7fi0y0JDi_8bevy_ecs5query8par_iterINtB5_12QueryParIterTNtNtB9_6entity6EntityRNtNtCslJuOcG9b2qi_11bevy_camera10visibil
     0.49%  Compute Task Po  ambition_game_bin                 [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs97IECS7Fxk5_10bevy_tasks9task_poolNtB19_8TaskPool12new_
     0.47%  ambition_game_b  ambition_game_bin                 [.] _RNvMs2_NtCsicKOxALeEQc_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCsdypwjDlmvri_14ambition_input7actions31Platformer2dInpu
     0.42%  ambition_game_b  ambition_game_bin                 [.] mi_theap_malloc_aligned                                                                                                              
     0.40%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtCskKQRbIZt79x_3ron5parseNtB5_6Parser25next_chars_while_from_len                                                            
     0.39%  Compute Task Po  ambition_game_bin                 [.] _RNvMs0_CsbiKPD912mSu_11fixedbitsetNtB5_11FixedBitSet11is_disjoint                                                                   
     0.39%  Compute Task Po  ambition_game_bin                 [.] _mi_page_free_collect                                                                                                                
     0.38%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtCs4bBPlq7bjoc_21ambition_sprite_sheet6framesNtB7_11SheetRecord18flat_index_in_page                                         
     0.38%  Compute Task Po  ambition_game_bin                 [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCs97IECS7Fxk5_10b
     0.37%  Compute Task Po  [kernel.kallsyms]                 [k] __update_blocked_fair                                                                                                                
     0.36%  Compute Task Po  ambition_game_bin                 [.] _RNCINvMNtNtCs8r7fi0y0JDi_8bevy_ecs5query8par_iterINtB5_12QueryParIterTNtNtB9_6entity6EntityRNtNtCslJuOcG9b2qi_11bevy_camera10visibil
     0.36%  Compute Task Po  ambition_game_bin                 [.] _RINvMs_NtNtCs8r7fi0y0JDi_8bevy_ecs5query4iterINtB5_9QueryIterNtNtB9_6entity6EntityINtNtB7_6filter2OrTINtB1l_7ChangedNtNtNtCs41nYrMTO
     0.35%  ambition_game_b  ambition_game_bin                 [.] _RNvMs3_NtNtCs4bBPlq7bjoc_21ambition_sprite_sheet9character6riggedNtB5_17RiggedSpriteAsset24row_draws_the_body_whole                 
     0.34%  Compute Task Po  ambition_game_bin                 [.] _RNvNtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                       
     0.33%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCs8r7fi0y0JDi_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCsbOemfX7FPlS_16bevy_ya
     0.31%  ambition_game_b  ambition_game_bin                 [.] _RNvMs1_NtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.30%  Compute Task Po  [kernel.kallsyms]                 [k] psi_group_change                                                                                                                     
     0.30%  Compute Task Po  [kernel.kallsyms]                 [k] futex_wake                                                                                                                           
     0.29%  Compute Task Po  ambition_game_bin                 [.] _RNvMs0_NtCsjIIPkFW7fiI_16concurrent_queue9unboundedINtB5_9UnboundedNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCs97IECS7Fxk
     0.28%  Compute Task Po  [kernel.kallsyms]                 [k] try_to_wake_up                                                                                                                       
     0.27%  Compute Task Po  ambition_game_bin                 [.] _RNvMsd_CsaAXh38BfT2C_14async_executorNtB5_6Ticker5sleep                                                                             
     0.26%  part table warm  ambition_game_bin                 [.] _RNvMs0_NtCskKQRbIZt79x_3ron5parseNtB5_6Parser25next_chars_while_from_len                                                            
     0.25%  ambition_game_b  [kernel.kallsyms]                 [k] __update_blocked_fair                                                                                                                
```

## Assets and render resources

- Decoded images: 84 → 830 (381.6 MP, 1526.5 MB of decode work).
- Images resident at end: 371.
- **Resident image BYTES at end: 1526.5 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **323 images (109.3 MP)** at 10.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 5 GENERATED image(s) were allocated during gameplay (13.2 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 16 notable decode(s) (18.4 MP) landed AFTER the first `room-loaded` (frame 323) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   1.0MP  f    325  backgrounds/parallax_layers/boss_foreground.png
   1.0MP  f    325  backgrounds/parallax_layers/cove_sky.png
   1.0MP  f    325  backgrounds/parallax_layers/cove_foreground.png
   1.6MP  f    325  backgrounds/parallax_layers/cove_near_background.png
   1.0MP  f    325  backgrounds/parallax_layers/boss_sky.png
   1.6MP  f    325  backgrounds/parallax_layers/boss_near_background.png
   1.0MP  f    325  backgrounds/parallax_layers/boss_far_backplate.png
   1.0MP  f    325  backgrounds/parallax_layers/lab_foreground.png
```

⚠ 70 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   6x  <runtime-generated>
   3x  backgrounds/parallax_layers/hub_sky.png
   3x  backgrounds/parallax_layers/hub_far_backplate.png
   3x  backgrounds/parallax_layers/hub_near_background.png
   2x  backgrounds/parallax_layers/boss_foreground.png
   2x  backgrounds/parallax_layers/cove_sky.png
   2x  backgrounds/parallax_layers/cove_foreground.png
   2x  backgrounds/parallax_layers/cove_near_background.png
   2x  backgrounds/parallax_layers/boss_sky.png
   2x  backgrounds/parallax_layers/boss_near_background.png
   2x  backgrounds/parallax_layers/boss_far_backplate.png
   2x  backgrounds/parallax_layers/cove_far_backplate.png
   2x  backgrounds/parallax_layers/basement_sky.png
   2x  backgrounds/parallax_layers/basement_foreground.png
   2x  backgrounds/parallax_layers/basement_far_backplate.png
   2x  backgrounds/parallax_layers/basement_near_background.png
   2x  backgrounds/parallax_layers/primeval_sky.png
   2x  backgrounds/parallax_layers/primeval_far_backplate.png
   2x  backgrounds/parallax_layers/primeval_near_background.png
   2x  backgrounds/parallax_layers/hub_foreground.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 835488 bytes

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

