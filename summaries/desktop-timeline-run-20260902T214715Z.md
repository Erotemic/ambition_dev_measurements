# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `eafd454888ec` on `main` |
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

Observed span of the game's own log: **39.2s**.

## Frame time

35 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.1      57    10.9     2.6    14.1    77.6    296.6
      6.1     282     3.6     2.9     5.0    10.1     95.2
      8.1     213     4.7     3.7    10.1    13.5     21.6
     15.1     202     5.0     4.5     6.4    12.6     19.2
      3.1     296     3.4     2.8     6.8    16.4     17.0
     31.1     219     4.6     4.0     7.0    11.3     16.0
     22.1     228     4.4     4.2     5.7     6.5     13.0
     30.1     225     4.5     4.1     6.1     9.2     12.8
```

Full series: `frame_times.csv`.

4 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    3.962s     296.6 ms
    7.671s      95.2 ms
    4.170s      77.6 ms
    3.665s      72.4 ms
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
    1      383     True  central_hub_complex -> hall_of_characters
```

Frames over 33.4 ms AFTER the last transition was logged (t=10.210s): **0**.

The hall entry hitched for nine such frames on 2026-09-01, the worst well over a third of a second, all AFTER the cover lifted. Under the cover they are cover time, which is what a cover is for.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.1        4       3      1       0      1
      2.1        4       3      1       0      1
      3.1        4       3      1       0      1
      4.1        4       3      1       0      1
      5.1        4       3      1       0      1
      6.1        4       3      1       0      1
      7.1        4       3      1       0      1
      8.1        4       3      1       0      1
      9.1        4       3      1       0      1
     10.1        4       3      1       0      1
     11.1        4       3      1       0      1
     12.1        4       3      1       0      1
     13.1        4       3      1       0      1
     14.1        4       3      1       0      1
     15.1        4       4      1       0      1
     16.1        4       4      1       0      1
     17.1        4       4      1       0      1
     18.1        4       4      1       0      1
     19.1        4       4      1       0      1
     20.1        4       4      1       0      1
     21.1        4       4      1       0      1
     22.1        4       4      1       0      1
     23.1        4       4      1       0      1
     24.1        4       4      1       0      1
     25.1        4       4      1       0      1
     26.1        4       4      1       0      1
     27.1        4       4      1       0      1
     28.1        4       4      1       0      1
     29.1        4       4      1       0      1
     30.1        4       3      1       0      1
     31.1        4       3      1       0      1
     32.1        4       3      1       0      1
     33.1        4       3      1       0      1
     34.1        4       3      1       0      1
     35.1        4       3      1       0      1
     36.1        4       4      1       0      1
