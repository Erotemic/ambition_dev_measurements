# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `736351732443` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.98.0 (88d9e12ae 2026-08-18)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling ` |
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

Observed span of the game's own log: **131.0s**.

## Frame time

115 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     81.8       3  1975.8    18.3  5893.4  5893.4   5893.4
      6.3       1  1148.1  1148.1  1148.1  1148.1   1148.1
     82.9      17    58.7    16.6   194.4   526.9    526.9
      7.3      26    39.0    16.8   181.9   405.1    405.1
      5.0       1   316.5   316.5   316.5   316.5    316.5
     89.0      51    22.5    17.1    30.1   276.0    276.0
    117.4      29    34.6    25.7    67.9   155.8    155.8
     90.1      45    22.5    16.4    99.6   154.2    154.2
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   85.861s    5893.2 ms
   10.314s    1148.1 ms
   86.656s     526.8 ms
   10.719s     405.2 ms
    9.166s     316.4 ms
   93.047s     276.0 ms
   86.056s     194.6 ms
   10.958s     181.9 ms
   93.215s     154.4 ms
   89.719s     130.1 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      3.8        4       3      1       0      1
      9.3        4       3      1       0      1
     14.4        4       3      1       0      1
     19.4        4       3      1       0      1
     24.5        4       3      1       0      1
     29.5        4       3      1       0      1
     34.6        4       3      1       0      1
     39.6        4       3      1       0      1
     44.7        4       3      1       0      1
     49.7        4       3      1       0      1
     54.8        4       3      1       0      1
     59.8        4       3      1       0      1
     64.8        4       3      1       0      1
     69.9        4       3      1       0      1
     74.9        4       3      1       0      1
     84.9        4       3      1       0      1
     90.1        4       3      1       0      1
     95.1        4       3      1       0      1
    100.2        4       3      1       0      1
    105.3        4       3      1       0      1
    110.3        4       3      1       0      1
    115.4        4       3      1       0      1
    120.4        4       3      1       0      1
    125.5        4       4      1       0      1
```

Peak world-rendering cameras: **1** at t=3.8s.

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

Peak active portal capture rigs: **0** of 0 at t=3.8s.

```text
        t  rigs  active  budget
      3.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     13.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     22.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     31.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     40.6     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     49.7     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     58.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     67.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     81.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     91.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
    100.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
    109.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
    118.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=3.8s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         109        0        0
       end       8192         663        2        1
      peak       8192         663        2        1
```

⚠ Entity count rose by 6144 over the session and was **still
climbing in the second half** (+6144 after t=62.8s). Growth that never falls across room
transitions is the shape of a lifecycle leak; check `runtime_census.csv`
against the room markers in `timeline.md`.

Peak sprites: **145** (40 visible), text2d 61, per-view projections 20 at t=121.4s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3301** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         9.051          9.902        3  render/main_opaque_pass_3d/elapsed_gpu
         2.380          6.665      113  render/ui/elapsed_gpu
         1.721          9.308      113  render/main_transparent_pass_2d/elapsed_gpu
         1.119          3.103      113  render/upscaling/elapsed_gpu
         0.306          0.339        3  render/main_transparent_pass_3d/elapsed_gpu
         0.045          0.057        3  render/main_transparent_pass_3d/elapsed_cpu
         0.026          0.112      113  render/main_transparent_pass_2d/elapsed_cpu
         0.026          0.097      113  render/ui/elapsed_cpu
         0.022          0.023        3  render/main_indirect_parameters_building/elapsed_gpu
         0.019          0.019        3  render/early_mesh_preprocessing/elapsed_gpu
         0.014          0.015        3  render/main_opaque_pass_3d/elapsed_cpu
         0.006          0.136      113  render/main_opaque_pass_2d/elapsed_cpu
         0.004          0.004        3  render/early_mesh_preprocessing/elapsed_cpu
         0.003          0.004      113  render/main_opaque_pass_2d/elapsed_gpu
         0.002          0.008      113  render/upscaling/elapsed_cpu
         0.002          0.003        3  render/main_indirect_parameters_building/elapsed_cpu
         0.002          0.002        3  render/late_mesh_preprocessing/elapsed_gpu
         0.000          0.001        3  render/late_mesh_preprocessing/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   7537393.333    8109368.000        3  render/main_opaque_pass_3d/fragment_shader_invocations
   7249815.363   41933676.000      113  render/main_transparent_pass_2d/fragment_shader_invocations
   2964004.248    7818240.000      113  render/upscaling/fragment_shader_invocations
   2127127.221   12736456.000      113  render/ui/fragment_shader_invocations
    169732.000     184860.000        3  render/main_transparent_pass_3d/fragment_shader_invocations
      1370.333       1409.000        3  render/main_transparent_pass_3d/vertex_shader_invocations
       693.876       2344.000      113  render/ui/vertex_shader_invocations
       677.667        697.000        3  render/main_transparent_pass_3d/clipper_invocations
       552.333        579.000        3  render/main_transparent_pass_3d/clipper_primitives_out
       346.938       1172.000      113  render/ui/clipper_invocations
       318.301       1140.000      113  render/ui/clipper_primitives_out
       256.000        256.000        3  render/main_indirect_parameters_building/compute_shader_invocations
       216.504       2092.000      113  render/main_transparent_pass_2d/vertex_shader_invocations
       164.000        164.000        3  render/main_opaque_pass_3d/vertex_shader_invocations
       128.000        128.000        3  render/early_mesh_preprocessing/compute_shader_invocations
       108.265       1046.000      113  render/main_transparent_pass_2d/clipper_invocations
        98.867        910.000      113  render/main_transparent_pass_2d/clipper_primitives_out
        86.000         86.000        3  render/main_opaque_pass_3d/clipper_primitives_out
        82.000         82.000        3  render/main_opaque_pass_3d/clipper_invocations
         3.000          3.000      113  render/upscaling/vertex_shader_invocations
         1.000          1.000      113  render/upscaling/clipper_invocations
         1.000          1.000      113  render/upscaling/clipper_primitives_out
         0.000          0.000      113  render/main_opaque_pass_2d/clipper_invocations
         0.000          0.000      113  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      113  render/main_opaque_pass_2d/vertex_shader_invocations
         0.000          0.000      113  render/main_opaque_pass_2d/fragment_shader_invocations
         0.000          0.000        3  render/late_mesh_preprocessing/compute_shader_invocations
```

