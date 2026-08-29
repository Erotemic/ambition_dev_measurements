# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `4dd43f8202f6` on `main` |
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

Observed span of the game's own log: **25.5s**.

## Frame time

21 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      2.1       2   415.4   423.2   423.2   423.2    423.2
      3.1      15    68.7    46.5   171.6   260.6    260.6
     13.4      15    73.4    44.0   166.6   256.1    256.1
      8.2      21    48.7    45.8    60.1    88.9     88.9
      6.2      23    45.0    41.3    71.4    83.4     83.4
      5.1      19    53.0    54.4    79.0    82.4     82.4
     11.3      23    44.4    40.9    64.9    69.9     69.9
     12.3      24    43.1    42.6    52.6    67.4     67.4
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    3.433s     423.4 ms
    3.840s     407.3 ms
    4.101s     260.8 ms
    4.272s     171.6 ms
    4.374s      85.5 ms
    7.109s      83.0 ms
    6.853s      82.2 ms
    6.419s      78.9 ms
    6.097s      67.8 ms
    5.460s      63.4 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.8        4       3      1       0      1
      2.1        4       3      1       0      1
      3.1        4       3      1       0      1
      4.1        4       3      1       0      1
      5.1        4       3      1       0      1
      6.2        4       3      1       0      1
      7.2        4       3      1       0      1
      8.2        4       3      1       0      1
      9.2        4       3      1       0      1
     10.2        4       3      1       0      1
     11.3        4       3      1       0      1
     12.3        4       3      1       0      1
     13.4        4       3      1       0      1
     14.4        4       3      1       0      1
     15.4        4       3      1       0      1
     16.4        4       3      1       0      1
     17.4        4       3      1       0      1
     18.4        4       3      1       0      1
     19.4        4       3      1       0      1
     20.4        4       3      1       0      1
     21.4        4       3      1       0      1
     22.4        4       3      1       0      1
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
      2.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      3.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      4.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      5.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      6.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      7.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      8.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      9.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     10.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     11.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     12.3     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     13.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     14.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     15.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     16.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     17.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     18.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     19.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     20.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     21.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     22.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.8s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         109        0        0
       end       2048         429        2        0
      peak       2048         429        2        0
```

Peak sprites: **46** (42 visible), text2d 10, per-view projections 6 at t=18.4s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3300** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         3.067          8.785       20  render/main_transparent_pass_2d/elapsed_gpu
         3.031          7.732       20  render/ui/elapsed_gpu
         2.471          3.080       20  render/msaa_writeback/elapsed_gpu
         1.956          2.852       20  render/upscaling/elapsed_gpu
         0.020          0.042       20  render/ui/elapsed_cpu
         0.019          0.052       20  render/main_transparent_pass_2d/elapsed_cpu
         0.002          0.005       20  render/main_opaque_pass_2d/elapsed_cpu
         0.001          0.004       20  render/msaa_writeback/elapsed_cpu
         0.001          0.002       20  render/main_opaque_pass_2d/elapsed_gpu
         0.001          0.003       20  render/upscaling/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
  11462281.200   29507532.000       20  render/main_transparent_pass_2d/fragment_shader_invocations
   6678028.800   18667884.000       20  render/ui/fragment_shader_invocations
   5760000.000    5760000.000       20  render/msaa_writeback/fragment_shader_invocations
   5760000.000    5760000.000       20  render/upscaling/fragment_shader_invocations
       992.400       2344.000       20  render/ui/vertex_shader_invocations
       496.200       1172.000       20  render/ui/clipper_invocations
       466.400       1144.000       20  render/ui/clipper_primitives_out
       181.600        660.000       20  render/main_transparent_pass_2d/vertex_shader_invocations
        90.800        330.000       20  render/main_transparent_pass_2d/clipper_invocations
        90.800        330.000       20  render/main_transparent_pass_2d/clipper_primitives_out
         3.000          3.000       20  render/msaa_writeback/vertex_shader_invocations
         3.000          3.000       20  render/upscaling/vertex_shader_invocations
         1.000          1.000       20  render/msaa_writeback/clipper_primitives_out
         1.000          1.000       20  render/upscaling/clipper_primitives_out
         1.000          1.000       20  render/msaa_writeback/clipper_invocations
         1.000          1.000       20  render/upscaling/clipper_invocations
         0.000          0.000       20  render/main_opaque_pass_2d/clipper_invocations
         0.000          0.000       20  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       20  render/main_opaque_pass_2d/vertex_shader_invocations
         0.000          0.000       20  render/main_opaque_pass_2d/fragment_shader_invocations
```

