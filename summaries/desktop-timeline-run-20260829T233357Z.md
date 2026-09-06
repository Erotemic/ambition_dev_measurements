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

Observed span of the game's own log: **14.7s**.

## Frame time

10 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.0       1   904.4   904.4   904.4   904.4    904.4
      3.5       2   506.6   526.6   526.6   526.6    526.6
      8.5      31    32.9    17.4   151.3   171.7    171.7
      4.5      57    17.6    16.9    22.8    26.5     54.1
      5.5      59    17.2    16.5    22.6    36.1     38.0
      7.5      60    16.9    16.5    27.5    31.4     36.9
      6.5      60    16.6    16.6    19.1    19.4     32.3
     10.5      60    16.8    16.4    20.4    25.5     26.0
```

Full series: `frame_times.csv`.

16 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    4.073s     904.6 ms
    4.600s     526.8 ms
    5.086s     486.5 ms
    9.685s     171.7 ms
    9.947s     151.3 ms
    9.513s      61.4 ms
    9.796s      61.1 ms
    5.140s      54.3 ms
   10.001s      54.3 ms
    9.735s      50.1 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.8        4       3      1       0      1
      2.0        4       3      1       0      1
      3.5        4       3      1       0      1
      4.5        4       3      1       0      1
      5.5        4       3      1       0      1
      6.5        4       3      1       0      1
      7.5        4       3      1       0      1
      8.5        4       3      1       0      1
      9.5        4       3      1       0      1
     10.5        4       3      1       0      1
     11.6        4       3      1       0      1
```

Peak world-rendering cameras: **1** at t=0.8s.

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

Peak active portal capture rigs: **0** of 0 at t=0.8s.

```text
        t  rigs  active  budget
      0.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      2.0     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      3.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      4.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      5.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      6.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      7.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      8.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      9.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     10.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     11.6     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.8s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         109        0        0
       end       4096         459        4        1
      peak       4096         459        4        1
```

⚠ Entity count rose by 2048 over the session and was **still
climbing in the second half** (+2048 after t=6.5s). Growth that never falls across room
transitions is the shape of a lifecycle leak; check `runtime_census.csv`
against the room markers in `timeline.md`.

Peak sprites: **163** (62 visible), text2d 46, per-view projections 18 at t=9.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3301** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         2.440          6.822        9  render/ui/elapsed_gpu
         1.760          5.114        9  render/main_transparent_pass_2d/elapsed_gpu
         0.695          0.842        8  render/upscaling/elapsed_gpu
         0.020          0.052        9  render/main_transparent_pass_2d/elapsed_cpu
         0.012          0.026        9  render/ui/elapsed_cpu
         0.003          0.003        9  render/main_opaque_pass_2d/elapsed_gpu
         0.001          0.003        9  render/main_opaque_pass_2d/elapsed_cpu
         0.001          0.003        8  render/upscaling/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   2933146.667    7864696.000        9  render/main_transparent_pass_2d/fragment_shader_invocations
   1759160.000    4681940.000        9  render/ui/fragment_shader_invocations
   1440000.000    1440000.000        8  render/upscaling/fragment_shader_invocations
       990.222       3240.000        9  render/main_transparent_pass_2d/vertex_shader_invocations
       608.889        780.000        9  render/ui/vertex_shader_invocations
       495.111       1620.000        9  render/main_transparent_pass_2d/clipper_invocations
       445.778       1470.000        9  render/main_transparent_pass_2d/clipper_primitives_out
       304.444        390.000        9  render/ui/clipper_invocations
       276.889        362.000        9  render/ui/clipper_primitives_out
         3.000          3.000        8  render/upscaling/vertex_shader_invocations
         1.000          1.000        8  render/upscaling/clipper_invocations
         1.000          1.000        8  render/upscaling/clipper_primitives_out
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

Mean milliseconds per frame over 452 frames, summing to 23.89ms:

```text
    7.43 ms   31.1%  outside
    4.83 ms   20.2%  Update
    4.83 ms   20.2%  PreUpdate
    4.34 ms   18.1%  PostUpdate
    1.49 ms    6.2%  RunFixedMainLoop
    0.34 ms    1.4%  StateTransition
    0.31 ms    1.3%  Last
    0.19 ms    0.8%  First
    0.13 ms    0.6%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  94.5%  the game itself
   4.5%  build tooling
   1.0%  audio
```

No profiler threads were sampled, so nothing but `perf` itself was
observing the game and the frame times in this bundle are the honest ones.
A `build tooling` share is the launcher's own `cargo` resolving the build;
it competes for cores but is not attributed to the game.

## Where the native time went

```text
  59.0%  game binary + its Rust/C deps
  37.7%  kernel
   2.6%  GPU driver / graphics stack
   0.8%  audio
```

