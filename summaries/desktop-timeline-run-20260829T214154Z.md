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

Observed span of the game's own log: **32.6s**.

## Frame time

21 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      5.7       2   631.8   834.2   834.2   834.2    834.2
     22.1      10   113.3    55.1   559.0   559.0    559.0
      6.7       6   145.3    74.6   456.6   456.6    456.6
     15.9      20    50.8    50.1    62.6   115.6    115.6
     23.1      16    60.7    58.1    77.7    81.1     81.1
     14.9      25    41.4    39.6    62.6    72.8     72.8
     19.0      23    43.5    42.5    54.3    70.6     70.6
     25.2      19    55.0    56.3    63.2    68.8     68.8
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   10.379s     831.7 ms
   10.838s     459.1 ms
    9.547s     429.6 ms
   11.026s     187.9 ms
   11.101s      74.5 ms
   12.109s      68.2 ms
   11.253s      67.6 ms
   13.255s      66.1 ms
   13.758s      63.4 ms
   11.354s      63.1 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      4.0        4       3      1       0      1
      5.7        4       3      1       0      1
      6.7        4       3      1       0      1
      7.8        4       3      1       0      1
      8.8        4       3      1       0      1
      9.8        4       3      1       0      1
     10.9        4       3      1       0      1
     11.9        4       3      1       0      1
     12.9        4       3      1       0      1
     13.9        4       3      1       0      1
     14.9        4       3      1       0      1
     15.9        4       3      1       0      1
     17.0        4       3      1       0      1
     18.0        4       3      1       0      1
     19.0        4       3      1       0      1
     20.0        4       3      1       0      1
     21.0        4       3      1       0      1
     22.1        4       3      1       0      1
     23.1        4       3      1       0      1
     24.1        4       3      1       0      1
     25.2        4       3      1       0      1
     26.2        4       3      1       0      1
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
      5.7     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      6.7     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      7.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      8.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      9.8     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     10.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     11.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     12.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     13.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     14.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     15.9     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     17.0     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     18.0     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     19.0     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     20.0     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     21.0     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     22.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     23.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     24.1     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     25.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
     26.2     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=4.0s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048         109        0        0
       end       2048         405        3        0
      peak       2048         405        3        0
```

Peak sprites: **81** (76 visible), text2d 15, per-view projections 6 at t=25.2s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3300** in 37 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
         4.140          8.421       20  render/ui/elapsed_gpu
         2.558          2.984       20  render/msaa_writeback/elapsed_gpu
         1.833          8.626       20  render/main_transparent_pass_2d/elapsed_gpu
         1.811          2.130       20  render/upscaling/elapsed_gpu
         0.020          0.051       20  render/ui/elapsed_cpu
         0.017          0.100       20  render/main_transparent_pass_2d/elapsed_cpu
         0.002          0.005       20  render/main_opaque_pass_2d/elapsed_cpu
         0.001          0.005       20  render/upscaling/elapsed_cpu
         0.001          0.002       20  render/main_opaque_pass_2d/elapsed_gpu
         0.001          0.003       20  render/msaa_writeback/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
   9463141.800   19088652.000       20  render/ui/fragment_shader_invocations
   6796571.600   29771152.000       20  render/main_transparent_pass_2d/fragment_shader_invocations
   5760000.000    5760000.000       20  render/msaa_writeback/fragment_shader_invocations
   5760000.000    5760000.000       20  render/upscaling/fragment_shader_invocations
      1049.800       2344.000       20  render/ui/vertex_shader_invocations
       524.900       1172.000       20  render/ui/clipper_invocations
       495.500       1144.000       20  render/ui/clipper_primitives_out
       218.000       1612.000       20  render/main_transparent_pass_2d/vertex_shader_invocations
       109.000        806.000       20  render/main_transparent_pass_2d/clipper_invocations
       108.500        806.000       20  render/main_transparent_pass_2d/clipper_primitives_out
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

Mean milliseconds per frame over 407 frames, summing to 54.76ms:

```text
   18.05 ms   33.0%  outside
   11.83 ms   21.6%  Update
   10.79 ms   19.7%  PreUpdate
    6.91 ms   12.6%  PostUpdate
    5.03 ms    9.2%  RunFixedMainLoop
    0.84 ms    1.5%  Last
    0.63 ms    1.2%  StateTransition
    0.40 ms    0.7%  First
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
  95.6%  the game itself
   3.0%  build tooling
   1.4%  audio
```

No profiler threads were sampled, so nothing but `perf` itself was
observing the game and the frame times in this bundle are the honest ones.
A `build tooling` share is the launcher's own `cargo` resolving the build;
it competes for cores but is not attributed to the game.

## Where the native time went

```text
  68.3%  game binary + its Rust/C deps
  30.6%  kernel
   0.6%  audio
   0.5%  GPU driver / graphics stack
