# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `1341e8cb7154` on `main` |
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

Observed span of the game's own log: **81.6s**.

## Frame time

78 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.6      18    40.1     3.2    85.1   503.9    503.9
      8.9      87    14.9     6.5    45.9   137.2    355.2
      7.6      25    39.7    11.3   199.2   207.3    207.3
     73.0     182     5.5     3.9     5.9    28.4    122.8
      9.9     251     3.9     3.2     4.3    15.1     89.1
      4.6     268     3.7     3.1     5.4    17.2     73.6
      6.6     225     4.5     3.3     7.4    25.8     73.1
     53.9     210     4.8     3.9    10.5    17.3     17.7
```

Full series: `frame_times.csv`.

22 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    3.450s     503.9 ms
   10.904s     355.1 ms
    9.024s     207.3 ms
    9.223s     199.2 ms
    9.378s     155.5 ms
   10.549s     137.2 ms
   74.353s     122.8 ms
    8.817s     107.5 ms
    9.534s      96.7 ms
   74.467s      94.4 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.6        4       3      1       0      1
      3.6        4       3      1       0      1
      6.6        4       3      1       0      1
      9.9        4       3      1       0      1
     12.9        4       3      1       0      1
     15.9        4       3      1       0      1
     18.9        4       3      1       0      1
     21.9        4       3      1       0      1
     24.9        4       3      1       0      1
     27.9        4       3      1       0      1
     30.9        4       3      1       0      1
     33.9        4       3      1       0      1
     36.9        4       3      1       0      1
     39.9        4       3      1       0      1
     42.9        4       3      1       0      1
     45.9        4       3      1       0      1
     48.9        4       3      1       0      1
     51.9        4       3      1       0      1
     54.9        4       3      1       0      1
     57.9        4       3      1       0      1
     61.0        4       3      1       0      1
     64.0        4       3      1       0      1
     67.0        4       3      1       0      1
     70.0        4       3      1       0      1
     73.0       18       5      3       2      1
     76.0       18       3      1       0      1
     79.0       18       3      1       0      1
```

Peak world-rendering cameras: **3** at t=73.0s.

⭐ **The world was drawn 3 times in one frame at peak.** Only
cameras that draw the SIMULATED WORLD are counted — main gameplay, a
split-screen local view, a portal capture rig — so the HUD is not one of
these. Each is a full pass over the visible population. Check
`camera_views.csv` for which roles were live together and at what
resolution each drew.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
           other  Cube pause camera, Cube scrim display camera
  portal_capture  Portal view capture (c128), Portal view capture (c129), Portal view capture (c130), Portal view capt
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **2** of 14 at t=73.0s.

```text
        t  rigs  active  budget
      0.6     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      6.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     18.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     24.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     30.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     36.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     42.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     48.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     54.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     61.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     67.0     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     73.0    14       2  res<=2048 depth=1 captures<=4 updates/frame<=4
     79.0    14       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **14** (largest dimension 2048px) at t=73.0s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1377        0        0
       end       8192        2076        1        1
      peak       8192        2076      130        1
```

Entity count rose by 6144 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **268** (56 visible), text2d 35, per-view projections 11 at t=6.6s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3241** in 33 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.024          0.031       77  render/msaa_writeback/elapsed_gpu
         0.022          0.065       78  render/ui/elapsed_gpu
         0.015          0.043       78  render/ui/elapsed_cpu
         0.012          0.015       78  render/upscaling/elapsed_gpu
         0.009          0.014        7  render/main_opaque_pass_2d/elapsed_cpu
         0.005          0.008        7  render/main_opaque_pass_2d/elapsed_gpu
         0.003          0.004       78  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.006       77  render/msaa_writeback/elapsed_cpu
         0.002          0.005       78  render/upscaling/elapsed_cpu
         0.001          0.002       78  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    271942.705    4653053.000       78  render/ui/fragment_shader_invocations
    184257.857     489955.000        7  render/main_opaque_pass_2d/fragment_shader_invocations
       687.590        698.000       78  render/ui/vertex_shader_invocations
       342.795        348.000       78  render/ui/clipper_primitives_out
       342.795        348.000       78  render/ui/clipper_invocations
         6.714         11.000        7  render/main_opaque_pass_2d/vertex_shader_invocations
         3.857          7.000        7  render/main_opaque_pass_2d/clipper_invocations
         3.857          7.000        7  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       78  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       78  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       78  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       78  render/main_transparent_pass_2d/clipper_primitives_out
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

Mean milliseconds per frame over 21056 frames, summing to 3.73ms:

```text
    1.06 ms   28.4%  Update
    0.85 ms   22.9%  PostUpdate
    0.74 ms   19.8%  PreUpdate
    0.52 ms   14.1%  outside
    0.30 ms    8.1%  RunFixedMainLoop
    0.07 ms    2.0%  StateTransition
    0.07 ms    1.8%  Last
    0.06 ms    1.7%  SpawnScene
    0.05 ms    1.4%  First
```

Wall against CPU, per phase. `stall` is wall minus CPU — time the frame spent in that phase with nothing running:

```text
    wall      cpu    stall   phase
    1.06     2.06    -1.01   Update
    0.85     1.25    -0.39   PostUpdate
    0.74     2.24    -1.50   PreUpdate
    0.52     1.35    -0.83   outside
    0.30     0.98    -0.68   RunFixedMainLoop
    0.07     0.28    -0.21   StateTransition
    0.07     0.08    -0.01   Last
    0.06     0.09    -0.03   SpawnScene
    0.05     0.11    -0.06   First
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
  99.5%  the game itself
   0.3%  audio
   0.2%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  0.2%   (cargo and shell; NOT a compile)
the game itself           : 99.5%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  75.4%  game binary + its Rust/C deps
  18.9%  kernel
   5.7%  GPU driver / graphics stack
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
     2.17%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     1.41%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     1.25%  ambition_game_b  ambition_game_bin                      [.] _RNvMs2_NtCs2DYmA60ysKy_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCsfCZ9IGOsnvc_14ambition_input7actions31Platformer2
     1.03%  Compute Task Po  ambition_game_bin                      [.] _RNvMs1_NtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     1.02%  Compute Task Po  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     1.00%  ambition_game_b  ambition_game_bin                      [.] mi_free                                                                                                                         
     0.82%  Compute Task Po  ambition_game_bin                      [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC
     0.77%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.77%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.74%  Compute Task Po  ambition_game_bin                      [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCshswzoL9RzC8_10bevy_tasks9task_poolNtB19_8TaskPool1
     0.57%  ambition_game_b  ambition_game_bin                      [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithINtNtCs7H8wk14Yp9I_16v
     0.55%  Compute Task Po  ambition_game_bin                      [.] _RNvMNtNtNtNtCs6ZjlLoI6YmX_3std3sys4sync5mutex5futexNtB2_5Mutex14lock_contended                                                 
     0.53%  Compute Task Po  ambition_game_bin                      [.] _RNvNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor28___rust_begin_short_backtrace10run_unsafe                                  
     0.52%  Compute Task Po  ambition_game_bin                      [.] _mi_page_free_collect                                                                                                           
     0.49%  ambition_game_b  ambition_game_bin                      [.] mi_theap_malloc_aligned                                                                                                         
     0.47%  ambition_game_b  ambition_game_bin                      [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCs4WaVACXR4pa_16be
     0.46%  Compute Task Po  ambition_game_bin                      [.] _RNvMs0_NtCsjIIPkFW7fiI_16concurrent_queue9unboundedINtB5_9UnboundedNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzo
     0.45%  Compute Task Po  ambition_game_bin                      [.] _RNvMsd_CsaAXh38BfT2C_14async_executorNtB5_6Ticker5sleep                                                                        
     0.43%  Compute Task Po  [kernel.kallsyms]                      [k] psi_group_change                                                                                                                
     0.40%  Compute Task Po  [kernel.kallsyms]                      [k] futex_wake                                                                                                                      
     0.38%  ambition_game_b  ambition_game_bin                      [.] _RNvMsi_NtNtNtCscHgRw1M2fX5_5alloc11collections5btree3mapINtB5_8BTreeMapNtCsdhgKHBJqEfX_12ambition_sfx5SfxIdNtNtB7_7set_val9SetV
     0.36%  ambition_game_b  ambition_game_bin                      [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCsjRiFk8VshxD_11be
     0.34%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtCs2DYmA60ysKy_22leafwing_input_manager10user_input8keyboardNtNtCsfigEMvHI2pm_10bevy_input8keyboard7KeyCodeNtB4_9UserInp
     0.34%  ambition_game_b  libc.so.6                              [.] __memcmp_evex_movbe                                                                                                             
     0.34%  Compute Task Po  [kernel.kallsyms]                      [k] try_to_wake_up                                                                                                                  
     0.32%  Compute Task Po  [kernel.kallsyms]                      [k] _raw_spin_lock                                                                                                                  
     0.30%  Compute Task Po  ambition_game_bin                      [.] _RINvMNtCsjIIPkFW7fiI_16concurrent_queue7boundedINtB3_7BoundedNtNtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor14multi_threaded1
     0.30%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtCs2DYmA60ysKy_22leafwing_input_manager15clashing_inputsNtB4_11BasicInputs12clashes_with                                
     0.30%  Compute Task Po  ambition_game_bin                      [.] _RNvMsb_CsaAXh38BfT2C_14async_executorNtB5_5State6active                                                                        
     0.30%  Compute Task Po  ambition_game_bin                      [.] _RNvMs0_CsbiKPD912mSu_11fixedbitsetNtB5_11FixedBitSet11is_disjoint                                                              
     0.29%  Compute Task Po  ambition_game_bin                      [.] _RNvMs1_NtCs14YtULSvDw6_10async_task3rawINtB5_7RawTaskINtCsaAXh38BfT2C_14async_executor15AsyncCallOnDropINtNtCsl3tN2pGYYNh_12fut
     0.29%  ambition_game_b  ambition_game_bin                      [.] _RNvMs1_NtNtNtCskYb4qUL7BCq_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.27%  ambition_game_b  ambition_game_bin                      [.] _RNvXsf_NtNtCs2DYmA60ysKy_22leafwing_input_manager10user_input7gamepadNtNtCsfigEMvHI2pm_10bevy_input7gamepad13GamepadButtonNtB7_
     0.26%  ambition_game_b  ambition_game_bin                      [.] _RNvMs0_NtNtCskYb4qUL7BCq_8bevy_ecs5query5stateINtB5_10QueryStateNtNtB9_6entity6EntityINtNtB7_6filter4WithNtNtCsebJz3vhySHQ_13be
```

## Assets and render resources

- Decoded images: 0 → 316 (573.9 MP, 2295.5 MB of decode work).
- Images resident at end: 301.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **149 images (104.4 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 21 decode(s) landed before the first `room-loaded` (74.0 MP) — boot. Not a gameplay hitch.

⚠ 100 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  29x  <runtime-generated> — allocated during gameplay. No asset path, so this is generated (an atlas or a render target), not content that could have been demanded earlier.
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 911852 bytes

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

