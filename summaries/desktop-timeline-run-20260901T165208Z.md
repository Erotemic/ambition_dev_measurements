# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `95e6be68268c` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.95.0 (59807616e 2026-04-14)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling -- --start-room hall_of_characters --headless --headless-ticks 6000 ` |
| host | `aivm-2404` |
| kernel | `Linux aivm-2404 6.8.0-110-generic #110-Ubuntu SMP PREEMPT_DYNAMIC Thu Mar 19 15:09:20 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | yes, 6000 ticks |
| scenario | `headless:--start-room+hall_of_characters` |

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

Observed span of the game's own log: **11.6s**.

## Frame time

9 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.3     531     1.6     1.6     2.2     2.8     15.2
      2.3     632     1.6     1.5     2.2     2.7      3.4
      8.3     649     1.5     1.4     2.1     2.4      3.2
      5.3     645     1.6     1.5     2.1     2.4      3.1
      4.3     642     1.6     1.5     2.1     2.5      3.0
      7.3     644     1.6     1.5     2.1     2.5      2.8
      3.3     655     1.5     1.4     2.1     2.3      2.8
      6.3     650     1.5     1.4     2.1     2.3      2.8
```

Full series: `frame_times.csv`.

No frames crossed the 33.4ms spike threshold.

## Cameras and views

NOT APPLICABLE — a headless run composes no cameras.

## Portal and offscreen workload

NOT APPLICABLE — headless runs compose no portal capture rigs.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start        512         429        0        0
       end       1024         642      130        1
      peak       1024         642      130        1
```

Entity count rose by 512 and then held flat at 1024 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak registered systems across visible schedules: **864** in 20 schedules.

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

Mean milliseconds per frame over 5705 frames, summing to 1.58ms:

```text
    1.16 ms   73.6%  Update
    0.13 ms    8.4%  StateTransition
    0.06 ms    4.0%  PostUpdate
    0.04 ms    2.6%  SpawnScene
    0.04 ms    2.6%  Last
    0.04 ms    2.5%  PreUpdate
    0.04 ms    2.2%  outside
    0.03 ms    2.0%  RunFixedMainLoop
    0.03 ms    2.0%  First
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  94.8%  the game itself
   5.2%  build launcher (cargo, shell)
```

```text
profiler (Tracy) overhead :  0.0%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  5.2%   (cargo and shell; NOT a compile)
the game itself           : 94.8%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
  88.8%  game binary + its Rust/C deps
  11.2%  kernel