- CPU pass timings: **measured** (5 spans).
- GPU pass timings: **measured** (5 spans).
- Pipeline statistics: **measured** (20 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
tracy-capture/tracy-csvexport not found; per-system timings unavailable (run ./run_developer_setup.sh)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 409 frames, summing to 53.02ms:

```text
   16.05 ms   30.3%  PreUpdate
   12.30 ms   23.2%  outside
   11.32 ms   21.4%  Update
    6.22 ms   11.7%  PostUpdate
    4.79 ms    9.0%  RunFixedMainLoop
    0.74 ms    1.4%  Last
    0.70 ms    1.3%  StateTransition
    0.62 ms    1.2%  First
    0.27 ms    0.5%  SpawnScene
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  97.4%  the game itself
   1.9%  build tooling
   0.7%  audio
```

No profiler threads were sampled, so nothing but `perf` itself was
observing the game and the frame times in this bundle are the honest ones.
A `build tooling` share is the launcher's own `cargo` resolving the build;
it competes for cores but is not attributed to the game.

## Where the native time went

```text
  64.0%  game binary + its Rust/C deps
  33.0%  kernel
   1.8%  GPU driver / graphics stack
   1.2%  audio
```

From `perf-report-by-dso.txt`. If the top bucket is not the game binary,
ranking game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     3.01%  ambition_game_b  [unknown]                      [.] 0000000000000000                                                                                                                        
     2.66%  Compute Task Po  libc.so.6                      [.] __memmove_avx_unaligned_erms                                                                                                            
     1.73%  ambition_game_b  ambition_game_bin              [.] core::slice::sort::unstable::ipnsort::<alloc::string::String, <[alloc::string::String]>::sort_unstable_by_key<usize, <alloc::string::Str
     1.73%  ambition_game_b  ambition_game_bin              [.] <str as core::fmt::Debug>::fmt                                                                                                          
     1.72%  ambition_game_b  ambition_game_bin              [.] <&mut bevy_falling_sand::render::pipeline::textures::setup_world_textures as core::ops::function::FnMut<(bevy_ecs::system::commands::Com
     1.70%  ambition_game_b  ambition_game_bin              [.] <bevy_ecs::schedule::executor::single_threaded::SingleThreadedExecutor as bevy_ecs::schedule::executor::SystemExecutor>::run            
     1.57%  Compute Task Po  [kernel.kallsyms]              [k] __get_vma_policy                                                                                                                        
     1.27%  ambition_game_b  [kernel.kallsyms]              [k] __irqentry_text_end                                                                                                                     
     1.25%  ambition_game_b  [kernel.kallsyms]              [k] cgroup_sk_alloc                                                                                                                         
     1.18%  ambition_game_b  ambition_game_bin              [.] <bevy_input::gamepad::GamepadButton as leafwing_input_manager::user_input::UserInput>::decompose                                        
     1.16%  ambition_game_b  ambition_game_bin              [.] <sharded_slab::pool::Pool<tracing_subscriber::registry::sharded::DataInner>>::get                                                       
     1.05%  ambition_game_b  ambition_game_bin              [.] <ambition_characters::prepared::PreparedCharacterRegistry>::get                                                                         
     1.01%  ambition_game_b  ambition_game_bin              [.] _RNvMs3_NtCs6oYcxvEZuo2_9hashbrown3mapINtB5_7HashMapINtNtCscHgRw1M2fX5_5alloc5boxed3BoxDNtNtCs2fJztW0ZAb5_22leafwing_input_manager10user
     0.99%  ambition_game_b  ambition_game_bin              [.] <bevy_ecs::query::state::QueryState<&bevy_render::camera::ExtractedCamera>>::update_archetypes_unsafe_world_cell                        
     0.99%  ambition_game_b  ambition_game_bin              [.] _RNvXsa_NtNtCsdLvyDDwu5y2_8bevy_ecs6system15function_systemINtB5_14FunctionSystemFNtNtB7_8commands8CommandsINtNtB7_12system_param5Localj
     0.99%  ambition_game_b  libvulkan_intel.so             [.] 0x00000000002823b0                                                                                                                      
     0.98%  ambition_game_b  ambition_game_bin              [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::l
     0.96%  Compute Task Po  ambition_game_bin              [.] _RNvMs1_NtCs14YtULSvDw6_10async_task3rawINtB5_7RawTaskINtCs3ODmb7WFdax_14async_executor15AsyncCallOnDropINtNtCsLBeAIKUTrH_12futures_lite
     0.96%  Compute Task Po  [unknown]                      [.] 0000000000000000                                                                                                                        
     0.96%  ambition_game_b  [kernel.kallsyms]              [k] free_unref_page_list                                                                                                                    
     0.92%  ambition_game_b  ambition_game_bin              [.] _mi_page_malloc_zero                                                                                                                    
     0.84%  ambition_game_b  ambition_game_bin              [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::l
     0.82%  ambition_game_b  libc.so.6                      [.] __memmove_avx_unaligned_erms                                                                                                            
     0.81%  ambition_game_b  ambition_game_bin              [.] <bevy_ecs::query::access::Access>::is_compatible                                                                                        
     0.77%  ambition_game_b  ambition_game_bin              [.] mi_theap_umalloc                                                                                                                        
     0.76%  ambition_game_b  [kernel.kallsyms]              [k] select_task_rq_fair                                                                                                                     
     0.75%  Compute Task Po  ambition_game_bin              [.] <futures_lite::future::Or<futures_lite::future::Or<<bevy_tasks::task_pool::TaskPool>::new_internal::{closure#0}::{closure#0}::{closure#0
     0.75%  ambition_game_b  [kernel.kallsyms]              [k] dequeue_task_fair                                                                                                                       
     0.72%  IO Task Pool (0  [unknown]                      [.] 0000000000000000                                                                                                                        
     0.72%  Compute Task Po  [kernel.kallsyms]              [k] syscall_exit_to_user_mode                                                                                                               
     0.70%  ambition_game_b  [kernel.kallsyms]              [k] syscall_return_via_sysret                                                                                                               
     0.70%  IO Task Pool (0  ambition_game_bin              [.] <regex_automata::util::pool::inner::Pool<regex_automata::meta::regex::Cache, alloc::boxed::Box<dyn core::ops::function::Fn<(), Output = 
     0.65%  ambition_game_b  ambition_game_bin              [.] <bevy_ecs::storage::sparse_set::SparseSet<bevy_ecs::component::info::ComponentId, bevy_ecs::message::messages::Messages<bevy_ecs::lifecy
     0.65%  ambition_game_b  ambition_game_bin              [.] core::slice::sort::unstable::quicksort::quicksort::<alloc::string::String, <[alloc::string::String]>::sort_unstable_by_key<usize, <alloc
     0.64%  ambition_game_b  [kernel.kallsyms]              [k] __mod_node_page_state                                                                                                                   
```

## Assets and render resources

- Decoded images: 87 → 175 (124.1 MP, 496.4 MB of decode work).
- Images resident at end: 153.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **135 images (96.2 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 29 decode(s) landed before the first `room-loaded` (91.1 MP) — boot. Not a gameplay hitch.

⚠ 2 decode(s) landed WITHIN 3s of a `room-loaded` — a room still arriving. Expected, and the reason "during gameplay" alone is not the contract.

⛔ **8 of 39 notable decodes landed during SETTLED play** (11.1 MP) — more than 3s after the last room finished loading. Each one cost a frame.

Worst offenders by megapixels:

```text
   2.0MP  at 22.854s  sprites/noether_portraits.png
   1.3MP  at 22.854s  sprites/oiler_portraits.png
   1.3MP  at 22.854s  sprites/player_robot_v3_portraits.png
   1.3MP  at 22.901s  sprites/patent_clerk_portraits.png
   1.3MP  at 22.901s  sprites/carl_stargan_portraits.png
   1.3MP  at 22.901s  sprites/performer_portraits.png
   1.3MP  at 22.901s  sprites/author_portraits.png
   1.3MP  at 22.956s  sprites/medic_portraits.png
```

Textures decoded more than once:

```text
   2x  <runtime-generated> — allocated during gameplay. No asset path, so this is generated (an atlas or a render target), not content that could have been demanded earlier.
   2x  sprites/oiler_portraits.png
   2x  sprites/performer_portraits.png
   2x  sprites/noether_portraits.png
   2x  sprites/patent_clerk_portraits.png
   2x  sprites/medic_portraits.png
   2x  sprites/author_portraits.png
   2x  sprites/player_robot_v3_portraits.png
   2x  sprites/carl_stargan_portraits.png
   2x  sprites/officer_portraits.png
```

## Collection status

- `warm-build`: 0
- `perf-record`: 139
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 217380 bytes

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

