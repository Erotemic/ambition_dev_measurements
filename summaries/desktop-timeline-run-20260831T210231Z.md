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

Observed span of the game's own log: **401.0s**.

## Frame time

122 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.1       2   319.9   439.2   439.2   439.2    439.2
     69.4      18    55.6    29.9   153.3   199.0    199.0
     67.4      23    44.3    32.2   136.1   180.8    180.8
      3.1     126     7.9     6.9     8.8    17.1    118.2
     52.3     110     9.1     7.1    17.3    38.8    103.2
     68.4      64    15.7    14.2    25.1    32.8     60.8
    115.6      97    10.4     9.5    12.8    57.6     59.0
     66.4      75    13.8    10.8    30.4    42.1     48.2
```

Full series: `frame_times.csv`.

30 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
  276.922s     439.2 ms
  276.482s     200.7 ms
  344.068s     199.0 ms
  341.580s     180.9 ms
  343.869s     153.3 ms
  341.716s     136.0 ms
  343.715s     121.6 ms
  277.040s     118.2 ms
  343.594s     105.1 ms
  326.841s     103.2 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.9        4       3      1       0      1
      7.1        4       3      1       0      1
     13.1        4       3      1       0      1
     19.1        4       3      1       0      1
     25.2        4       3      1       0      1
     31.2        4       3      1       0      1
     37.2        4       3      1       0      1
     43.2        4       3      1       0      1
     49.3        4       3      1       0      1
     55.3        4       3      1       0      1
     61.3        4       3      1       0      1
     67.4        4       3      1       0      1
     73.4        4       3      1       0      1
     79.4        4       3      1       0      1
     85.5        4       3      1       0      1
     91.5        4       3      1       0      1
     97.5        4       3      1       0      1
    103.6        4       3      1       0      1
    109.6        4       3      1       0      1
    115.6       18       3      1       0      1
    121.6       18       6      3       2      1
```

Peak world-rendering cameras: **3** at t=116.6s.

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

Peak active portal capture rigs: **2** of 14 at t=116.6s.

```text
        t  rigs  active  budget
      0.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     11.1     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     21.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     31.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     41.2     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     51.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     61.3     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     71.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     81.4     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
     91.5     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    101.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    111.6     0       0  res<=2048 depth=1 captures<=4 updates/frame<=4
    121.6    14       2  res<=2048 depth=1 captures<=4 updates/frame<=4
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **14** (largest dimension 2048px) at t=115.6s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       4096        1468        0        0
       end       8192        2057        1        1
      peak       8192        2057      130        1
```

Entity count rose by 4096 and then held flat at 8192 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **257** (43 visible), text2d 180, per-view projections 40 at t=72.4s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3464** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.106          0.112        3  render/clustering/elapsed_gpu
         0.060          0.112      121  render/ui/elapsed_gpu
         0.045          0.056      121  render/msaa_writeback/elapsed_gpu
         0.040          0.046        3  render/main_opaque_pass_3d/elapsed_gpu
         0.021          0.039      121  render/upscaling/elapsed_gpu
         0.020          0.020        3  render/early_mesh_preprocessing/elapsed_gpu
         0.017          0.017        3  render/bin_unpacking/elapsed_gpu
         0.015          0.017        3  render/main_opaque_pass_3d/elapsed_cpu
         0.014          0.017        3  render/clustering/elapsed_cpu
         0.014          0.055      121  render/ui/elapsed_cpu
         0.011          0.016        8  render/main_opaque_pass_2d/elapsed_gpu
         0.010          0.014        8  render/main_opaque_pass_2d/elapsed_cpu
         0.009          0.009        3  render/main_transparent_pass_3d/elapsed_gpu
         0.007          0.008        3  render/main_transparent_pass_3d/elapsed_cpu
         0.004          0.034      121  render/main_transparent_pass_2d/elapsed_gpu
         0.003          0.011      121  render/msaa_writeback/elapsed_cpu
         0.003          0.003        3  render/bin_unpacking/elapsed_cpu
         0.002          0.003        3  render/early_mesh_preprocessing/elapsed_cpu
         0.002          0.008      121  render/upscaling/elapsed_cpu
         0.001          0.014      121  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   1212220.000    1309497.000        3  render/main_opaque_pass_3d/fragment_shader_invocations
   1149187.215    4007608.000      121  render/ui/fragment_shader_invocations
    471179.875     778356.000        8  render/main_opaque_pass_2d/fragment_shader_invocations
     26510.333      28246.000        3  render/main_transparent_pass_3d/fragment_shader_invocations
       882.333        937.000        3  render/main_transparent_pass_3d/vertex_shader_invocations
       631.653        702.000      121  render/ui/vertex_shader_invocations
       435.667        461.000        3  render/main_transparent_pass_3d/clipper_invocations
       430.333        461.000        3  render/main_transparent_pass_3d/clipper_primitives_out
       314.826        350.000      121  render/ui/clipper_primitives_out
       314.826        350.000      121  render/ui/clipper_invocations
       129.333        144.000        3  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        3  render/early_mesh_preprocessing/compute_shader_invocations
        67.333         76.000        3  render/main_opaque_pass_3d/clipper_primitives_out
        64.667         72.000        3  render/main_opaque_pass_3d/clipper_invocations
        64.000         64.000        3  render/bin_unpacking/compute_shader_invocations
        11.250         12.000        8  render/main_opaque_pass_2d/vertex_shader_invocations
         7.250          8.000        8  render/main_opaque_pass_2d/clipper_invocations
         7.250          8.000        8  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      121  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      121  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      121  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      121  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (10 spans).