- CPU pass timings: **measured** (9 spans).
- GPU pass timings: **measured** (9 spans).
- Pipeline statistics: **measured** (27 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
--no-tracy was passed; no per-system or per-render-pass timings were collected
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 6008 frames, summing to 20.25ms:

```text
    9.98 ms   49.3%  outside
    3.26 ms   16.1%  PreUpdate
    2.31 ms   11.4%  RunFixedMainLoop
    1.98 ms    9.8%  PostUpdate
    1.77 ms    8.8%  Update
    0.37 ms    1.8%  StateTransition
    0.25 ms    1.3%  Last
    0.17 ms    0.8%  First
    0.16 ms    0.8%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  97.7%  the game itself
   1.7%  build tooling
   0.7%  audio
```

No profiler threads were sampled, so nothing but `perf` itself was
observing the game and the frame times in this bundle are the honest ones.
A `build tooling` share is the launcher's own `cargo` resolving the build;
it competes for cores but is not attributed to the game.

## Where the native time went

```text
  65.5%  game binary + its Rust/C deps
  28.9%  kernel
   5.1%  GPU driver / graphics stack
   0.5%  audio
```

From `perf-report-by-dso.txt`. If the top bucket is not the game binary,
ranking game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     3.55%  ambition_game_b  [unknown]                       [.] 0000000000000000                                                                                                                       
     1.67%  ambition_game_b  libc.so.6                       [.] __memmove_avx_unaligned_erms                                                                                                           
     1.65%  Compute Task Po  [unknown]                       [.] 0000000000000000                                                                                                                       
     1.53%  ambition_game_b  ambition_game_bin               [.] _mi_page_malloc_zero                                                                                                                   
     1.39%  ambition_game_b  ambition_game_bin               [.] __rustc::__rust_alloc                                                                                                                  
     1.37%  ambition_game_b  ambition_game_bin               [.] mi_free                                                                                                                                
     1.31%  ambition_game_b  ambition_game_bin               [.] <bevy_ecs::schedule::executor::single_threaded::SingleThreadedExecutor as bevy_ecs::schedule::executor::SystemExecutor>::run           
     1.25%  ambition_game_b  ambition_game_bin               [.] <leafwing_input_manager::input_map::InputMap<ambition_input::actions::Platformer2dInputActionMonolith>>::process_actions               
     1.20%  Compute Task Po  [kernel.kallsyms]               [k] syscall_return_via_sysret                                                                                                              
     1.11%  Compute Task Po  ambition_game_bin               [.] <bevy_ecs::schedule::executor::multi_threaded::Context>::tick_executor                                                                 
     0.84%  Compute Task Po  ambition_game_bin               [.] <core::panic::unwind_safe::AssertUnwindSafe<bevy_falling_sand::movement::processing::systems::par_handle_movement_by_chunks::{closure#1
     0.64%  ambition_game_b  ambition_game_bin               [.] <bevy_ecs::schedule::executor::multi_threaded::Context>::tick_executor                                                                 
     0.64%  IO Task Pool (1  ambition_game_bin               [.] png::filter::paeth::unfilter                                                                                                           
     0.64%  Compute Task Po  ambition_game_bin               [.] <concurrent_queue::unbounded::Unbounded<async_task::runnable::Runnable>>::push                                                         
     0.63%  ambition_game_b  [kernel.kallsyms]               [k] syscall_return_via_sysret                                                                                                              
     0.63%  threaded-ml      [kernel.kallsyms]               [k] rt_mutex_slowlock_block.constprop.0                                                                                                    
     0.59%  ambition_game_b  ambition_game_bin               [.] <ambition_dev_tools::runtime_census::FramePhaseMark as core::any::Any>::type_id                                                        
     0.56%  ambition_game_b  ambition_game_bin               [.] mi_theap_malloc_aligned                                                                                                                
     0.55%  Compute Task Po  libc.so.6                       [.] __memmove_avx_unaligned_erms                                                                                                           
     0.55%  ambition_game_b  libvulkan_intel.so              [.] 0x0000000000090969                                                                                                                     
     0.54%  Compute Task Po  ambition_game_bin               [.] <bevy_ecs::system::function_system::FunctionSystem<fn(bevy_ecs::system::query::Query<(bevy_ecs::entity::Entity, &avian2d::dynamics::rig
     0.54%  ambition_game_b  ambition_game_bin               [.] <alloc::collections::btree::node::Handle<alloc::collections::btree::node::NodeRef<alloc::collections::btree::node::marker::Mut, alloc::
     0.51%  Compute Task Po  ambition_game_bin               [.] <core::panic::unwind_safe::AssertUnwindSafe<<bevy_ecs::schedule::executor::multi_threaded::ExecutorState>::spawn_system_task::{closure#
     0.46%  ambition_game_b  [kernel.kallsyms]               [k] __irqentry_text_end                                                                                                                    
     0.45%  Compute Task Po  ambition_game_bin               [.] <futures_lite::future::Or<futures_lite::future::Or<<bevy_tasks::task_pool::TaskPool>::new_internal::{closure#0}::{closure#0}::{closure#
     0.44%  threaded-ml      [kernel.kallsyms]               [k] syscall_return_via_sysret                                                                                                              
     0.44%  Compute Task Po  ambition_game_bin               [.] <async_executor::Ticker>::sleep                                                                                                        
     0.43%  Compute Task Po  [kernel.kallsyms]               [k] psi_group_change                                                                                                                       
     0.42%  Compute Task Po  ambition_game_bin               [.] <concurrent_queue::ConcurrentQueue<async_task::runnable::Runnable>>::pop                                                               
     0.41%  Compute Task Po  [kernel.kallsyms]               [k] entry_SYSCALL_64_after_hwframe                                                                                                         
     0.38%  ambition_game_b  [kernel.kallsyms]               [k] alloc_pages_vma                                                                                                                        
     0.37%  Compute Task Po  ambition_game_bin               [.] <async_task::raw::RawTask<async_executor::AsyncCallOnDrop<futures_lite::future::CatchUnwind<core::panic::unwind_safe::AssertUnwindSafe<
     0.36%  ambition_game_b  ambition_game_bin               [.] <bevy_input::keyboard::KeyCode as leafwing_input_manager::user_input::UserInput>::decompose                                            
     0.36%  ambition_game_b  ambition_game_bin               [.] <concurrent_queue::ConcurrentQueue<async_task::runnable::Runnable>>::pop                                                               
     0.36%  Compute Task Po  [kernel.kallsyms]               [k] remove_entity_load_avg                                                                                                                 
```

## Assets and render resources

- Decoded images: 86 → 207 (139.8 MP, 559.0 MB of decode work).
- Images resident at end: 185.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **127 images (90.4 MP)** at 5.2s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 28 decode(s) landed before the first `room-loaded` (88.7 MP) — boot. Not a gameplay hitch.

⚠ 3 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
   2x  <runtime-generated> — allocated during gameplay. No asset path, so this is generated (an atlas or a render target), not content that could have been demanded earlier.
   2x  sprites/performer_portraits.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 737628 bytes

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

