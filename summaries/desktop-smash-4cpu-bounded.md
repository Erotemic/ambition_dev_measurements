# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `7d6d8e4017a2` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/smash_match_profile` |
| package / bin | `ambition_app_tools` / `smash_match_profile` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.99.1 (58cf8f9c3 2026-10-13)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling smash-match -- --fighters 4 --seconds 25 ` |
| host | `toothbrush` |
| kernel | `Linux toothbrush 6.8.0-147-generic #147-Ubuntu SMP PREEMPT_DYNAMIC Thu Sep 24 11:33:16 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `smash-match-4p` |

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

Observed span of the game's own log: **60.7s**.

## Frame time

26 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.0      52    14.9     7.0    44.4   109.8    156.8
      3.0     137     7.3     7.1    11.6    15.3     28.1
     20.1     144     7.0     6.9    10.3    11.9     13.8
      9.1     144     7.0     6.9    10.6    12.0     12.9
     10.1     144     7.0     7.0    10.0    11.8     12.8
     25.1     144     7.0     7.0     9.9    12.0     12.4
     17.1     144     7.0     6.9    10.2    12.2     12.4
      5.0     144     7.0     7.0    10.0    12.0     12.4
```

Full series: `frame_times.csv`.

4 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   35.026s     156.7 ms
   34.656s     110.1 ms
   35.265s     105.5 ms
   34.546s      45.3 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 948 images / 432.6 MP in total; 133 were notable enough to print.

```text
ordered by: frame
before smash_stage: 11 image(s), 29.6 MP
  unknown                  5    21.3 MP
  parallax                 4     4.6 MP
  boss-sheet               1     2.5 MP
  fx-sheet                 1     1.2 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      1.0        4       3      1       0      1
      2.0        5       3      1       0      1
      3.0        9       3      1       0      1
      4.0        9       3      1       0      1
      5.0        9       3      1       0      1
      6.1        7       3      1       0      1
      7.1        9       3      1       0      1
      8.1        7       5      1       2      1
      9.1        9       3      1       0      1
     10.1        9       3      1       0      1
     11.1        9       5      1       2      1
     12.1        7       3      1       0      1
     13.1        7       3      1       0      1
     14.1        9       3      1       0      1
     15.1        7       3      1       0      1
     16.1        9       3      1       0      1
     17.1        9       5      1       2      1
     18.1        9       3      1       0      1
     19.1        5       3      1       0      1
     20.1       11       3      1       0      1
     21.1       11       3      1       0      1
     22.1       13       3      1       0      1
     23.1       13       3      1       0      1
     24.1        9       3      1       0      1
     25.1        9       3      1       0      1
     26.1       11       3      1       0      1
     27.1       13       3      1       0      1
```

Peak world-rendering cameras: **1** at t=1.0s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
       offscreen  Cast bubble camera, rigged impostor camera, rigged impostor unpremultiply camera
           other  Cube pause camera, Cube scrim display camera, Developer Panel Camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=1.0s.

```text
        t  rigs  active  budget
      1.0     0       0  captures<=2
      3.0     0       0  captures<=2
      5.0     0       0  captures<=2
      7.1     0       0  captures<=2
      9.1     0       0  captures<=2
     11.1     0       0  captures<=2
     13.1     0       0  captures<=2
     15.1     0       0  captures<=2
     17.1     0       0  captures<=2
     19.1     0       0  captures<=2
     21.1     0       0  captures<=2
     23.1     0       0  captures<=2
     25.1     0       0  captures<=2
     27.1     0       0  captures<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **8** (largest dimension 1152px) at t=22.1s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1693        0        0
       end       4096        2050        4        0
      peak       4096        2050        4        0
