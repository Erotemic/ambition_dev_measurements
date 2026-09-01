# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `c501c9087aee` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `<none>` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.95.0 (59807616e 2026-04-14)` |
| capture mode | `perf-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling sandbox -- --headless --headless-ticks 1800 ` |
| host | `aivm-2404` |
| kernel | `Linux aivm-2404 6.8.0-110-generic #110-Ubuntu SMP PREEMPT_DYNAMIC Thu Mar 19 15:09:20 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | yes, 1800 ticks |
| scenario | `sandbox (profiler default)` |

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

Observed span of the game's own log: **4.7s**.

## Frame time

1 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.3     822     0.9     0.8     1.5     2.0     12.1
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
     start        512         442        0        0
       end        512         515        2        1
      peak        512         515        2        1
```

Peak registered systems across visible schedules: **921** in 20 schedules.

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

Mean milliseconds per frame over 823 frames, summing to 1.22ms:

```text
    0.82 ms   67.4%  Update
    0.14 ms   11.3%  StateTransition
    0.06 ms    4.7%  PostUpdate
    0.04 ms    3.2%  Last
    0.04 ms    2.9%  PreUpdate
    0.04 ms    2.9%  SpawnScene
    0.03 ms    2.6%  RunFixedMainLoop
    0.03 ms    2.6%  outside
    0.03 ms    2.4%  First
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  79.9%  the game itself
  20.1%  build tooling
```

```text
profiler (Tracy) overhead :  0.0%
compile inside the capture: 20.1%   (the game itself: 79.9%)
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
 206.8%  game binary + its Rust/C deps
  22.0%  kernel
```

From `perf-report-by-dso.txt`. If the top bucket is not the game binary,
ranking game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
    56.13%     0.00%  ambition_game_b  [unknown]             [k] 0xffffffffffffffff                                                                                                                     
    38.18%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::world::World::try_schedule_scope                                                                                             
    36.75%     1.56%  ambition_game_b  ambition_game_bin     [.] <bevy_ecs::schedule::executor::single_threaded::SingleThreadedExecutor as bevy_ecs::schedule::executor::SystemExecutor>::run           
    36.75%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::schedule::executor::__rust_begin_short_backtrace::run_without_applying_deferred                                              
    35.70%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::world::World::resource_scope                                                                                                 
    34.90%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::system::system::System::run_without_applying_deferred                                                                        
    31.30%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::world::World::schedule_scope                                                                                                 
    26.12%     0.00%  ambition_game_b  ambition_game_bin     [.] ambition_app::headless::run_headless                                                                                                   
    23.34%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_app::sub_app::SubApps::update                                                                                                     
    14.96%     0.00%  cargo            [unknown]             [k] 0xffffffffffffffff                                                                                                                     
     7.22%     0.00%  ambition_game_b  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_struct                                                       
     6.82%     0.00%  ambition_game_b  ambition_game_bin     [.] <ron::de::CommaSeparated as serde_core::de::SeqAccess>::next_element_seed                                                              
     6.72%     0.00%  ambition_game_b  [kernel.kallsyms]     [k] __handle_mm_fault                                                                                                                      
     6.72%     0.00%  ambition_game_b  [kernel.kallsyms]     [k] asm_exc_page_fault                                                                                                                     
     6.72%     0.00%  ambition_game_b  [kernel.kallsyms]     [k] do_user_addr_fault                                                                                                                     
     6.72%     0.00%  ambition_game_b  [kernel.kallsyms]     [k] exc_page_fault                                                                                                                         
     6.72%     0.00%  ambition_game_b  [kernel.kallsyms]     [k] handle_mm_fault                                                                                                                        
     6.71%     0.00%  Compute Task Po  [unknown]             [.] 0xffffffffffffffff                                                                                                                     
     6.61%     0.00%  Compute Task Po  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_struct                                                       
     6.42%     0.00%  ambition_game_b  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_seq                                                          
     6.42%     0.00%  ambition_game_b  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_seq                                                          
     6.42%     0.00%  ambition_game_b  ambition_game_bin     [.] <ron::de::CommaSeparated as serde_core::de::SeqAccess>::next_element_seed                                                              
     6.42%     0.00%  ambition_game_b  ambition_game_bin     [.] <serde_core::de::impls::<impl serde_core::de::Deserialize for alloc::vec::Vec<T>>::deserialize::VecVisitor<T> as serde_core::de::Visito
     6.42%     0.00%  ambition_game_b  ambition_game_bin     [.] <serde_core::de::impls::<impl serde_core::de::Deserialize for alloc::vec::Vec<T>>::deserialize::VecVisitor<T> as serde_core::de::Visito
     6.42%     0.00%  ambition_game_b  ambition_game_bin     [.] ambition_sprite_sheet::index_baked_table                                                                                               
     6.42%     0.00%  ambition_game_b  ambition_game_bin     [.] ron::options::Options::from_str_seed                                                                                                   
     6.21%     0.00%  Compute Task Po  ambition_game_bin     [.] <ron::de::CommaSeparated as serde_core::de::SeqAccess>::next_element_seed                                                              
     6.03%     0.00%  ambition_game_b  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_struct                                                       
     5.63%     0.00%  ambition_game_b  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_seq                                                          
     5.63%     0.00%  ambition_game_b  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_struct                                                       
     5.63%     0.00%  ambition_game_b  ambition_game_bin     [.] <ron::de::CommaSeparated as serde_core::de::SeqAccess>::next_element_seed                                                              
     5.63%     0.00%  ambition_game_b  ambition_game_bin     [.] <serde_core::de::impls::<impl serde_core::de::Deserialize for alloc::vec::Vec<T>>::deserialize::VecVisitor<T> as serde_core::de::Visito
     5.53%     0.00%  ambition_game_b  [kernel.kallsyms]     [k] do_huge_pmd_anonymous_page                                                                                                             
     5.42%     0.00%  Compute Task Po  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_seq                                                          
     5.42%     0.00%  Compute Task Po  ambition_game_bin     [.] <serde_core::de::impls::<impl serde_core::de::Deserialize for alloc::vec::Vec<T>>::deserialize::VecVisitor<T> as serde_core::de::Visito
```

## Assets and render resources

UNAVAILABLE — no asset census rows in this bundle.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 2881940 bytes

## Files in this bundle

| file | contents | present |
| --- | --- | --- |
| `summary.md` | this file | yes |
| `metadata.txt / metadata.json` | build, commit, host, and capture settings | yes |
| `host-environment.txt` | CPU, GPU, DRM nodes, Vulkan ICDs, graphics env overrides | yes |
| `timeline.md` | per-window perf symbols labelled with the game's own log markers | no |
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
| `perf_windows/` | one flat perf report per time slice | no |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

