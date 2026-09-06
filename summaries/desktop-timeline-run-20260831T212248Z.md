# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `b20324980c65` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
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
AdapterInfo { name: "NVIDIA GeForce RTX 3090", vendor: 4318, device: 8708, device_type: DiscreteGpu, device_pci_bus_id: "0000:01:00.0", driver: "NVIDIA", driver_info: "595.84", backend: Vulkan, subgroup_min_size: 32, subgroup_max_size: 32, transient_saves_memory: false }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **313.5s**.

## Frame time

272 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.1       4   173.9   153.2   414.6   414.6    414.6
     80.8      60    16.7    16.6    17.6    18.1     29.7
    121.2      60    16.7    16.6    17.5    18.6     29.2
     13.2      60    16.7    16.7    17.5    18.7     26.7
    254.4      61    16.7    16.7    18.2    18.5     25.7
    175.7      60    16.7    16.7    17.2    17.7     25.0
     42.5      61    16.6    16.6    19.6    21.0     24.2
    158.5      61    16.6    16.7    18.2    22.2     23.6
```

Full series: `frame_times.csv`.

4 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   38.142s     414.6 ms
   37.727s     153.2 ms
   38.262s     119.6 ms
  312.425s      52.3 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.1        4       3      1       0      1
     14.2        4       3      1       0      1
     27.3        4       3      1       0      1
     40.5        4       3      1       0      1
     53.6        4       3      1       0      1
     66.8        4       3      1       0      1
     79.8        4       3      1       0      1
     92.9        4       3      1       0      1
    106.1        4       3      1       0      1
    119.2        4       3      1       0      1
    132.3        4       3      1       0      1
    145.4        4       3      1       0      1
    158.5        4       3      1       0      1
    171.7        4       3      1       0      1
    184.8        4       3      1       0      1
    197.9        4       3      1       0      1
    211.1        4       3      1       0      1
    224.2        4       3      1       0      1
    237.3        4       3      1       0      1
    250.4        4       3      1       0      1
    263.6        4       3      1       0      1
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
      1.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     23.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     45.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     67.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     89.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    112.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    134.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    156.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    178.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    200.9     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    223.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    245.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    267.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=1.1s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       4096        1468        0        0
       end       4096        1562        0        0
      peak       4096        1562        0        0
```

Peak sprites: **0** (0 visible), text2d 0, per-view projections 0 at t=1.1s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3362** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.101          0.155      272  render/ui/elapsed_gpu
         0.052          0.100      271  render/msaa_writeback/elapsed_gpu
         0.024          0.045      271  render/upscaling/elapsed_gpu
         0.012          0.069      272  render/ui/elapsed_cpu
         0.004          0.005      272  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.051      271  render/msaa_writeback/elapsed_cpu
         0.002          0.005      271  render/upscaling/elapsed_cpu
         0.001          0.005      272  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   2424966.923    4007470.000      272  render/ui/fragment_shader_invocations
       577.316        594.000      272  render/ui/vertex_shader_invocations
       287.662        296.000      272  render/ui/clipper_primitives_out
       287.662        296.000      272  render/ui/clipper_invocations
         0.000          0.000      272  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      272  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      272  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      272  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (4 spans).