```

Entity count rose by 2048 and then held flat at 4096 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **425** (374 visible), text2d 20, per-view projections 9 at t=17.1s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3870** in 36 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         0.040          0.053       26  render/msaa_writeback/elapsed_gpu
         0.038          0.127       26  render/ui/elapsed_gpu
         0.019          0.025       26  render/upscaling/elapsed_gpu
         0.013          0.023       25  render/main_opaque_pass_2d/elapsed_gpu
         0.011          0.018       26  render/ui/elapsed_cpu
         0.009          0.012       25  render/main_opaque_pass_2d/elapsed_cpu
         0.003          0.004       26  render/main_transparent_pass_2d/elapsed_gpu
         0.002          0.003       26  render/msaa_writeback/elapsed_cpu
         0.002          0.003       26  render/upscaling/elapsed_cpu
         0.000          0.001       26  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    729907.200    1327104.000       25  render/main_opaque_pass_2d/fragment_shader_invocations
    668473.462   13365064.000       26  render/ui/fragment_shader_invocations
       429.846        902.000       26  render/ui/vertex_shader_invocations
       213.923        450.000       26  render/ui/clipper_primitives_out
       213.923        450.000       26  render/ui/clipper_invocations
         4.000          4.000       25  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000       25  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000       25  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       26  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000       26  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000       26  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000       26  render/main_transparent_pass_2d/clipper_primitives_out
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

Mean milliseconds per frame over 3646 frames, summing to 7.18ms:

```text
    3.75 ms   52.3%  outside
    1.38 ms   19.2%  PreUpdate
    0.96 ms   13.4%  Update
    0.86 ms   12.0%  PostUpdate
    0.08 ms    1.2%  Last
    0.06 ms    0.9%  RunFixedMainLoop
    0.04 ms    0.6%  First
    0.02 ms    0.3%  SpawnScene
    0.01 ms    0.2%  StateTransition
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
    3.75     2.05     0.55   outside
    1.38     3.37     2.45   PreUpdate
    0.96     2.24     2.32   Update
    0.86     1.42     1.66   PostUpdate
    0.08     0.13     1.56   Last
    0.06     0.17     2.67   RunFixedMainLoop
    0.04     0.08     1.90   First
    0.02     0.05     1.94   SpawnScene
    0.01     0.03     1.94   StateTransition
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
  85.6%  compiler / codegen / linker
  14.0%  the game itself
   0.2%  build launcher (cargo, shell)
   0.1%  audio
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture: 85.6%   (rustc / LLVM / linker threads)
build launcher            :  0.2%   (cargo and shell; NOT a compile)
the game itself           : 14.0%
native attribution        : COMPILE-CONTAMINATED
```

⚠⚠ **The native profile below is COMPILE-CONTAMINATED and must not be quoted.**

⭐ Everything keyed to GAME TIME is unaffected — `frame_times.csv`,
`frame_spikes.csv`, `runtime_census.csv` and the image censuses come from the
game's own stderr census, not from `perf` samples, and a compile competes for
cores mostly before the game starts.

**A compile ran inside this capture** — 86% of sampled cycles in
rustc, LLVM codegen and linker threads, against the game's own 14%.
Check `warm-build.status` and the gap between `wall_s` and `game_s` in
`frame_spikes.csv`: a first frame tens of seconds into the capture is the
build. If the warm build ran and the launch rebuilt anyway, the two are
asking cargo for different fingerprints — see the `build_env` rows in
`run_game.sh --print-plan`.

## Where the native time went

```text
  92.1%  game binary + its Rust/C deps
   7.0%  kernel
   0.8%  GPU driver / graphics stack
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
     0.69%  ld.mold          mold                                    [.] 0x0000000000da1341                                                                                                             
     0.57%  ld.mold          libc.so.6                               [.] __memmove_evex_unaligned_erms                                                                                                  
     0.55%  rustc            librustc_driver-7ed0375ae352bd75.so     [.] _RNvMs2_NtNtCsdSHT1xazwJC_12rustc_middle2ty7contextNtB5_13CtxtInterners9intern_ty                                              
     0.49%  ld.mold          libc.so.6                               [.] __strlen_evex                                                                                                                  
     0.46%  rustc            librustc_driver-7ed0375ae352bd75.so     [.] _RINvNtCshycNWnLvI7D_17rustc_codegen_ssa3mir11codegen_mirINtNtCs9fqAePeZKqF_18rustc_codegen_llvm7builder14GenericBuilderNtNtBX_
     0.42%  ld.mold          [kernel.kallsyms]                       [k] native_queued_spin_lock_slowpath                                                                                               
     0.34%  ld.mold          mold                                    [.] 0x00000000006d99fe                                                                                                             
     0.34%  smash_match_pro  smash_match_profile                     [.] _RNvXsb_NtCsgFX3jaOk65H_5alloc3vecINtB5_3VecNtCs6y8RcMu9aQ3_21ambition_sprite_sheet8SheetRowENtNtCskADBYZwJmSU_4core5clone5Clon
     0.31%  ld.mold          libc.so.6                               [.] __memcmp_evex_movbe                                                                                                            
     0.31%  rustc            librustc_driver-7ed0375ae352bd75.so     [.] _RNvMs2_NtNtCsdSHT1xazwJC_12rustc_middle2ty7contextNtB5_13CtxtInterners16intern_predicate                                      
     0.27%  sprite-sheet-wa  libc.so.6                               [.] __memcmp_evex_movbe                                                                                                            
     0.25%  ld.mold          [kernel.kallsyms]                       [k] memset_orig                                                                                                                    