```

From `perf-report-by-dso.txt`, SELF time (`--no-children`), so the rows
partition the capture. If the top bucket is not the game binary, ranking
game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
     3.47%  ambition_game_b  ambition_game_bin     [.] _mi_page_malloc_zero                                                                                                                             
     2.56%  ambition_game_b  ambition_game_bin     [.] <bevy_ecs::schedule::executor::single_threaded::SingleThreadedExecutor as bevy_ecs::schedule::executor::SystemExecutor>::run                     
     1.84%  ambition_game_b  libc.so.6             [.] 0x000000000019839e                                                                                                                               
     1.49%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_core::world::World::first_body_sweep                                                                                       
     1.34%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_actor_monolith::features::ecs::actors::update::integrate_sim_bodies                                                        
     1.32%  ambition_game_b  ambition_game_bin     [.] ambition_combat::targeting::select_actor_targets                                                                                                 
     1.14%  ambition_game_b  ambition_game_bin     [.] <bevy_math::bounding::bounded2d::Aabb2d as ambition_geometry::geometry::AabbExt>::strict_intersects                                              
     1.12%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_actor_monolith::features::ecs::actors::update::tick_actor_brains                                                           
     1.06%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_core::movement::update_body_with_frame_clusters                                                                            
     1.02%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::query::state::QueryState<D,F>::new_archetype                                                                                           
     0.98%  ambition_game_b  libc.so.6             [.] 0x00000000001983a5                                                                                                                               
     0.92%  ambition_game_b  ambition_game_bin     [.] ron::parse::Parser::skip_ws                                                                                                                      
     0.79%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_core::movement::collision::resolve_axis_repair                                                                             
     0.76%  ambition_game_b  ambition_game_bin     [.] mi_free                                                                                                                                          
     0.75%  ambition_game_b  ambition_game_bin     [.] ambition_sim_view::anim_index::rebuild_actor_anim_index                                                                                          
     0.74%  ambition_game_b  ambition_game_bin     [.] ambition_geometry::geometry::slab_sweep                                                                                                          
     0.71%  Compute Task Po  [kernel.kallsyms]     [k] native_write_msr                                                                                                                                 
     0.65%  ambition_game_b  ambition_game_bin     [.] ambition_sim_view::pose_view::rebuild_body_pose_views                                                                                            
     0.64%  ambition_game_b  ambition_game_bin     [.] ambition_characters::actor::character_catalog::CharacterCatalog::bark_line                                                                       
     0.64%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_actor_monolith::features::ecs::save_sync::sync_ecs_actors_with_save                                                        
     0.59%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_actor_monolith::features::ecs::actors::update::apply_actor_contact_damage                                                  
     0.58%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_actor_monolith::features::ecs::actors::update::sync_actor_read_model                                                       
     0.54%  ambition_game_b  libc.so.6             [.] 0x00000000001989c5                                                                                                                               
     0.52%  ambition_game_b  ambition_game_bin     [.] ambition_sim_view::view_index::rebuild_nameplate_index                                                                                           
     0.52%  ambition_game_b  ambition_game_bin     [.] _ZN71_$LT$core..hash..sip..Hasher$LT$S$GT$$u20$as$u20$core..hash..Hasher$GT$5write17h99e9c218259a56d9E.llvm.5329884021581780808                  
     0.51%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_core::movement::integration::integrate_velocity_clusters                                                                   
     0.50%  ambition_game_b  ambition_game_bin     [.] _mi_theap_realloc_zero                                                                                                                           
     0.48%  ambition_game_b  ambition_game_bin     [.] ambition_combat::body_geometry::publish_body_footprint                                                                                           
     0.45%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_actor_monolith::features::ecs::actor_clusters::ActorClusterQueryDataItem::as_actor_mut                                     
     0.43%  ambition_game_b  ambition_game_bin     [.] ambition_platformer2d_actor_monolith::action_scheme::reconcile_action_schemes                                                                    
     0.43%  Compute Task Po  ambition_game_bin     [.] <futures_lite::future::Or<F1,F2> as core::future::future::Future>::poll                                                                          
     0.42%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::world::unsafe_world_cell::UnsafeWorldCell::get_resource_mut_by_id                                                                      
     0.42%  ambition_game_b  ambition_game_bin     [.] _mi_bin_size                                                                                                                                     
     0.41%  ambition_game_b  ambition_game_bin     [.] ambition_characters::prepared::PreparedCharacterRegistry::get                                                                                    
     0.41%  ambition_game_b  ambition_game_bin     [.] <bevy_math::bounding::bounded2d::Aabb2d as ambition_geometry::geometry::AabbExt>::sweep_hit                                                      
```

## Assets and render resources

- Busiest arrival window: **2 images (0.0 MP)** at 5.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

UNAVAILABLE — no asset census rows in this bundle.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 170060 bytes

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
| `camera_views.csv` | one row per camera per sample: role, target, size, layers | no |
| `view_totals.csv` | camera/active/world-rendering/offscreen counts per sample | no |
| `runtime_census.csv` | entity, archetype, component, body, and player counts | yes |
| `draw_census.csv` | sprite/text/projection population and visibility | no |
| `render_target_census.csv` | offscreen image targets and their bytes | no |
| `render_diagnostics.csv` | Bevy per-pass CPU/GPU times and pipeline statistics | no |
| `portal_activity.csv` | portal capture rigs and the budget bounding them | no |
| `asset_activity.csv` | cumulative decode work and resident images | no |
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