```

From `perf-report-by-dso.txt`. If the top bucket is not the game binary,
ranking game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     2.50%  IO Task Pool (1  ambition_game_bin              [.] <fdeflate::decompress::Decompressor>::read                                                                                              
     1.85%  IO Task Pool (0  ambition_game_bin              [.] <fdeflate::decompress::Decompressor>::read                                                                                              
     1.84%  ambition_game_b  [kernel.kallsyms]              [k] syscall_return_via_sysret                                                                                                               
     1.78%  IO Task Pool (1  ambition_game_bin              [.] png::filter::paeth::unfilter                                                                                                            
     1.67%  ambition_game_b  ambition_game_bin              [.] <&mut bevy_falling_sand::render::pipeline::textures::update_world_color_texture as core::ops::function::FnMut<(bevy_ecs::change_detectio
     1.58%  Compute Task Po  ambition_game_bin              [.] <tracing_subscriber::registry::sharded::Registry as tracing_core::subscriber::Subscriber>::enter                                        
     1.56%  ambition_game_b  ambition_game_bin              [.] <async_task::task::Task<core::result::Result<core::result::Result<bevy_app::sub_app::SubApp, async_channel::RecvError>, alloc::boxed::Bo
     1.55%  ambition_game_b  ambition_game_bin              [.] mi_free                                                                                                                                 
     1.51%  ambition_game_b  ambition_game_bin              [.] core::ptr::drop_glue::<wgpu_core::track::metadata::ResourceMetadata<alloc::sync::Arc<wgpu_core::resource::Texture>>>                    
     1.49%  ambition_game_b  ambition_game_bin              [.] mi_theap_malloc_aligned                                                                                                                 
     1.40%  ambition_game_b  [unknown]                      [.] 0000000000000000                                                                                                                        
     1.32%  Async Compute T  ambition_game_bin              [.] <regex_automata::util::pool::inner::Pool<regex_automata::meta::regex::Cache, alloc::boxed::Box<dyn core::ops::function::Fn<(), Output = 
     1.19%  Compute Task Po  [kernel.kallsyms]              [k] apic_ack_irq                                                                                                                            
     1.19%  ambition_game_b  [i915]                         [k] eb_submit                                                                                                                               
     1.17%  ambition_game_b  ambition_game_bin              [.] <fixedbitset::FixedBitSet>::is_disjoint                                                                                                 
     1.16%  Compute Task Po  ambition_game_bin              [.] <bevy_ecs::schedule::executor::multi_threaded::Context>::tick_executor                                                                  
     1.11%  ambition_game_b  libc.so.6                      [.] __memmove_avx_unaligned_erms                                                                                                            
     1.07%  Compute Task Po  [kernel.kallsyms]              [k] __handle_mm_fault                                                                                                                       
     1.05%  ambition_game_b  ambition_game_bin              [.] core::slice::sort::unstable::ipnsort::<alloc::string::String, <[alloc::string::String]>::sort_unstable_by_key<usize, <alloc::string::Str
     1.05%  ambition_game_b  ambition_game_bin              [.] _mi_theap_realloc_zero                                                                                                                  
     1.01%  ambition_game_b  ambition_game_bin              [.] core::fmt::write                                                                                                                        
     1.00%  ambition_game_b  ambition_game_bin              [.] <alloc::raw_vec::RawVec<(alloc::string::String, usize)>>::grow_one                                                                      
     0.93%  Compute Task Po  [unknown]                      [.] 0000000000000000                                                                                                                        
     0.91%  Compute Task Po  libc.so.6                      [.] __memset_avx2_unaligned_erms                                                                                                            
     0.90%  cpal_alsa_out    ambition_game_bin              [.] <kira::sound::static_sound::sound::StaticSound as kira::sound::Sound>::process                                                          
     0.89%  ambition_game_b  ambition_game_bin              [.] <futures_lite::future::CatchUnwind<core::panic::unwind_safe::AssertUnwindSafe<bevy_render::pipelined_rendering::renderer_extract::{closu
     0.86%  ambition_game_b  ambition_game_bin              [.] _mi_page_malloc_zero                                                                                                                    
     0.84%  IO Task Pool (0  [kernel.kallsyms]              [k] clear_page_erms                                                                                                                         
     0.80%  ambition_game_b  ambition_game_bin              [.] <tracing_tracy::TracyLayer as tracing_subscriber::layer::Layer<tracing_subscriber::layer::layered::Layered<tracing_subscriber::filter::l
     0.72%  Compute Task Po  ambition_game_bin              [.] <event_listener::InnerListener<(), alloc::sync::Arc<event_listener::Inner<()>>>>::poll_internal                                         
     0.72%  ambition_game_b  ambition_game_bin              [.] <tracing_subscriber::fmt::format::DefaultVisitor as tracing_core::field::Visit>::record_debug                                           
     0.66%  ambition_game_b  ambition_game_bin              [.] __rustc::__rust_no_alloc_shim_is_unstable_v2                                                                                            
     0.65%  Compute Task Po  ambition_game_bin              [.] bevy_ui_render::extract_uinode_background_colors                                                                                        
     0.64%  IO Task Pool (0  ambition_game_bin              [.] <symphonia_core::dsp::fft::Fft>::transform                                                                                              
     0.61%  ambition_game_b  ambition_game_bin              [.] <bevy_ecs::schedule::executor::single_threaded::SingleThreadedExecutor as bevy_ecs::schedule::executor::SystemExecutor>::run            
```

## Assets and render resources

- Decoded images: 83 → 177 (130.1 MP, 520.5 MB of decode work).
- Images resident at end: 155.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **127 images (91.5 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 29 decode(s) landed before the first `room-loaded` (91.1 MP) — boot. Not a gameplay hitch.

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
- `perf.data`: 216656 bytes

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