```

## Assets and render resources

- Decoded images: 139 → 948 (432.6 MP, 1730.3 MB of decode work).
- Images resident at end: 835.
- **Resident image BYTES at end: 1730.3 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **841 images (373.9 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 32 GENERATED image(s) were allocated during gameplay (41.6 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

⚠ FIRST ROOM: 88 notable decode(s) (221.7 MP) landed AFTER the first `room-loaded` (frame 49) within a second — in the open. The neighbour PREFETCH is expected here (one ration per neighbouring room, by design; the hub's basement brings `ai_slop`); the player's own sheet or one of the room's placed characters is NOT — that is the first-room cover failing:

```text
   4.1MP  f     52  content://ambition_shared/__art/bundles/52e539e0f26f165dbbe1e193d850d752ad8234d40f4c2c62c7904757d636d596/runtime/full/sprites/obs_deck_window_spritesheet.png
   1.3MP  f     52  content://ambition_shared/__art/bundles/7b2e18c59fd353969aa1ef3a7dab32478b27a46fb01246109215292bfac1350c/runtime/full/sprites/castle_cells_room_spritesheet.png
   1.7MP  f     52  content://ambition_shared/__art/bundles/4771c5923143e4ecf2a9ba53dc3f275034d15d63cf44003ae2880cd4573a0e2a/runtime/full/sprites/tea_room_spritesheet.png
   4.1MP  f     52  content://ambition_shared/__art/bundles/c3bb3e114f14bf51680fc3db1f9e41af05c66e05cc6445b5cf61529692431ef2/runtime/full/sprites/hanoi_temple_spritesheet.png
   1.2MP  f     52  content://ambition_shared/__art/bundles/e06fa0b7e7d2633d3ac9d2034ecd0bc26353764650af5ced197dbf4a83c4415a/runtime/full/sprites/ossuary_room_spritesheet.png
   1.8MP  f     52  content://ambition_shared/__art/bundles/6a3532230602c226a3c430262a0d45ddd79d228769e0672be71804af8116b09c/runtime/full/sprites/hill_east_spritesheet.png
   1.6MP  f     52  content://ambition_shared/__art/bundles/111c06cf11ae5067bd28e6003d7d00b16b1b43b595ff6ef2bb006ad46f25ac35/runtime/full/sprites/hill_west_spritesheet.png
   1.8MP  f     52  content://ambition_shared/__art/bundles/8ac0aeb4a558af5c8618c929c23727ce2c413c2f23eefc55a57bda09de32092e/runtime/full/sprites/pass_bamboo_spritesheet.png
```

⚠ 88 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  33x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 1275324 bytes

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