From `perf-report-by-dso.txt`. If the top bucket is not the game binary,
ranking game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     5.70%  ambition_game_b  [unknown]                [.] 0000000000000000                                                                                                                              
     3.04%  Compute Task Po  [kernel.kallsyms]        [k] migrate_page_move_mapping                                                                                                                     
     2.26%  ambition_game_b  [i915]                   [k] shmem_get_pages                                                                                                                               
     2.26%  IO Task Pool (0  ambition_game_bin        [.] <symphonia_codec_vorbis::floor::Floor1 as symphonia_codec_vorbis::floor::Floor>::synthesis                                                    
     2.11%  ambition_game_b  libc.so.6                [.] __memmove_avx_unaligned_erms                                                                                                                  
     1.94%  Compute Task Po  libvulkan_intel.so       [.] 0x00000000003f3de8                                                                                                                            
     1.81%  ambition_game_b  [kernel.kallsyms]        [k] hrtimer_interrupt                                                                                                                             
     1.59%  ambition_game_b  ambition_game_bin        [.] <bevy_render::renderer::graph_runner::RenderGraphRunner>::run_graph                                                                           
     1.54%  Compute Task Po  ambition_game_bin        [.] <futures_lite::future::CatchUnwind<core::panic::unwind_safe::AssertUnwindSafe<bevy_falling_sand::movement::processing::systems::par_handle_mov
     1.43%  Compute Task Po  [kernel.kallsyms]        [k] __pagevec_lru_add_fn                                                                                                                          
     1.21%  ambition_game_b  ambition_game_bin        [.] mi_free                                                                                                                                       
     1.17%  ambition_game_b  ambition_game_bin        [.] <tracing_subscriber::registry::sharded::Registry as tracing_subscriber::registry::LookupSpan>::span_data                                      
     1.16%  cargo            [kernel.kallsyms]        [k] __irqentry_text_end                                                                                                                           
     1.01%  ambition_game_b  [kernel.kallsyms]        [k] shmem_add_to_page_cache                                                                                                                       
     0.95%  IO Task Pool (0  [kernel.kallsyms]        [k] smp_call_function_many_cond                                                                                                                   
     0.90%  ambition_game_b  [kernel.kallsyms]        [k] __irqentry_text_end                                                                                                                           
     0.88%  Compute Task Po  [kernel.kallsyms]        [k] ttwu_queue_wakelist                                                                                                                           
     0.87%  ambition_game_b  ambition_game_bin        [.] core::slice::sort::unstable::quicksort::quicksort::<alloc::string::String, <[alloc::string::String]>::sort_unstable_by_key<usize, <alloc::stri
     0.85%  Async Compute T  ambition_game_bin        [.] <naga_oil::compose::tokenizer::Tokenizer>::new                                                                                                
     0.83%  ambition_game_b  ambition_game_bin        [.] ___tracy_emit_zone_begin_alloc                                                                                                                
     0.82%  Compute Task Po  ambition_game_bin        [.] <tracing_subscriber::filter::layer_filters::Filtered<alloc::boxed::Box<dyn tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered
     0.82%  Compute Task Po  [kernel.kallsyms]        [k] syscall_return_via_sysret                                                                                                                     
     0.77%  ambition_game_b  ambition_game_bin        [.] _RNvXs3_NtCs313PAFOJvMO_10antlr_rust10atn_configNtB5_9ATNConfigNtNtCsc36rpYXAlPq_4core5clone5Clone5clone.llvm.1683413308022219913             
     0.76%  Compute Task Po  ambition_game_bin        [.] <tracing_tracy::utils::StrCacheGuard as core::ops::drop::Drop>::drop                                                                          
     0.75%  Compute Task Po  ambition_game_bin        [.] <alloc::vec::Vec<(core::option::Option<bevy_render::render_resource::bind_group::BindGroupId>, alloc::vec::Vec<u32>)>>::extend_with           
     0.74%  IO Task Pool (0  [kernel.kallsyms]        [k] unmap_and_move                                                                                                                                
     0.71%  ambition_game_b  ambition_game_bin        [.] _mi_page_malloc_zero                                                                                                                          
     0.63%  Compute Task Po  [unknown]                [.] 0000000000000000                                                                                                                              
     0.62%  IO Task Pool (0  ambition_game_bin        [.] _mi_strnicmp                                                                                                                                  
     0.61%  bash             [kernel.kallsyms]        [k] __irqentry_text_end                                                                                                                           
     0.58%  ambition_game_b  ambition_game_bin        [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::layer_f
     0.58%  threaded-ml      [kernel.kallsyms]        [k] rt_mutex_slowlock_block.constprop.0                                                                                                           
     0.52%  ambition_game_b  ambition_game_bin        [.] <ron::parse::Parser>::src                                                                                                                     
     0.51%  Compute Task Po  ambition_game_bin        [.] <ron::parse::Parser>::next_chars_while_from_len                                                                                               
     0.51%  Compute Task Po  ambition_game_bin        [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::layer_f
```

## Assets and render resources

- Decoded images: 88 → 146 (106.8 MP, 427.2 MB of decode work).
- Images resident at end: 145.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **127 images (90.4 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 20 decode(s) landed before the first `room-loaded` (77.6 MP) — boot. Not a gameplay hitch.

⚠ 1 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

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
- `perf.data`: 148972 bytes

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