```

Peak world-rendering cameras: **1** at t=1.1s.

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

Peak active portal capture rigs: **0** of 0 at t=1.1s.

```text
        t  rigs  active  budget
      1.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      4.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      7.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     10.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     13.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     16.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     19.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     22.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     25.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     28.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     31.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     34.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=1.1s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1382        0        0
       end       8192        1932      130        1
      peak       8192        1932      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **280** (93 visible), text2d 35, per-view projections 11 at t=8.1s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3230** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.083          0.090       22  render/clustering/elapsed_gpu
         0.030          0.045       22  render/main_opaque_pass_3d/elapsed_gpu
         0.025          0.029       35  render/msaa_writeback/elapsed_gpu
         0.024          0.062       35  render/ui/elapsed_gpu
         0.017          0.018       22  render/early_mesh_preprocessing/elapsed_gpu
         0.014          0.016       22  render/bin_unpacking/elapsed_gpu
         0.013          0.025       22  render/clustering/elapsed_cpu
         0.012          0.014       35  render/upscaling/elapsed_gpu
         0.012          0.019       22  render/main_opaque_pass_3d/elapsed_cpu
         0.011          0.019       35  render/ui/elapsed_cpu
         0.008          0.015       22  render/main_transparent_pass_3d/elapsed_cpu
         0.007          0.009       22  render/main_transparent_pass_3d/elapsed_gpu
         0.003          0.004       35  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.006       22  render/early_mesh_preprocessing/elapsed_cpu
         0.003          0.005       22  render/bin_unpacking/elapsed_cpu
         0.002          0.006       35  render/msaa_writeback/elapsed_cpu
         0.002          0.004       35  render/upscaling/elapsed_cpu
         0.001          0.002       35  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1114214.909    1647801.000       22  render/main_opaque_pass_3d/fragment_shader_invocations
    520446.029    4653402.000       35  render/ui/fragment_shader_invocations
     24676.727      41437.000       22  render/main_transparent_pass_3d/fragment_shader_invocations
      1256.818       1846.000       22  render/main_transparent_pass_3d/vertex_shader_invocations
       622.727        916.000       22  render/main_transparent_pass_3d/clipper_invocations
       548.629       1438.000       35  render/ui/vertex_shader_invocations
       493.818        728.000       22  render/main_transparent_pass_3d/clipper_primitives_out
       273.314        718.000       35  render/ui/clipper_primitives_out
       273.314        718.000       35  render/ui/clipper_invocations
       181.455        276.000       22  render/main_opaque_pass_3d/vertex_shader_invocations
       145.455        192.000       22  render/early_mesh_preprocessing/compute_shader_invocations
        94.455        142.000       22  render/main_opaque_pass_3d/clipper_primitives_out
        90.727        138.000       22  render/main_opaque_pass_3d/clipper_invocations
        81.455        128.000       22  render/bin_unpacking/compute_shader_invocations
         0.000          0.000       35  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       35  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       35  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       35  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (9 spans).
