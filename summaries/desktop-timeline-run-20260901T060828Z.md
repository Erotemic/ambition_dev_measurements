# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `88f4ca40e208` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/smash_match_profile` |
| package / bin | `ambition_app_tools` / `smash_match_profile` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.95.0 (59807616e 2026-04-14)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling smash-match --headless -- --fighters 2 --ticks 1800 ` |
| host | `aivm-2404` |
| kernel | `Linux aivm-2404 6.8.0-110-generic #110-Ubuntu SMP PREEMPT_DYNAMIC Thu Mar 19 15:09:20 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | yes, 1800 ticks |
| scenario | `smash-match-2p` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=12
- MemTotal:       65841164 kB

## Renderer

**NOT APPLICABLE — headless run.** The game ran its supported headless
path (`--headless`), which selects `backends: None`: no window, no wgpu
adapter, and therefore no render app and no GPU work at all. The
presentation composition itself may still exist — where camera and view
rows appear below, they are real and they are what a windowed run would
have drawn — but nothing here measures drawing.

Every GPU and render-pass measurement below is marked not-applicable
rather than missing. This run is not evidence that rendering is cheap.

## Session

Observed span of the game's own log: **9.6s**.

## Frame time

7 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.5     108     4.5     3.5     5.5    54.1     60.5
      2.5     288     3.5     3.3     4.7     6.5     14.6
      3.5     288     3.5     3.4     4.2     8.0     13.8
      4.5     206     4.8     4.5     7.9    11.0     12.2
      5.5     280     3.6     3.4     5.1     5.9      9.8
      7.5     308     3.3     3.2     4.2     5.0      6.7
      6.5     301     3.3     3.3     4.0     4.5      5.5
```

Full series: `frame_times.csv`.

2 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
    2.287s      60.5 ms
    2.149s      53.6 ms
```

Full list: `frame_spikes.csv`.

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      0.5        4       3      1       0      1
      1.5        4       3      1       0      1
      2.5        4       3      1       0      1
      3.5        4       3      1       0      1
      4.5        4       3      1       0      1
      5.5        4       3      1       0      1
      6.5        4       3      1       0      1
      7.5        4       3      1       0      1
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
      1.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      2.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      3.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      4.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      5.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      6.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
      7.5     0       0  res<=1024 depth=1 captures<=2 updates/frame<=2
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **0** (largest dimension 0px) at t=0.5s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       4096        1446        0        0
       end       4096        1645        2        0
      peak       4096        1645        2        0
```

Peak sprites: **98** (5 visible), text2d 10, per-view projections 6 at t=3.5s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3405** in 36 schedules.

## Render passes

NOT APPLICABLE — a headless run has no render app, so there are no
passes to time. This is not a claim that rendering is cheap.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
--no-tracy was passed; no per-system or per-render-pass timings were collected
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 1780 frames, summing to 3.94ms:

```text
    1.20 ms   30.5%  PreUpdate
    0.94 ms   23.7%  Update
    0.80 ms   20.2%  PostUpdate
    0.48 ms   12.2%  RunFixedMainLoop
    0.20 ms    5.2%  StateTransition
    0.13 ms    3.3%  SpawnScene
    0.08 ms    2.0%  First
    0.08 ms    1.9%  Last
    0.04 ms    1.0%  outside
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
   4.4%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  4.4%   (cargo and shell; NOT a compile)
