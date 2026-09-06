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

Observed span of the game's own log: **89.0s**.

## Frame time

51 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.6      14    51.8     7.3   146.4   391.4    391.4
     12.6     114     8.8     7.2    13.2    34.8     72.7
     28.7     111     9.0     9.3    11.4    19.2     25.7
      3.6     111     9.1     7.1    17.1    17.9     23.8
     19.7     109     9.2     9.4    12.3    20.6     22.4
     36.7      94    10.7    10.6    14.1    17.4     20.7
      4.6      60    16.7    16.7    17.8    18.9     19.3
      6.6      61    16.6    16.8    17.6    18.4     18.7
```

Full series: `frame_times.csv`.

5 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   35.202s     391.4 ms
   34.811s     146.4 ms
   35.311s     109.3 ms
   46.081s      72.7 ms
   46.008s      34.8 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.5        4       3      1       0      1
      2.6        4       3      1       0      1
      4.6        4       3      1       0      1
      6.6        4       3      1       0      1
      8.6        4       3      1       0      1
     10.6        4       3      1       0      1
     12.6        4       3      1       0      1
     14.6        4       3      1       0      1
     16.7        4       3      1       0      1
     18.7        4       3      1       0      1
     20.7        4       3      1       0      1
     22.7        4       3      1       0      1
     24.7        4       3      1       0      1
     26.7        4       3      1       0      1
     28.7        4       3      1       0      1
     30.7        4       3      1       0      1
     32.7        4       3      1       0      1
     34.7        4       3      1       0      1
     36.7        4       3      1       0      1
     38.7        4       3      1       0      1
     40.8        4       3      1       0      1
     42.8        4       3      1       0      1
     44.8        4       3      1       0      1
     46.8        4       3      1       0      1
     48.8        4       3      1       0      1
     50.8        4       3      1       0      1
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
      4.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
      8.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     12.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     16.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     20.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     24.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     28.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     32.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     36.7     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     40.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     44.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     48.8     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       4096        1468        0        0
       end       8192        1971        3        1
      peak       8192        1971        3        1
```

Entity count rose by 4096 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **176** (129 visible), text2d 12, per-view projections 8 at t=33.7s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3470** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.048          0.119       51  render/ui/elapsed_gpu
         0.041          0.051       51  render/msaa_writeback/elapsed_gpu
         0.020          0.025       51  render/upscaling/elapsed_gpu
         0.013          0.022       51  render/ui/elapsed_cpu
         0.004          0.004       51  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.013       51  render/msaa_writeback/elapsed_cpu
         0.002          0.007       51  render/upscaling/elapsed_cpu
         0.001          0.003       51  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    908694.784    4007519.000       51  render/ui/fragment_shader_invocations
       684.196       1114.000       51  render/ui/vertex_shader_invocations
       341.098        556.000       51  render/ui/clipper_primitives_out
       341.098        556.000       51  render/ui/clipper_invocations
         0.000          0.000       51  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       51  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       51  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       51  render/main_transparent_pass_2d/clipper_primitives_out
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

Mean milliseconds per frame over 5441 frames, summing to 9.42ms:

```text
    2.59 ms   27.5%  Update
    2.36 ms   25.0%  PreUpdate
    1.71 ms   18.2%  PostUpdate
    1.53 ms   16.3%  outside
    0.60 ms    6.4%  RunFixedMainLoop
    0.21 ms    2.3%  StateTransition
    0.20 ms    2.1%  Last
    0.13 ms    1.4%  First
    0.09 ms    0.9%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  69.5%  compiler / codegen / linker
  29.8%  the game itself
   0.3%  profiler (Tracy)
   0.2%  build launcher (cargo, shell)
   0.1%  audio
```

```text
profiler (Tracy) overhead :  0.3%
codegen inside the capture: 69.5%   (rustc / LLVM / linker threads)
build launcher            :  0.2%   (cargo and shell; NOT a compile)
the game itself           : 29.8%
native attribution        : COMPILE-CONTAMINATED
```

⚠⚠ **The native profile below is COMPILE-CONTAMINATED and must not be quoted.**

⭐ Everything keyed to GAME TIME is unaffected — `frame_times.csv`,
`frame_spikes.csv`, `runtime_census.csv` and the image censuses come from the
game's own stderr census, not from `perf` samples, and a compile competes for
cores mostly before the game starts.

