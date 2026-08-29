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

Observed span of the game's own log: **34.3s**.

## Frame time

23 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      6.4       1  1093.4  1093.4  1093.4  1093.4   1093.4
      5.2       1   383.9   383.9   383.9   383.9    383.9
      7.4      11    93.5    57.1   378.5   378.5    378.5
     16.7      10    99.5    59.3   269.9   269.9    269.9
     12.6      16    60.0    46.5    88.0   249.6    249.6
     15.7      22    49.2    45.3    64.7   112.7    112.7
     13.6      21    49.2    44.8    87.2   104.6    104.6
     28.0      19    56.8    47.4    90.8   100.1    100.1
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   10.948s    1093.5 ms
    9.854s     383.8 ms
   11.327s     378.6 ms
   11.485s     159.0 ms
   13.439s      76.0 ms
   11.562s      75.8 ms
   14.392s      74.6 ms
   11.829s      68.8 ms
   11.976s      63.0 ms
   13.501s      62.2 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      4.0        4       3      1       0      1
      5.2        4       3      1       0      1
      6.4        4       3      1       0      1
      7.4        4       3      1       0      1
      8.4        4       3      1       0      1
      9.5        4       3      1       0      1
     10.5        4       3      1       0      1
     11.5        4       3      1       0      1
     12.6        4       3      1       0      1
     13.6        4       3      1       0      1
     14.6        4       3      1       0      1
     15.7        4       3      1       0      1
     16.7        4       3      1       0      1
     17.7        4       3      1       0      1
     18.7        4       3      1       0      1
     19.8        4       3      1       0      1
     20.8        4       3      1       0      1
     21.8        4       3      1       0      1
     22.9        4       3      1       0      1
     23.9        4       3      1       0      1
     24.9        4       3      1       0      1
     25.9        4       3      1       0      1
     26.9        4       3      1       0      1
     28.0        4       3      1       0      1
```

Peak world-rendering cameras: **1** at t=4.0s.

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

Peak active portal capture rigs: **0** of 0 at t=4.0s.

```text
        t  rigs  active  budget
      4.0     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      6.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      8.4     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     10.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     12.6     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     14.6     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     16.7     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     18.7     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     20.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     22.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     24.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     26.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=4.0s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         109        0        0
       end       4096         608        2        1
      peak       4096         608        2        1
```

⚠ Entity count rose by 2048 over the session and was **still
climbing in the second half** (+2048 after t=16.7s). Growth that never falls across room
transitions is the shape of a lifecycle leak; check `runtime_census.csv`
against the room markers in `timeline.md`.

Peak sprites: **151** (46 visible), text2d 46, per-view projections 18 at t=28.0s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3300** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         4.129         18.041       21  render/main_transparent_pass_2d/elapsed_gpu
         3.125          8.052       21  render/ui/elapsed_gpu
         2.533          3.104       21  render/msaa_writeback/elapsed_gpu
         1.876          2.323       21  render/upscaling/elapsed_gpu
         0.027          0.095       21  render/main_transparent_pass_2d/elapsed_cpu
         0.022          0.092       21  render/ui/elapsed_cpu
         0.002          0.017       21  render/upscaling/elapsed_cpu
         0.002          0.007       21  render/main_opaque_pass_2d/elapsed_cpu
         0.001          0.002       21  render/main_opaque_pass_2d/elapsed_gpu
         0.001          0.005       21  render/msaa_writeback/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
  13516986.857   30932272.000       21  render/main_transparent_pass_2d/fragment_shader_invocations
   6920007.619   18667708.000       21  render/ui/fragment_shader_invocations
   5760000.000    5760000.000       21  render/msaa_writeback/fragment_shader_invocations
   5760000.000    5760000.000       21  render/upscaling/fragment_shader_invocations
       912.190       2344.000       21  render/ui/vertex_shader_invocations
       456.095       1172.000       21  render/ui/clipper_invocations
       426.000       1142.000       21  render/ui/clipper_primitives_out
       284.857       2064.000       21  render/main_transparent_pass_2d/vertex_shader_invocations
       142.571       1032.000       21  render/main_transparent_pass_2d/clipper_invocations
       136.095        896.000       21  render/main_transparent_pass_2d/clipper_primitives_out
         3.000          3.000       21  render/msaa_writeback/vertex_shader_invocations
         3.000          3.000       21  render/upscaling/vertex_shader_invocations
         1.000          1.000       21  render/msaa_writeback/clipper_primitives_out
         1.000          1.000       21  render/upscaling/clipper_primitives_out
         1.000          1.000       21  render/msaa_writeback/clipper_invocations
         1.000          1.000       21  render/upscaling/clipper_invocations
         0.000          0.000       21  render/main_opaque_pass_2d/clipper_invocations
         0.000          0.000       21  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000       21  render/main_opaque_pass_2d/vertex_shader_invocations
         0.000          0.000       21  render/main_opaque_pass_2d/fragment_shader_invocations
```