the game itself           : 95.6%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  70.0%  game binary + its Rust/C deps
  30.0%  kernel
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     2.00%  smash_match_pro  smash_match_profile   [.] <bevy_ecs::schedule::executor::single_threaded::SingleThreadedExecutor as bevy_ecs::schedule::executor::SystemExecutor>::run                     
     1.41%  smash_match_pro  smash_match_profile   [.] _mi_page_malloc_zero                                                                                                                             
     1.02%  smash_match_pro  [kernel.kallsyms]     [k] copy_page                                                                                                                                        
     1.01%  smash_match_pro  [kernel.kallsyms]     [k] clear_page_erms                                                                                                                                  
     0.98%  smash_match_pro  smash_match_profile   [.] alloc::collections::btree::map::BTreeMap<K,V,A>::insert                                                                                          
     0.90%  Compute Task Po  smash_match_profile   [.] <async_executor::AsyncCallOnDrop<Fut,Cleanup> as core::future::future::Future>::poll                                                             
     0.79%  Compute Task Po  smash_match_profile   [.] ron::parse::Parser::next_chars_while_from_len                                                                                                    
     0.79%  Compute Task Po  [kernel.kallsyms]     [k] __pv_queued_spin_lock_slowpath                                                                                                                   
     0.71%  smash_match_pro  smash_match_profile   [.] bevy_ecs::query::state::QueryState<D,F>::new_archetype                                                                                           
     0.68%  IO Task Pool (2  [kernel.kallsyms]     [k] isolate_freepages_block                                                                                                                          
     0.67%  IO Task Pool (2  [kernel.kallsyms]     [k] clear_page_erms                                                                                                                                  
     0.60%  smash_match_pro  smash_match_profile   [.] mi_free                                                                                                                                          
     0.59%  smash_match_pro  smash_match_profile   [.] leafwing_input_manager::input_map::InputMap<A>::process_actions                                                                                  
     0.58%  IO Task Pool (2  [kernel.kallsyms]     [k] copy_page                                                                                                                                        
     0.57%  Compute Task Po  [kernel.kallsyms]     [k] copy_page                                                                                                                                        
     0.56%  smash_match_pro  smash_match_profile   [.] bevy_ecs::query::state::QueryState<D,F>::new_archetype                                                                                           
     0.54%  smash_match_pro  smash_match_profile   [.] core::ops::function::impls::<impl core::ops::function::FnMut<A> for &mut F>::call_mut                                                            
     0.51%  smash_match_pro  [kernel.kallsyms]     [k] folio_migrate_flags                                                                                                                              
     0.40%  Compute Task Po  smash_match_profile   [.] ron::parse::Parser::skip_ws                                                                                                                      
     0.40%  IO Task Pool (2  smash_match_profile   [.] symphonia_codec_vorbis::residue::Residue::read_residue                                                                                           
     0.40%  IO Task Pool (0  [kernel.kallsyms]     [k] copy_page                                                                                                                                        
     0.39%  blocking-5       [kernel.kallsyms]     [k] copy_page                                                                                                                                        
     0.39%  smash_match_pro  smash_match_profile   [.] mi_theap_malloc_aligned                                                                                                                          
     0.39%  IO Task Pool (0  [kernel.kallsyms]     [k] __pv_queued_spin_lock_slowpath                                                                                                                   
     0.38%  smash_match_pro  smash_match_profile   [.] ambition_render::hud::declared::update_declared_hud_panels                                                                                       
     0.38%  smash_match_pro  smash_match_profile   [.] bevy_ecs::query::state::QueryState<D,F>::new_archetype                                                                                           
     0.37%  Compute Task Po  libc.so.6             [.] 0x00000000001983a5                                                                                                                               
     0.37%  smash_match_pro  smash_match_profile   [.] <alloc::vec::Vec<T> as alloc::vec::spec_from_iter_nested::SpecFromIterNested<T,I>>::from_iter                                                    
     0.36%  smash_match_pro  smash_match_profile   [.] taffy::compute::flexbox::compute_flexbox_layout                                                                                                  
     0.35%  IO Task Pool (2  [kernel.kallsyms]     [k] xas_descend                                                                                                                                      
     0.32%  IO Task Pool (0  smash_match_profile   [.] symphonia_codec_vorbis::residue::Residue::read_residue                                                                                           
     0.31%  Compute Task Po  [kernel.kallsyms]     [k] futex_wake                                                                                                                                       
     0.31%  smash_match_pro  smash_match_profile   [.] ron::parse::Parser::next_chars_while_from_len                                                                                                    
     0.30%  smash_match_pro  smash_match_profile   [.] bevy_ecs::query::par_iter::QueryParIter$LT$D$C$F$GT$::for_each_init::_$u7b$$u7b$closure$u7d$$u7d$::hd8958c1c0c6f6319                             
     0.30%  smash_match_pro  smash_match_profile   [.] ron::parse::Parser::skip_ws                                                                                                                      
```

## Assets and render resources

- Decoded images: 18 → 58 (21.8 MP, 87.2 MB of decode work).
- Images resident at end: 58.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **52 images (20.2 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

✔ 3 decode(s) landed before the first `room-loaded` (9.4 MP) — boot. Not a gameplay hitch.

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
- `perf.data`: 83672 bytes

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
| `render_diagnostics.csv` | Bevy per-pass CPU/GPU times and pipeline statistics | no |
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