**A compile ran inside this capture** — 70% of sampled cycles in
rustc, LLVM codegen and linker threads, against the game's own 30%.
Check `warm-build.status` and the gap between `wall_s` and `game_s` in
`frame_spikes.csv`: a first frame tens of seconds into the capture is the
build. If the warm build ran and the launch rebuilt anyway, the two are
asking cargo for different fingerprints — see the `build_env` rows in
`run_game.sh --print-plan`.

## Where the native time went

```text
  88.5%  game binary + its Rust/C deps
  10.2%  kernel
   1.2%  GPU driver / graphics stack
   0.0%  audio
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
     0.83%  ld.mold          libc.so.6                               [.] __strlen_evex                                                                                                                  
     0.81%  ambition_game_b  ambition_game_bin                       [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layer
     0.79%  ambition_game_b  ambition_game_bin                       [.] _RINvNtNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable9quicksort9quicksortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB8_SB17_20s
     0.78%  ld.mold          mold                                    [.] 0x0000000000da1341                                                                                                             
     0.75%  ld.mold          libc.so.6                               [.] __memmove_evex_unaligned_erms                                                                                                  
     0.68%  ambition_game_b  libc.so.6                               [.] __memmove_evex_unaligned_erms                                                                                                  
     0.63%  ambition_game_b  ambition_game_bin                       [.] _RNvMs_NtCshmDmZzHTD7x_12sharded_slab4poolINtB4_4PoolNtNtNtCshkcqoohQOEC_18tracing_subscriber8registry7sharded9DataInnerE3getBU
     0.56%  ld.mold          mold                                    [.] 0x00000000006d99fe                                                                                                             
     0.50%  ambition_game_b  ambition_game_bin                       [.] _RINvNtNtNtCsc36rpYXAlPq_4core5slice4sort8unstable7ipnsortNtNtCscHgRw1M2fX5_5alloc6string6StringNCINvMB6_SBT_20sort_unstable_by
     0.43%  ld.mold          [kernel.kallsyms]                       [k] native_queued_spin_lock_slowpath                                                                                               
     0.43%  ambition_game_b  ambition_game_bin                       [.] _RNvXNtNtNtCshA6g2k3PUQh_8bevy_ecs8schedule8executor15single_threadedNtB2_22SingleThreadedExecutorNtB4_14SystemExecutor3run    
     0.42%  ambition_game_b  ambition_game_bin                       [.] _mi_page_malloc_zero                                                                                                           
     0.41%  ld.mold          libc.so.6                               [.] __memcmp_evex_movbe                                                                                                            
     0.30%  Compute Task Po  libc.so.6                               [.] __memmove_evex_unaligned_erms                                                                                                  
     0.29%  Compute Task Po  ambition_game_bin                       [.] _RNvXs1_Cs22H1xAPeSjx_13tracing_tracyNtB5_10TracyLayerINtNtCshkcqoohQOEC_18tracing_subscriber5layer5LayerINtNtBS_7layered7Layer
     0.29%  ld.mold          [kernel.kallsyms]                       [k] memset_orig                                                                                                                    
     0.29%  rustc            librustc_driver-28a98848f7a7c026.so     [.] _RINvNtCslKopGNxs1uA_17rustc_codegen_ssa3mir11codegen_mirINtNtCsfXHF3yfd57u_18rustc_codegen_llvm7builder14GenericBuilderNtNtBX_
     0.25%  rustc            librustc_driver-28a98848f7a7c026.so     [.] _RNvMs2_NtNtCsf3umj1THu2A_12rustc_middle2ty7contextNtB5_13CtxtInterners9intern_ty                                              
```

## Assets and render resources

- Decoded images: 89 → 167 (121.6 MP, 486.4 MB of decode work).
- Images resident at end: 166.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **129 images (97.2 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 23 decode(s) landed before the first `room-loaded` (82.4 MP) — boot. Not a gameplay hitch.

⚠ 2 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   3x  <runtime-generated> — allocated during gameplay. No asset path, so this is generated (an atlas or a render target), not content that could have been demanded earlier.
```

## Collection status

- `warm-build`: 0
- `perf-record`: 101
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1408504 bytes

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

