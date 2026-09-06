# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `143d37a96aea` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile ` |
| host | `calculex` |
| kernel | `Linux calculex 5.15.0-187-generic #197-Ubuntu SMP Fri Jul 17 19:17:01 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `n/a` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: Intel(R) Core(TM) i7-7700HQ CPU @ 2.80GHz
- logical_cpus=8
- MemTotal:       32393480 kB

## Renderer

```text
AdapterInfo { name: "Intel(R) HD Graphics 630 (KBL GT2)", vendor: 32902, device: 22811, device_type: IntegratedGpu, driver: "Intel open-source Mesa driver", driver_info: "Mesa 23.2.1-1ubuntu3.1~22.04.4", backend: Vulkan }
```

Hardware rendering was available and used.

## Session

Observed span of the game's own log: **21.7s**.

## Frame time

11 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      6.3       1   929.5   929.5   929.5   929.5    929.5
      7.3      22    45.9    17.5    39.2   632.4    632.4
      5.2       1   391.0   391.0   391.0   391.0    391.0
     12.3      47    21.4    18.7    37.9    39.5     39.5
      9.3      59    16.9    16.2    22.4    34.9     39.5
     15.4      59    17.0    16.0    27.9    34.6     36.9
     11.3      58    17.2    16.6    27.2    35.5     35.6
     10.3      58    17.4    16.1    27.3    32.1     35.4
```

Full series: `frame_times.csv`.

20 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   11.270s     929.5 ms
   11.903s     632.6 ms
   10.340s     391.1 ms
   13.721s      39.3 ms
   17.098s      39.3 ms
   11.942s      39.1 ms
   16.720s      38.3 ms
   17.172s      38.1 ms
   16.989s      37.6 ms
   19.910s      36.7 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      4.1        4       3      1       0      1
      5.2        4       3      1       0      1
      6.3        4       3      1       0      1
      7.3        4       3      1       0      1
      8.3        4       3      1       0      1
      9.3        4       3      1       0      1
     10.3        4       3      1       0      1
     11.3        4       3      1       0      1
     12.3        4       3      1       0      1
     13.3        4       3      1       0      1
     14.4        4       3      1       0      1
     15.4        4       3      1       0      1
```

Peak world-rendering cameras: **1** at t=4.1s.

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

Peak active portal capture rigs: **0** of 0 at t=4.1s.

```text
        t  rigs  active  budget
      4.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      5.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      6.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      7.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      8.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      9.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     10.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     11.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     12.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     13.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     14.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     15.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=4.1s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         109        0        0
       end       2048         158        0        0
      peak       2048         158        0        0
```

Peak sprites: **0** (0 visible), text2d 0, per-view projections 0 at t=4.1s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3199** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         4.100          6.475        9  render/ui/elapsed_gpu
         0.646          0.817        9  render/upscaling/elapsed_gpu
         0.167          0.740        9  render/main_transparent_pass_2d/elapsed_gpu
         0.011          0.020        9  render/ui/elapsed_cpu
         0.006          0.022        9  render/main_transparent_pass_2d/elapsed_cpu
         0.003          0.003        9  render/main_opaque_pass_2d/elapsed_gpu
         0.001          0.002        9  render/main_opaque_pass_2d/elapsed_cpu
         0.001          0.002        9  render/upscaling/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   3674025.778    4790772.000        9  render/ui/fragment_shader_invocations
   1440000.000    1440000.000        9  render/upscaling/fragment_shader_invocations
    320400.000    1441800.000        9  render/main_transparent_pass_2d/fragment_shader_invocations
       488.444        568.000        9  render/ui/vertex_shader_invocations
       244.222        284.000        9  render/ui/clipper_invocations
       215.333        256.000        9  render/ui/clipper_primitives_out
         3.000          3.000        9  render/upscaling/vertex_shader_invocations
         1.000          1.000        9  render/upscaling/clipper_invocations
         1.000          1.000        9  render/upscaling/clipper_primitives_out
         0.889          4.000        9  render/main_transparent_pass_2d/vertex_shader_invocations
         0.444          2.000        9  render/main_transparent_pass_2d/clipper_invocations
         0.444          2.000        9  render/main_transparent_pass_2d/clipper_primitives_out
         0.000          0.000        9  render/main_opaque_pass_2d/clipper_invocations
         0.000          0.000        9  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000        9  render/main_opaque_pass_2d/vertex_shader_invocations
         0.000          0.000        9  render/main_opaque_pass_2d/fragment_shader_invocations
```