- GPU pass timings: **measured** (4 spans).
- Pipeline statistics: **measured** (8 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
tracy-capture produced no trace (the game never connected)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 16875 frames, summing to 16.27ms:

```text
   10.55 ms   64.8%  outside
    2.03 ms   12.5%  Update
    1.37 ms    8.4%  PostUpdate
    1.03 ms    6.3%  PreUpdate
    0.62 ms    3.8%  RunFixedMainLoop
    0.24 ms    1.5%  StateTransition
    0.20 ms    1.2%  Last
    0.15 ms    1.0%  First
    0.09 ms    0.5%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  50.5%  compiler / codegen / linker
  48.3%  the game itself
   0.7%  profiler (Tracy)
   0.4%  audio
   0.1%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  0.7%
codegen inside the capture: 50.5%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           : 48.3%
native attribution        : COMPILE-CONTAMINATED
```

⚠⚠ **The native profile below is COMPILE-CONTAMINATED and must not be quoted.**

⭐ Everything keyed to GAME TIME is unaffected — `frame_times.csv`,
`frame_spikes.csv`, `runtime_census.csv` and the image censuses come from the
game's own stderr census, not from `perf` samples, and a compile competes for
cores mostly before the game starts.

**A compile ran inside this capture** — 50% of sampled cycles in
rustc, LLVM codegen and linker threads, against the game's own 48%.
Check `warm-build.status` and the gap between `wall_s` and `game_s` in
`frame_spikes.csv`: a first frame tens of seconds into the capture is the
build. If the warm build ran and the launch rebuilt anyway, the two are
asking cargo for different fingerprints — see the `build_env` rows in
`run_game.sh --print-plan`.

## Where the native time went

```text
  85.3%  game binary + its Rust/C deps
  12.8%  kernel
   1.9%  GPU driver / graphics stack
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
     1.36%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20so
     1.31%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layere
     1.05%  ambition_game_b  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.86%  ambition_game_b  ambition_game_bin                      [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_
     0.82%  ambition_game_b  ambition_game_bin                      [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by_
     0.76%  ambition_game_b  ambition_game_bin                      [.] _RNvXNtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run     
     0.72%  ambition_game_b  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.59%  ld.mold          libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.57%  ld.mold          libc.so.6                              [.] __strlen_evex                                                                                                                   
     0.56%  Compute Task Po  ambition_game_bin                      [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layere
     0.46%  ambition_game_b  ambition_game_bin                      [.] mi_free                                                                                                                         
     0.46%  ld.mold          mold                                   [.] 0x0000000000da1341                                                                                                              
     0.45%  ambition_game_b  ambition_game_bin                      [.] _RNvMs2_NtCsbH7joPaZV8S_22leafwing_input_manager9input_mapINtB5_8InputMapNtNtCs43kaUkgDg0C_14ambition_input7actions31Platformer2
     0.43%  Compute Task Po  libc.so.6                              [.] __memmove_evex_unaligned_erms                                                                                                   
     0.43%  ld.mold          [kernel.kallsyms]                      [k] native_queued_spin_lock_slowpath                                                                                                
     0.39%  ld.mold          mold                                   [.] 0x00000000006d99fe                                                                                                              
     0.37%  Compute Task Po  ambition_game_bin                      [.] _RNvMs1_NtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor14multi_threadedNtB5_7Context13tick_executor                             
     0.35%  ambition_game_b  ambition_game_bin                      [.] _RNvXs4_NtNtCshkcqoohQOEC_18tracing_subscriber6filter13layer_filtersINtB5_8FilteredINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDINtNtB9_5
     0.35%  Compute Task Po  ambition_game_bin                      [.] _mi_page_malloc_zero                                                                                                            
     0.30%  Compute Task Po  ambition_game_bin                      [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU_
     0.30%  ambition_game_b  ambition_game_bin                      [.] _RNvXs1_NtNtCshkcqoohQOEC_18tracing_subscriber8registry7shardedNtB5_8RegistryNtB7_10LookupSpan9span_data                        
     0.29%  ld.mold          libc.so.6                              [.] __memcmp_evex_movbe                                                                                                             
     0.28%  Compute Task Po  ambition_game_bin                      [.] _RNvMs2_CsjIIPkFW7fiI_16concurrent_queueINtB5_15ConcurrentQueueNtNtCs14YtULSvDw6_10async_task8runnable8RunnableE3popCshswzoL9RzC
     0.27%  ambition_game_b  ambition_game_bin                      [.] mi_theap_umalloc                                                                                                                
     0.26%  Compute Task Po  ambition_game_bin                      [.] _RNvXs5_NtCsl3tN2pGYYNh_12futures_lite6futureINtB5_2OrIBH_NCNCNCNCNCNvMs0_NtCshswzoL9RzC8_10bevy_tasks9task_poolNtB19_8TaskPool1
     0.25%  ld.mold          [kernel.kallsyms]                      [k] memset_orig                                                                                                                     
```

## Assets and render resources

- Decoded images: 92 → 133 (98.3 MP, 393.0 MB of decode work).
- Images resident at end: 132.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **129 images (97.2 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 23 decode(s) landed before the first `room-loaded` (82.4 MP) — boot. Not a gameplay hitch.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   3x  <runtime-generated> — allocated during gameplay. No asset path, so this is generated (an atlas or a render target), not content that could have been demanded earlier.
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 2153332 bytes

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

