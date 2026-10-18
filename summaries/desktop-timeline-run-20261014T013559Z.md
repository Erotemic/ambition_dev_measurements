# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `223a22170786` on `main` |
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

Observed span of the game's own log: **46.2s**.

## Frame time

43 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      4.0     203     5.4     3.4     5.7    37.7    188.9
      1.9      66     9.3     3.2     8.6   177.8    183.4
      5.0     128     7.7     5.6    13.5    63.8    123.1
     13.0     155     6.5     5.7     7.9    12.5     76.5
     40.1      72    14.0    10.7    30.7    43.0     61.3
     41.1     113     8.9     8.3    11.8    16.9     55.2
     16.0     137     7.4     6.2    11.3    37.4     48.4
      9.0     162     6.2     5.7     9.1    16.2     22.0
```

Full series: `frame_times.csv`.

15 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    5.674s     188.9 ms
    3.294s     183.4 ms
    3.472s     177.8 ms
    5.942s     123.0 ms
    5.784s     109.7 ms
   14.681s      76.5 ms
    6.562s      63.8 ms
   41.085s      61.3 ms
   42.203s      55.2 ms
   17.404s      48.4 ms
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
    1       32     True  central_hub_complex -> central_hub_complex
    2        6     True  central_hub_complex -> vertical_shaft
    3        5     True  vertical_shaft -> central_hub_complex
    4        6     True  central_hub_complex -> hall_of_characters
```

Frames over 33.4 ms AFTER the last transition was logged (t=17.396s): **4**, worst 61.3 ms.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 954 images / 267.7 MP in total; 38 were notable enough to print.