- CPU pass timings: **measured** (5 spans).
- GPU pass timings: **measured** (5 spans).
- Pipeline statistics: **measured** (20 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
tracy-capture produced no trace (the game never connected)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 409 frames, summing to 58.80ms:

```text
   18.63 ms   31.7%  PreUpdate
   13.33 ms   22.7%  outside
   12.68 ms   21.6%  Update
    6.46 ms   11.0%  PostUpdate
    5.16 ms    8.8%  RunFixedMainLoop
    1.22 ms    2.1%  StateTransition
    0.73 ms    1.2%  Last
    0.33 ms    0.6%  First
    0.26 ms    0.4%  SpawnScene
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
   3.9%  build tooling
   1.4%  audio
```

No profiler threads were sampled, so nothing but `perf` itself was
observing the game and the frame times in this bundle are the honest ones.
A `build tooling` share is the launcher's own `cargo` resolving the build;
it competes for cores but is not attributed to the game.

## Where the native time went

```text
  61.1%  game binary + its Rust/C deps
  37.9%  kernel
   0.6%  audio
   0.4%  GPU driver / graphics stack
```

From `perf-report-by-dso.txt`. If the top bucket is not the game binary,
ranking game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     3.71%  ambition_game_b  [unknown]                    [.] 0000000000000000                                                                                                                          
     2.40%  ambition_game_b  ambition_game_bin            [.] core::slice::sort::unstable::ipnsort::<alloc::string::String, <[alloc::string::String]>::sort_unstable_by_key<usize, <alloc::string::Strin
     2.04%  IO Task Pool (0  ambition_game_bin            [.] <symphonia_codec_vorbis::residue::Residue>::read_residue                                                                                  
     1.33%  IO Task Pool (0  [kernel.kallsyms]            [k] __unmap_and_move                                                                                                                          
     1.33%  Compute Task Po  [kernel.kallsyms]            [k] count_shadow_nodes                                                                                                                        
     1.23%  cargo            cargo                        [.] <clap_builder::builder::command::Command>::args::<clap_builder::builder::arg::Arg, [clap_builder::builder::arg::Arg; 1]>                  
     1.19%  ambition_game_b  ambition_game_bin            [.] <naga::back::spv::BlockContext>::write_block                                                                                              
     1.17%  ambition_game_b  [kernel.kallsyms]            [k] psi_group_change                                                                                                                          
     1.16%  ambition_game_b  ambition_game_bin            [.] <sharded_slab::pool::Pool<tracing_subscriber::registry::sharded::DataInner>>::get                                                         
     1.16%  Compute Task Po  [kernel.kallsyms]            [k] update_load_avg                                                                                                                           
     1.14%  ambition_game_b  ambition_game_bin            [.] <bevy_ecs::system::function_system::FunctionSystem<fn(core::option::Option<bevy_ecs::change_detection::params::Res<ambition_input::menu::M
     1.14%  ambition_game_b  ambition_game_bin            [.] <bevy_ecs::schedule::executor::single_threaded::SingleThreadedExecutor as bevy_ecs::schedule::executor::SystemExecutor>::run              
     1.07%  IO Task Pool (0  ambition_game_bin            [.] <fdeflate::decompress::Decompressor>::read                                                                                                
     1.02%  ambition_game_b  ambition_game_bin            [.] _mi_page_malloc_zero                                                                                                                      
     1.00%  ambition_game_b  libc.so.6                    [.] __memmove_avx_unaligned_erms                                                                                                              
     1.00%  Compute Task Po  [kernel.kallsyms]            [k] native_write_msr                                                                                                                          
     0.97%  ambition_game_b  ambition_game_bin            [.] <bevy_ecs::system::function_system::FunctionSystem<fn(bevy_ecs::change_detection::params::Res<leafwing_input_manager::user_input::updating
     0.97%  ambition_game_b  ambition_game_bin            [.] <bevy_ecs::query::state::QueryState<(core::option::Option<&ambition_combat::moveset::MovePlayback>, core::option::Option<&ambition_combat:
     0.93%  ambition_game_b  ambition_game_bin            [.] ___tracy_emit_zone_end                                                                                                                    
     0.89%  blocking-39      [kernel.kallsyms]            [k] copy_user_enhanced_fast_string                                                                                                            
     0.88%  ambition_game_b  [kernel.kallsyms]            [k] __update_load_avg_cfs_rq                                                                                                                  
     0.88%  ambition_game_b  ambition_game_bin            [.] _mi_theap_realloc_zero                                                                                                                    
     0.87%  Compute Task Po  ambition_game_bin            [.] <async_task::header::Header<()>>::register                                                                                                
     0.83%  ambition_game_b  ambition_game_bin            [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::lay
     0.80%  IO Task Pool (1  ambition_game_bin            [.] <symphonia_core::dsp::mdct::no_simd::Imdct>::imdct                                                                                        
     0.79%  IO Task Pool (1  libc.so.6                    [.] __memset_avx2_unaligned_erms                                                                                                              
     0.76%  ambition_game_b  ambition_game_bin            [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::lay
     0.73%  Compute Task Po  [kernel.kallsyms]            [k] __update_blocked_fair                                                                                                                     
     0.69%  Compute Task Po  [kernel.kallsyms]            [k] entry_SYSCALL_64                                                                                                                          
     0.68%  ambition_game_b  ambition_game_bin            [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::lay
     0.67%  IO Task Pool (1  libc.so.6                    [.] __memmove_avx_unaligned_erms                                                                                                              
     0.66%  ambition_game_b  ambition_game_bin            [.] <read_fonts::tables::loca::Loca>::get_glyf                                                                                                
     0.61%  ambition_game_b  ambition_game_bin            [.] _RINvNvNtCs8iRPrLRaIvb_5taffy7compute12round_layout18round_layout_innerINtNtNtB6_4tree10taffy_tree9TaffyViewNtNtCs8kGi66BLwdU_7bevy_ui11me
     0.58%  ambition_game_b  ambition_game_bin            [.] core::slice::sort::unstable::quicksort::quicksort::<alloc::string::String, <[alloc::string::String]>::sort_unstable_by_key<usize, <alloc::
     0.58%  ambition_game_b  ambition_game_bin            [.] mi_theap_umalloc                                                                                                                          
```

## Assets and render resources

- Decoded images: 87 → 184 (133.4 MP, 533.6 MB of decode work).
- Images resident at end: 162.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **127 images (90.4 MP)** at 5.3s. Each is extracted into the render world once, so this is what a frame spike is made of.

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
- `perf-record`: 139
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 231936 bytes

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

