# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `6bcb415bc148` on `main` |
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

Observed span of the game's own log: **41.0s**.

## Frame time

39 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.5      21    31.0     2.3   155.4   381.1    381.1
     35.5     316     3.2     2.8     3.8     6.0     69.1
      4.5     434     2.3     2.0     2.5     5.2     63.9
      7.5     386     2.6     2.3     3.0     7.9     56.2
     27.5     298     3.4     3.0     5.0     6.4     26.8
      2.5     424     2.4     2.1     3.6     6.3     13.4
     34.5     320     3.1     2.9     4.1     5.9     11.9
     31.5     308     3.2     3.0     4.5     6.8     10.6
```

Full series: `frame_times.csv`.

6 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    2.561s     381.1 ms
    2.180s     155.9 ms
   36.554s      69.1 ms
    5.496s      63.9 ms
    2.623s      62.2 ms
    8.645s      56.2 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.5        4       3      1       0      1
      2.5        4       3      1       0      1
      4.5        4       3      1       0      1
      6.5        4       3      1       0      1
      8.5        4       3      1       0      1
     10.5        4       3      1       0      1
     12.5        4       3      1       0      1
     14.5        4       3      1       0      1
     16.5        4       3      1       0      1
     18.5        4       3      1       0      1
     20.5        4       3      1       0      1
     22.5        4       3      1       0      1
     24.5        4       3      1       0      1
     26.5        4       3      1       0      1
     28.5        4       3      1       0      1
     30.5        4       3      1       0      1
     32.5        4       3      1       0      1
     34.5        4       3      1       0      1
     36.5        4       3      1       0      1
     38.5        4       3      1       0      1
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
      3.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      6.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      9.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     15.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     21.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     24.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     27.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     30.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     33.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     36.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     39.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       2048        1807        0        0
      peak       2048        1807        4        0
```

Peak sprites: **127** (121 visible), text2d 20, per-view projections 8 at t=22.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3241** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.028          0.080       39  render/ui/elapsed_gpu
         0.023          0.025       38  render/msaa_writeback/elapsed_gpu
         0.014          0.028       39  render/ui/elapsed_cpu
         0.011          0.012       39  render/upscaling/elapsed_gpu
         0.003          0.003       39  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.003       38  render/msaa_writeback/elapsed_cpu
         0.001          0.003       39  render/upscaling/elapsed_cpu
         0.000          0.001       39  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1090675.282    5542931.000       39  render/ui/fragment_shader_invocations
       636.051       2834.000       39  render/ui/vertex_shader_invocations
       317.026       1416.000       39  render/ui/clipper_primitives_out
       317.026       1416.000       39  render/ui/clipper_invocations
         0.000          0.000       39  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       39  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       39  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       39  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (4 spans).