```text
ordered by: frame
before central_hub_complex: 6 image(s), 24.3 MP
  unknown                  2    16.5 MP
  vanity-card              1     3.0 MP
  boss-sheet               1     2.0 MP
  character-sheet          1     1.6 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.9        5       3      1       0      1
      2.9        5       3      1       0      1
      5.0        5       3      1       0      1
      7.0        5       3      1       0      1
      9.0       11       7      1       4      1
     11.0       11       7      1       4      1
     13.0        7       3      1       0      1
     15.0        5       3      1       0      1
     17.0       11       3      1       0      1
     19.0       11       3      1       0      1
     21.0       11       3      1       0      1
     23.1       11       3      1       0      1
     25.1       11       3      1       0      1
     27.1       11       3      1       0      1
     29.1       11       3      1       0      1
     31.1       11       3      1       0      1
     33.1       11       3      1       0      1
     35.1       11       4      1       0      1
     37.1       11       4      1       0      1
     39.1       11       4      1       0      1
     41.1       11       4      1       0      1
     43.1       11       4      1       0      1
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
      0.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      4.0     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
      7.0     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     10.0     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     13.0     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     16.0     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     19.0     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     22.1     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     25.1     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     28.1     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     31.1     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     34.1     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     37.1     0       0  res<=512 depth=0 captures<=1 updates/frame<=1
     40.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     43.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **6** (largest dimension 1152px) at t=9.0s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1604        0        0
       end      16384        2411      155        1
      peak      16384        2411      155        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **6056** (108 visible), text2d 405, per-view projections 84 at t=33.1s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3735** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.088          0.102       11  render/clustering/elapsed_gpu
         0.033          0.040       11  render/main_opaque_pass_3d/elapsed_gpu
         0.024          0.102       43  render/ui/elapsed_gpu
         0.022          0.023        5  render/msaa_writeback/elapsed_gpu
         0.021          0.034       11  render/clustering/elapsed_cpu
         0.020          0.039       11  render/main_opaque_pass_3d/elapsed_cpu
         0.017          0.019       11  render/early_mesh_preprocessing/elapsed_gpu
         0.017          0.033       43  render/ui/elapsed_cpu
         0.015          0.017       11  render/bin_unpacking/elapsed_gpu
         0.014          0.077       40  render/main_opaque_pass_2d/elapsed_cpu
         0.011          0.014       43  render/upscaling/elapsed_gpu
         0.009          0.017       11  render/main_transparent_pass_3d/elapsed_cpu
         0.007          0.008       11  render/main_transparent_pass_3d/elapsed_gpu
         0.006          0.011        5  render/msaa_writeback/elapsed_cpu
         0.006          0.010       40  render/main_opaque_pass_2d/elapsed_gpu
         0.006          0.018       11  render/bin_unpacking/elapsed_cpu
         0.003          0.004       43  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.005       11  render/early_mesh_preprocessing/elapsed_cpu
         0.002          0.011       43  render/upscaling/elapsed_cpu
         0.001          0.003       43  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1295348.182    1542889.000       11  render/main_opaque_pass_3d/fragment_shader_invocations
    528819.581    9618727.000       43  render/ui/fragment_shader_invocations
    325555.200    1327104.000       40  render/main_opaque_pass_2d/fragment_shader_invocations
     25988.636      33958.000       11  render/main_transparent_pass_3d/fragment_shader_invocations
      1030.727       1438.000       11  render/main_transparent_pass_3d/vertex_shader_invocations
       618.000       1174.000       43  render/ui/vertex_shader_invocations
       509.636        712.000       11  render/main_transparent_pass_3d/clipper_invocations
       493.455        712.000       11  render/main_transparent_pass_3d/clipper_primitives_out
       308.000        586.000       43  render/ui/clipper_primitives_out
       308.000        586.000       43  render/ui/clipper_invocations
       163.273        256.000       11  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000       11  render/early_mesh_preprocessing/compute_shader_invocations
        85.091        132.000       11  render/main_opaque_pass_3d/clipper_primitives_out
        81.636        128.000       11  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000       11  render/bin_unpacking/compute_shader_invocations
         4.000          4.000       40  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       40  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       40  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       43  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       43  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       43  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       43  render/main_transparent_pass_2d/clipper_primitives_out
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

Mean milliseconds per frame over 6029 frames, summing to 7.17ms:

```text
    2.47 ms   34.4%  Update
    1.71 ms   23.8%  PreUpdate
    1.32 ms   18.4%  PostUpdate
    0.77 ms   10.8%  outside
    0.44 ms    6.2%  RunFixedMainLoop
    0.18 ms    2.5%  Last
    0.11 ms    1.6%  StateTransition
    0.09 ms    1.2%  SpawnScene
    0.08 ms    1.2%  First
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    2.47     4.20     1.70   Update
    1.71     4.33     2.54   PreUpdate
    1.32     2.00     1.52   PostUpdate
    0.77     1.82     2.36   outside
    0.44     1.20     2.70   RunFixedMainLoop
    0.18     0.19     1.07   Last
    0.11     0.27     2.44   StateTransition
    0.09     0.10     1.10   SpawnScene
    0.08     0.17     2.00   First
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
   0.6%  build launcher (cargo, shell)
   0.3%  audio
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.6%   (cargo and shell; NOT a compile)
the game itself           : 99.1%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  76.5%  game binary + its Rust/C deps
  18.8%  kernel
   4.6%  GPU driver / graphics stack
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
     2.07%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     1.47%  ambition_game_b  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     1.25%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     1.10%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.88%  ambition_game_b  ambition_game_bin                 [.] _RNvMsi_NtNtNtCscHgRw1M2fX5_5alloc11collections5btree3mapINtB5_8BTreeMapNtCsl5aZAE4yZGy_12ambition_sfx5SfxIdNtNtB7_7set_val9SetValZST
     0.79%  Compute Task Po  ambition_game_bin                 [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCs97IECS7Fxk5_10bevy_tasks9task_poolNtB19_8TaskPool12new_
     0.79%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.76%  Compute Task Po  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.66%  ambition_game_b  ambition_game_bin                 [.] _RNvMs2_NtCsicKOxALeEQc_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCsdypwjDlmvri_14ambition_input7actions31Platformer2dInpu
     0.63%  ambition_game_b  ambition_game_bin                 [.] mi_free                                                                                                                              
     0.61%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.53%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCs8r7fi0y0JDi_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCslJuOcG9b2qi_11bevy_ca
     0.52%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCs8r7fi0y0JDi_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithINtNtCsjiDLM6nEe1A_16virtua
     0.51%  Compute Task Po  ambition_game_bin                 [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCs97IECS7Fxk5_10b
     0.47%  Compute Task Po  ambition_game_bin                 [.] _RNvMsd_CsaAXh38BfT2C_14async_executorNtB5_6Ticker5sleep                                                                             
     0.46%  Compute Task Po  ambition_game_bin                 [.] _RNvMs0_NtCsjIIPkFW7fiI_16concurrent_queue9unboundedINtB5_9UnboundedNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCs97IECS7Fxk
     0.44%  Compute Task Po  ambition_game_bin                 [.] _RNvNtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                       
     0.43%  Compute Task Po  ambition_game_bin                 [.] _RNvMs0_CsbiKPD912mSu_11fixedbitsetNtB5_11FixedBitSet11is_disjoint                                                                   
     0.41%  Compute Task Po  ambition_game_bin                 [.] _RNCINvMNtNtCs8r7fi0y0JDi_8bevy_ecs5query8par_iterINtB5_12QueryParIterTNtNtB9_6entity6EntityRNtNtCslJuOcG9b2qi_11bevy_camera10visibil
     0.41%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtCs4bBPlq7bjoc_21ambition_sprite_sheet6framesNtB7_11SheetRecord18flat_index_in_page                                         
     0.40%  ambition_game_b  ambition_game_bin                 [.] _RNvNtNtNtCs4rrmoRF14i9_15ambition_render9rendering6actors6rigged26drive_rigged_presentations                                        
     0.39%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCs8r7fi0y0JDi_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCsbOemfX7FPlS_16bevy_ya
     0.37%  ambition_game_b  ambition_game_bin                 [.] _RNCINvMNtNtCs8r7fi0y0JDi_8bevy_ecs5query8par_iterINtB5_12QueryParIterTNtNtB9_6entity6EntityRNtNtCslJuOcG9b2qi_11bevy_camera10visibil
     0.37%  Compute Task Po  ambition_game_bin                 [.] _RNvNtNtCsjuExa4KhTqm_16bevy_ecs_tilemap6render7extract7extract.llvm.17903479772183190355                                            
     0.37%  ambition_game_b  ambition_game_bin                 [.] _RNvMs3_NtNtCs4bBPlq7bjoc_21ambition_sprite_sheet9character6riggedNtB5_17RiggedSpriteAsset24row_draws_the_body_whole                 
     0.35%  Compute Task Po  [kernel.kallsyms]                 [k] psi_group_change                                                                                                                     
     0.34%  ambition_game_b  ambition_game_bin                 [.] mi_theap_malloc_aligned                                                                                                              
     0.33%  ambition_game_b  ambition_game_bin                 [.] _RNvMs1_NtNtNtCs8r7fi0y0JDi_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.32%  Compute Task Po  ambition_game_bin                 [.] _RNvMNtNtNtNtCs6ZjlLoI6YmX_3std3sys4sync5mutex5futexNtB2_5Mutex14lock_contended                                                      
     0.32%  Compute Task Po  ambition_game_bin                 [.] _mi_page_free_collect                                                                                                                
     0.31%  Compute Task Po  [kernel.kallsyms]                 [k] task_cputime                                                                                                                         
     0.30%  Compute Task Po  [kernel.kallsyms]                 [k] try_to_wake_up                                                                                                                       
     0.28%  ambition_game_b  [nvidia]                          [k] _nv046207rm                                                                                                                          
     0.27%  Compute Task Po  [kernel.kallsyms]                 [k] __update_blocked_fair                                                                                                                
     0.26%  Compute Task Po  ambition_game_bin                 [.] _RINvMs_NtNtCs8r7fi0y0JDi_8bevy_ecs5query4iterINtB5_9QueryIterTNtNtB9_6entity6EntityRNtNtCslJuOcG9b2qi_11bevy_camera10visibility19Inh
```

## Assets and render resources

- Decoded images: 109 → 954 (267.7 MP, 1070.6 MB of decode work).
- Images resident at end: 366.
- **Resident image BYTES at end: 1070.6 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **479 images (84.5 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 5 GENERATED image(s) were allocated during gameplay (13.2 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

✔ FIRST ROOM: no notable decode landed in the second after the first `room-loaded` (frame 513) with a LATER frame stamp — the first room's art, the player's sheet included, was in before the route activated.

⚠ 14 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   6x  <runtime-generated>
   2x  sprites_0_5x/imperfect_cellular_automaton_parts.png
   2x  sprites_0_5x/noether_parts.png
   2x  sprites_0_5x/flying_spaghetti_monster_boss_parts.png
   2x  sprites_0_5x/gnu_ton_scholar_fighter_spritesheet.png
   2x  sprites_0_5x/perfect_cellular_automaton_parts.png
   2x  sprites_0_5x/gnu_ton_boss/giant_gnu_spritesheet.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 540256 bytes

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