- CPU pass timings: **measured** (4 spans).
- GPU pass timings: **measured** (4 spans).
- Pipeline statistics: **measured** (16 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
tracy-capture produced no trace (the game never connected)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 483 frames, summing to 23.40ms:

```text
    8.76 ms   37.5%  outside
    5.36 ms   22.9%  Update
    3.65 ms   15.6%  PostUpdate
    2.56 ms   10.9%  PreUpdate
    1.83 ms    7.8%  RunFixedMainLoop
    0.55 ms    2.3%  Last
    0.32 ms    1.4%  StateTransition
    0.23 ms    1.0%  First
    0.14 ms    0.6%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  94.7%  the game itself
   4.4%  build tooling
   0.9%  audio
```

No profiler threads were sampled, so nothing but `perf` itself was
observing the game and the frame times in this bundle are the honest ones.
A `build tooling` share is the launcher's own `cargo` resolving the build;
it competes for cores but is not attributed to the game.

## Where the native time went

```text
  57.5%  game binary + its Rust/C deps
  41.3%  kernel
   1.2%  GPU driver / graphics stack
```

From `perf-report-by-dso.txt`. If the top bucket is not the game binary,
ranking game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     3.46%  Compute Task Po  ambition_game_bin      [.] <bevy_ecs::schedule::executor::multi_threaded::Context>::tick_executor                                                                          
     2.27%  ambition_game_b  ambition_game_bin      [.] <alloc::string::String as core::fmt::Write>::write_str                                                                                          
     2.23%  ambition_game_b  [kernel.kallsyms]      [k] native_irq_return_iret                                                                                                                          
     2.02%  Compute Task Po  ambition_game_bin      [.] <futures_lite::future::CatchUnwind<core::panic::unwind_safe::AssertUnwindSafe<bevy_falling_sand::movement::processing::systems::par_handle_movem
     1.75%  ambition_game_b  ambition_game_bin      [.] <bevy_ecs::query::state::QueryState<&mut ambition_encounter::music::EncounterMusicRequest, bevy_ecs::query::filter::With<ambition_platformer2d_s
     1.69%  ambition_game_b  ambition_game_bin      [.] <bevy_ecs::component::register::ComponentsRegistrator>::apply_queued_registrations                                                              
     1.58%  Compute Task Po  ambition_game_bin      [.] <futures_lite::future::Or<futures_lite::future::Or<<bevy_tasks::task_pool::TaskPool>::new_internal::{closure#0}::{closure#0}::{closure#0}::{clos
     1.55%  ambition_game_b  [kernel.kallsyms]      [k] __split_vma                                                                                                                                     
     1.36%  ambition_game_b  ambition_game_bin      [.] <bevy_ecs::system::function_system::FunctionSystem<fn(bevy_ecs::system::query::Query<bevy_ecs::entity::Entity, (bevy_ecs::query::filter::Or<(bev
     1.27%  ambition_game_b  ambition_game_bin      [.] <bevy_ecs::system::function_system::FunctionSystem<fn(bevy_ecs::system::commands::Commands, bevy_ecs::system::query::Query<(&mut bevy_lunex::UiL
     1.23%  ambition_game_b  [kernel.kallsyms]      [k] __reset_isolation_pfn                                                                                                                           
     1.12%  ambition_game_b  [kernel.kallsyms]      [k] clear_page_erms                                                                                                                                 
     1.10%  IO Task Pool (0  ambition_game_bin      [.] <symphonia_codec_vorbis::residue::Residue>::read_residue                                                                                        
     1.07%  ambition_game_b  [unknown]              [.] 0000000000000000                                                                                                                                
     1.02%  ambition_game_b  ambition_game_bin      [.] core::slice::sort::unstable::quicksort::quicksort::<alloc::string::String, <[alloc::string::String]>::sort_unstable_by_key<usize, <alloc::string
     1.00%  ambition_game_b  ambition_game_bin      [.] <bevy_ui::experimental::ghost_hierarchy::UiChildren>::iter_ui_children                                                                          
     0.97%  ambition_game_b  libc.so.6              [.] __memmove_avx_unaligned_erms                                                                                                                    
     0.90%  ambition_game_b  ambition_game_bin      [.] ron::parse::is_ident_raw_char                                                                                                                   
     0.88%  IO Task Pool (0  [unknown]              [.] 0000000000000000                                                                                                                                
     0.83%  ambition_game_b  ambition_game_bin      [.] _RNvXsa_NtNtCsdLvyDDwu5y2_8bevy_ecs6system15function_systemINtB5_14FunctionSystemFNtNtB7_8commands8CommandsINtNtB7_12system_param5LocaljEINtNtCs
     0.82%  threaded-ml      [kernel.kallsyms]      [k] rt_mutex_slowlock_block.constprop.0                                                                                                             
     0.78%  Compute Task Po  [kernel.kallsyms]      [k] __irqentry_text_end                                                                                                                             
     0.78%  ambition_game_b  ambition_game_bin      [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::layer_fil
     0.72%  ambition_game_b  ambition_game_bin      [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::layer_fil
     0.70%  IO Task Pool (0  ambition_game_bin      [.] _mi_malloc_generic                                                                                                                              
     0.69%  ambition_game_b  [kernel.kallsyms]      [k] sync_regs                                                                                                                                       
     0.69%  ambition_game_b  ambition_game_bin      [.] _mi_page_malloc_zero                                                                                                                            
     0.66%  ambition_game_b  ambition_game_bin      [.] <tracing_subscriber::registry::sharded::Registry as tracing_core::subscriber::Subscriber>::try_close                                            
     0.60%  Compute Task Po  ambition_game_bin      [.] <tracing_subscriber::filter::env::EnvFilter>::cares_about_span                                                                                  
     0.59%  Async Compute T  ambition_game_bin      [.] <regex_automata::meta::strategy::Core as regex_automata::meta::strategy::Strategy>::search_half                                                 
     0.56%  ambition_game_b  [kernel.kallsyms]      [k] page_remove_rmap                                                                                                                                
     0.53%  bash             [kernel.kallsyms]      [k] next_uptodate_page                                                                                                                              
     0.52%  ambition_game_b  ambition_game_bin      [.] <ron::parse::Parser>::next_chars_while_from_len                                                                                                 
     0.52%  ambition_game_b  [kernel.kallsyms]      [k] syscall_return_via_sysret                                                                                                                       
     0.51%  cargo            cargo                  [.] <cargo::sources::registry::http_remote::HttpRegistry as cargo::sources::registry::RegistryData>::load::{closure#0}                              
```

## Assets and render resources

- Decoded images: 85 → 129 (93.6 MP, 374.4 MB of decode work).
- Images resident at end: 129.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **85 images (23.0 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 20 decode(s) landed before the first `room-loaded` (77.6 MP) — boot. Not a gameplay hitch.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   2x  <runtime-generated> — allocated during gameplay. No asset path, so this is generated (an atlas or a render target), not content that could have been demanded earlier.
```

## Collection status

- `warm-build`: 0
- `perf-record`: 139
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 157856 bytes

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