- GPU pass timings: **measured** (10 spans).
- Pipeline statistics: **measured** (22 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
tracy-capture produced no trace (the game never connected)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 14031 frames, summing to 8.75ms:

```text
    2.53 ms   28.9%  Update
    2.35 ms   26.8%  PreUpdate
    1.60 ms   18.3%  PostUpdate
    1.14 ms   13.0%  outside
    0.50 ms    5.7%  RunFixedMainLoop
    0.22 ms    2.5%  StateTransition
    0.20 ms    2.3%  Last
    0.13 ms    1.5%  First
    0.09 ms    1.0%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  92.4%  compiler / codegen / linker
   7.5%  the game itself
   0.1%  profiler (Tracy)
   0.1%  build launcher (cargo, shell)
   0.0%  audio
```

```text
profiler (Tracy) overhead :  0.1%
codegen inside the capture: 92.4%   (rustc / LLVM / linker threads)
build launcher            :  0.1%   (cargo and shell; NOT a compile)
the game itself           :  7.5%
native attribution        : COMPILE-CONTAMINATED
```

⚠⚠ **The native profile below is COMPILE-CONTAMINATED and must not be quoted.**

⭐ Everything keyed to GAME TIME is unaffected — `frame_times.csv`,
`frame_spikes.csv`, `runtime_census.csv` and the image censuses come from the
game's own stderr census, not from `perf` samples, and a compile competes for
cores mostly before the game starts.

**A compile ran inside this capture** — 92% of sampled cycles in
rustc, LLVM codegen and linker threads, against the game's own 7%.
Check `warm-build.status` and the gap between `wall_s` and `game_s` in
`frame_spikes.csv`: a first frame tens of seconds into the capture is the
build. If the warm build ran and the launch rebuilt anyway, the two are
asking cargo for different fingerprints — see the `build_env` rows in
`run_game.sh --print-plan`.

## Where the native time went

```text
  94.5%  game binary + its Rust/C deps
   5.0%  kernel
   0.4%  GPU driver / graphics stack
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
     0.71%  rustc            librustc_driver-28a98848f7a7c026.so                   [.] _RNvMs2_NtNtCsf3umj1THu2A_12rustc_middle2ty7contextNtB5_13CtxtInterners9intern_ty                                
     0.59%  rustc            librustc_driver-28a98848f7a7c026.so                   [.] _RINvNtCslKopGNxs1uA_17rustc_codegen_ssa3mir11codegen_mirINtNtCsfXHF3yfd57u_18rustc_codegen_llvm7builder14Generic
     0.40%  rustc            librustc_driver-28a98848f7a7c026.so                   [.] _RNvMs2_NtNtCsf3umj1THu2A_12rustc_middle2ty7contextNtB5_13CtxtInterners16intern_predicate                        
     0.32%  rustc            libLLVM.so.22.1-rust-1.98.0-stable                    [.] LLVMDIBuilderCreateDebugLocation                                                                                 
     0.30%  rustc            librustc_driver-28a98848f7a7c026.so                   [.] _RNCNvMs_NtNtCshxfVZSAaYWo_21rustc_trait_selection6traits6selectNtB6_16SelectionContext11poly_select0Ba_         
     0.30%  rustc            librustc_driver-28a98848f7a7c026.so                   [.] _RNvXs0_NtCsfXHF3yfd57u_18rustc_codegen_llvm9debuginfoINtNtB7_7builder14GenericBuilderNtNtB7_7context6FullCxENtNt
```

## Assets and render resources

- Decoded images: 80 → 305 (574.7 MP, 2298.7 MB of decode work).
- Images resident at end: 290.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **128 images (94.2 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 23 decode(s) landed before the first `room-loaded` (82.4 MP) — boot. Not a gameplay hitch.

⚠ 89 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **11 of 123 notable decodes landed during SETTLED play** (106.9 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
  16.8MP  at 68.893s  sprites/perfect_cellular_automaton_spritesheet.1.png
  16.7MP  at 68.893s  sprites/perfect_cellular_automaton_spritesheet.2.png
  16.5MP  at 68.893s  sprites/perfect_cellular_automaton_spritesheet.3.png
  15.9MP  at 69.046s  sprites/perfect_cellular_automaton_spritesheet.4.png
  15.7MP  at 69.046s  sprites/perfect_cellular_automaton_spritesheet.png
   7.2MP  at 69.317s  sprites/patent_clerk_spritesheet.png
   6.5MP  at 69.046s  sprites/player_robot_v2_spritesheet.png
   5.7MP  at 68.893s  sprites/robot_spritesheet.png
   2.3MP  at 69.378s  sprites/willson_spritesheet.png
   1.8MP  at 69.245s  sprites/sanic_spritesheet.png
```

Textures decoded more than once:

```text
  31x  <runtime-generated> — allocated during gameplay. No asset path, so this is generated (an atlas or a render target), not content that could have been demanded earlier.
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 14167236 bytes

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