- GPU pass timings: **measured** (9 spans).
- Pipeline statistics: **measured** (18 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
--no-tracy was passed; no per-system or per-render-pass timings were collected
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 8405 frames, summing to 4.17ms:

```text
    1.29 ms   31.0%  Update
    0.91 ms   21.9%  PostUpdate
    0.88 ms   21.2%  PreUpdate
    0.50 ms   11.9%  outside
    0.31 ms    7.3%  RunFixedMainLoop
    0.09 ms    2.0%  StateTransition
    0.08 ms    1.8%  Last
    0.06 ms    1.5%  SpawnScene
    0.05 ms    1.3%  First
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    1.29     2.83     2.19   Update
    0.91     1.62     1.78   PostUpdate
    0.88     2.57     2.91   PreUpdate
    0.50     1.25     2.52   outside
    0.31     1.01     3.31   RunFixedMainLoop
    0.09     0.32     3.69   StateTransition
    0.08     0.09     1.14   Last
    0.06     0.11     1.79   SpawnScene
    0.05     0.12     2.22   First
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
  99.2%  the game itself
   0.5%  build launcher (cargo, shell)
   0.3%  audio
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.5%   (cargo and shell; NOT a compile)
the game itself           : 99.2%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  75.5%  game binary + its Rust/C deps
  17.9%  kernel
   6.5%  GPU driver / graphics stack
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
     2.11%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     1.34%  Compute Task Po  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     1.26%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     1.13%  ambition_game_b  ambition_game_bin                 [.] _RNvMs2_NtCs2DYmA60ysKy_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCsfCZ9IGOsnvc_14ambition_input7actions31Platformer2dInpu
     0.94%  ambition_game_b  ambition_game_bin                 [.] mi_free                                                                                                                              
     0.83%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.76%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.75%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.74%  ambition_game_b  libc.so.6                         [.] __memcmp_evex_movbe                                                                                                                  
     0.73%  Compute Task Po  ambition_game_bin                 [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCshswzoL9RzC8_10bevy_tasks9task_poolNtB19_8TaskPool12new_
     0.56%  Compute Task Po  ambition_game_bin                 [.] _RNvMNtNtNtNtCs6ZjlLoI6YmX_3std3sys4sync5mutex5futexNtB2_5Mutex14lock_contended                                                      
     0.56%  Compute Task Po  ambition_game_bin                 [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC8_10b
     0.49%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithINtNtCs7H8wk14Yp9I_16virtua
     0.49%  Compute Task Po  ambition_game_bin                 [.] _mi_page_free_collect                                                                                                                
     0.47%  ambition_game_b  ambition_game_bin                 [.] mi_theap_malloc_aligned                                                                                                              
     0.46%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCs4WaVACXR4pa_16bevy_ya
     0.42%  Compute Task Po  ambition_game_bin                 [.] _RNvNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                       
     0.42%  Compute Task Po  ambition_game_bin                 [.] _RNvMs0_NtCsjIIPkFW7fiI_16concurrent_queue9unboundedINtB5_9UnboundedNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC
     0.40%  Compute Task Po  ambition_game_bin                 [.] _RNvMsd_CsaAXh38BfT2C_14async_executorNtB5_6Ticker5sleep                                                                             
     0.40%  Compute Task Po  libc.so.6                         [.] __memset_evex_unaligned_erms                                                                                                         
     0.36%  Compute Task Po  [kernel.kallsyms]                 [k] _raw_spin_lock                                                                                                                       
     0.35%  Compute Task Po  [kernel.kallsyms]                 [k] futex_wake                                                                                                                           
     0.32%  ambition_game_b  ambition_game_bin                 [.] _RNvMs_NtCs2DYmA60ysKy_22leafwing_input_manager15clashing_inputsNtB4_11BasicInputs12clashes_with                                     
     0.31%  Compute Task Po  ambition_game_bin                 [.] _RNvXs0_NtNtCseIVAWd0bsnF_13gpu_allocator9allocator19free_list_allocatorNtB5_17FreeListAllocatorNtB7_12SubAllocator8allocate         
     0.31%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtCs14YtULSvDw6_10async_task3rawINtB5_7RawTaskINtCsaAXh38BfT2C_14async_executor15AsyncCallOnDropINtNtCsl3tN2pGYYNh_12futures_
     0.30%  ambition_game_b  ambition_game_bin                 [.] _RNvMsi_NtNtNtCscHgRw1M2fX5_5alloc11collections5btree3mapINtB5_8BTreeMapNtCsdhgKHBJqEfX_12ambition_sfx5SfxIdNtNtB7_7set_val9SetValZST
     0.29%  ambition_game_b  [nvidia]                          [k] _nv046188rm                                                                                                                          
     0.28%  Compute Task Po  libc.so.6                         [.] syscall                                                                                                                              
     0.28%  Compute Task Po  ambition_game_bin                 [.] _RINvCsaAXh38BfT2C_14async_executor5stealNtNtCs14YtULSvDw6_10async_task8runnable8RunnableECshswzoL9RzC8_10bevy_tasks                 
     0.28%  ambition_game_b  ambition_game_bin                 [.] _RNvMs1_NtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.27%  Compute Task Po  [kernel.kallsyms]                 [k] __update_blocked_fair                                                                                                                
     0.27%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCsjRiFk8VshxD_11bevy_ca
     0.26%  Compute Task Po  [kernel.kallsyms]                 [k] psi_group_change                                                                                                                     
     0.26%  Compute Task Po  [kernel.kallsyms]                 [k] try_to_wake_up                                                                                                                       
     0.25%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCsebJz3vhySHQ_13bevy_ec
```

## Assets and render resources

- Decoded images: 0 → 252 (78.3 MP, 313.1 MB of decode work).
- Images resident at end: 251.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **150 images (53.8 MP)** at 10.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ FIRST ROOM: 1 notable decode(s) (2.2 MP) landed AFTER the first `room-loaded` (frame 1282) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   2.2MP  f   1284  sprites/ai_slop_spritesheet.png
```

⚠ 4 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

11 notable texture decodes, none repeated.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 498832 bytes

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