- GPU pass timings: **measured** (4 spans).
- Pipeline statistics: **measured** (8 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
--no-tracy was passed; no per-system or per-render-pass timings were collected
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 13339 frames, summing to 2.93ms:

```text
    0.78 ms   26.6%  Update
    0.64 ms   22.0%  PostUpdate
    0.59 ms   20.0%  PreUpdate
    0.42 ms   14.3%  outside
    0.28 ms    9.6%  RunFixedMainLoop
    0.06 ms    2.2%  StateTransition
    0.06 ms    2.0%  Last
    0.05 ms    1.8%  SpawnScene
    0.04 ms    1.4%  First
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    0.78     1.55    -0.77   Update
    0.64     1.01    -0.37   PostUpdate
    0.59     1.81    -1.22   PreUpdate
    0.42     1.05    -0.63   outside
    0.28     0.91    -0.63   RunFixedMainLoop
    0.06     0.26    -0.20   StateTransition
    0.06     0.06    -0.01   Last
    0.05     0.09    -0.03   SpawnScene
    0.04     0.10    -0.06   First
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
  99.3%  the game itself
   0.4%  build launcher (cargo, shell)
   0.3%  audio
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.4%   (cargo and shell; NOT a compile)
the game itself           : 99.3%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  75.9%  game binary + its Rust/C deps
  17.7%  kernel
   6.3%  GPU driver / graphics stack
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
     1.79%  ambition_game_b  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     1.65%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run          
     1.43%  ambition_game_b  ambition_game_bin                 [.] _RNvMs2_NtCs2DYmA60ysKy_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCsfCZ9IGOsnvc_14ambition_input7actions31Platformer2dInpu
     1.20%  ambition_game_b  ambition_game_bin                 [.] mi_free                                                                                                                              
     1.06%  Compute Task Po  ambition_game_bin                 [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCshswzoL9RzC8_10bevy_tasks9task_poolNtB19_8TaskPool12new_
     1.05%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.98%  Compute Task Po  ambition_game_bin                 [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC8_10b
     0.69%  Compute Task Po  ambition_game_bin                 [.] _RNvMNtNtNtNtCs6ZjlLoI6YmX_3std3sys4sync5mutex5futexNtB2_5Mutex14lock_contended                                                      
     0.68%  Compute Task Po  ambition_game_bin                 [.] _mi_page_malloc_zero                                                                                                                 
     0.65%  ambition_game_b  ambition_game_bin                 [.] mi_theap_malloc_aligned                                                                                                              
     0.64%  Compute Task Po  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.57%  Compute Task Po  ambition_game_bin                 [.] _RNvMsb_CsaAXh38BfT2C_14async_executorNtB5_5State6active                                                                             
     0.56%  ambition_game_b  libc.so.6                         [.] __memmove_evex_unaligned_erms                                                                                                        
     0.55%  Compute Task Po  ambition_game_bin                 [.] _RNvMs0_NtCsjIIPkFW7fiI_16concurrent_queue9unboundedINtB5_9UnboundedNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC
     0.50%  Compute Task Po  ambition_game_bin                 [.] _RNvMsd_CsaAXh38BfT2C_14async_executorNtB5_6Ticker5sleep                                                                             
     0.49%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCsjRiFk8VshxD_11bevy_ca
     0.48%  Compute Task Po  [kernel.kallsyms]                 [k] futex_wake                                                                                                                           
     0.47%  Compute Task Po  ambition_game_bin                 [.] _mi_page_free_collect                                                                                                                
     0.40%  Compute Task Po  ambition_game_bin                 [.] _RNvNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                       
     0.40%  ambition_game_b  ambition_game_bin                 [.] _RNvMs_NtCs2DYmA60ysKy_22leafwing_input_manager15clashing_inputsNtB4_11BasicInputs12clashes_with                                     
     0.40%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithINtNtCs7H8wk14Yp9I_16virtua
     0.39%  Compute Task Po  libc.so.6                         [.] syscall                                                                                                                              
     0.38%  Compute Task Po  [kernel.kallsyms]                 [k] psi_group_change                                                                                                                     
     0.38%  ambition_game_b  ambition_game_bin                 [.] _RNvXNtNtCs2DYmA60ysKy_22leafwing_input_manager10user_input8keyboardNtNtCsfigEMvHI2pm_10bevy_input8keyboard7KeyCodeNtB4_9UserInput9de
     0.37%  ambition_game_b  ambition_game_bin                 [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCs4WaVACXR4pa_16bevy_ya
     0.36%  Compute Task Po  [kernel.kallsyms]                 [k] try_to_wake_up                                                                                                                       
     0.34%  ambition_game_b  ambition_game_bin                 [.] _RNvMs1_NtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                                  
     0.34%  ambition_game_b  ambition_game_bin                 [.] _RNvMsi_NtNtNtCscHgRw1M2fX5_5alloc11collections5btree3mapINtB5_8BTreeMapNtCsdhgKHBJqEfX_12ambition_sfx5SfxIdNtNtB7_7set_val9SetValZST
     0.33%  ambition_game_b  ambition_game_bin                 [.] _RNvXs8_NtCs14YtULSvDw6_10async_task4taskINtB5_12FallibleTaskINtNtCsc36rpYXAlPq_4core6result6ResultuINtNtCscHgRw1M2fX5_5alloc5boxed3B
     0.32%  Compute Task Po  ambition_game_bin                 [.] _RNvMNtCsjIIPkFW7fiI_16concurrent_queue7boundedINtB2_7BoundedINtNtCs14YtULSvDw6_10async_task4task12FallibleTaskINtNtCsc36rpYXAlPq_4co
     0.31%  Compute Task Po  ambition_game_bin                 [.] _RNvMs0_CsbiKPD912mSu_11fixedbitsetNtB5_11FixedBitSet11is_disjoint                                                                   
     0.31%  Compute Task Po  [kernel.kallsyms]                 [k] _raw_spin_lock                                                                                                                       
     0.29%  Compute Task Po  ambition_game_bin                 [.] _RNvMs1_NtCs14YtULSvDw6_10async_task3rawINtB5_7RawTaskINtCsaAXh38BfT2C_14async_executor15AsyncCallOnDropINtNtCsl3tN2pGYYNh_12futures_
     0.28%  ambition_game_b  ambition_game_bin                 [.] _RINvNtNtCs4m7RjTVW33o_5taffy7compute7flexbox19compute_preliminaryINtNtNtB6_4tree10taffy_tree9TaffyViewNtNtCs2cXdBFrtC1Z_7bevy_ui11me
     0.28%  Compute Task Po  ambition_game_bin                 [.] _RINvCsaAXh38BfT2C_14async_executor5stealNtNtCs14YtULSvDw6_10async_task8runnable8RunnableECshswzoL9RzC8_10bevy_tasks                 
```

## Assets and render resources

- Decoded images: 0 → 329 (170.3 MP, 681.1 MB of decode work).
- Images resident at end: 290.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **157 images (106.9 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 31 decode(s) landed before the first `room-loaded` (87.7 MP) — boot. Not a gameplay hitch.

⚠ 2 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **9 of 42 notable decodes landed during SETTLED play** (12.4 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
   2.0MP  at 35.345s  sprites/noether_portraits.png
   1.3MP  at 35.263s  sprites/officer_portraits.png
   1.3MP  at 35.263s  sprites/perfect_cellular_automaton_portraits.png
   1.3MP  at 35.263s  sprites/medic_portraits.png
   1.3MP  at 35.276s  sprites/carl_stargan_portraits.png
   1.3MP  at 35.276s  sprites/author_portraits.png
   1.3MP  at 35.276s  sprites/performer_portraits.png
   1.3MP  at 35.276s  sprites/patent_clerk_portraits.png
   1.3MP  at 35.276s  sprites/oiler_portraits.png
```

Textures decoded more than once:

```text
   2x  sprites/player_robot_v3_portraits.png
   2x  sprites/patent_clerk_portraits.png
   2x  sprites/oiler_portraits.png
   2x  sprites/officer_portraits.png
   2x  sprites/noether_portraits.png
   2x  sprites/author_portraits.png
   2x  sprites/performer_portraits.png
   2x  sprites/perfect_cellular_automaton_portraits.png
   2x  sprites/carl_stargan_portraits.png
   2x  sprites/medic_portraits.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 509128 bytes

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

