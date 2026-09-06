# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `370bf5401f65` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.95.0 (59807616e 2026-04-14)` |
| capture mode | `perf-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile sandbox -- --headless --headless-ticks 900 ` |
| host | `aivm-2404` |
| kernel | `Linux aivm-2404 6.8.0-110-generic #110-Ubuntu SMP PREEMPT_DYNAMIC Thu Mar 19 15:09:20 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | yes, 900 ticks |
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

Observed span of the game's own log: **10.2s**.

## Frame time

3 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
      1.5     159     4.0     3.8     5.3     7.2     18.7
      2.5     252     4.0     3.9     4.7     5.3     10.0
      3.5     257     3.9     3.9     4.7     5.2      6.2
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

> **Timer caveat.** this CPU does not advertise an invariant TSC (constant_tsc + nonstop_tsc); TRACY_NO_INVARIANT_CHECK=1 was set so the game could run. Tracy zone durations here come from a timer the kernel does not vouch for: treat their RATIOS as sound and their absolute microseconds as approximate.

UNAVAILABLE — no Tracy capture:

```text
tracy-capture produced no trace (the game never connected)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

Mean milliseconds per frame over 669 frames, summing to 4.50ms:

```text
    3.73 ms   82.9%  Update
    0.18 ms    4.0%  StateTransition
    0.16 ms    3.5%  PostUpdate
    0.10 ms    2.3%  Last
    0.08 ms    1.9%  PreUpdate
    0.07 ms    1.5%  SpawnScene
    0.06 ms    1.4%  First
    0.06 ms    1.3%  outside
    0.06 ms    1.3%  RunFixedMainLoop
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

```text
  88.0%  the game itself
   9.4%  build launcher (cargo, shell)
   2.5%  profiler (Tracy)
```

```text
profiler (Tracy) overhead :  2.5%
codegen inside the capture:  0.0%   (rustc / LLVM / linker threads)
build launcher            :  9.4%   (cargo and shell; NOT a compile)
the game itself           : 88.0%
native attribution        : CLEAN
```

Neither the profiler nor a compile took a share worth correcting for, so
the native symbol ranking and the DSO split below stand on their own.

## Where the native time went

```text
 216.4%  game binary + its Rust/C deps
  22.8%  kernel
```

⚠⚠ **These rows sum to 239%, so they are not a breakdown.**

This report was written with `perf report`'s default INCLUSIVE accounting: a
sample is credited to every shared object on its call stack, so the game
binary and the kernel it called both own the same cycles.

Read each row as "cycles that passed through here", never as a share of
the capture, and never subtract one from another. Captures written after
2026-08-31 pass `--no-children` and do partition; this bundle predates it.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
    71.73%     0.00%  ambition_game_b  [unknown]             [.] 0xffffffffffffffff                                                                                                                     
    61.55%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::schedule::schedule::Schedule::run                                                                                            
    61.37%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::world::World::try_schedule_scope                                                                                             
    60.61%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::world::World::resource_scope                                                                                                 
    60.45%     1.59%  ambition_game_b  ambition_game_bin     [.] <bevy_ecs::schedule::executor::single_threaded::SingleThreadedExecutor as bevy_ecs::schedule::executor::SystemExecutor>::run           
    60.27%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::schedule::executor::__rust_begin_short_backtrace::run_without_applying_deferred                                              
    60.08%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::world::World::last_change_tick_scope                                                                                         
    59.91%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::system::system::System::run_without_applying_deferred                                                                        
    56.02%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_ecs::world::World::schedule_scope                                                                                                 
    12.94%     3.38%  ambition_game_b  ambition_game_bin     [.] <tracing_tracy::TracyLayer<C> as tracing_subscriber::layer::Layer<S>>::on_enter                                                        
    11.85%     0.00%  ambition_game_b  ambition_game_bin     [.] <tracing_subscriber::layer::layered::Layered<L,S> as tracing_core::subscriber::Subscriber>::try_close                                  
    11.48%     0.00%  ambition_game_b  ambition_game_bin     [.] core::ptr::drop_in_place<tracing::span::Span>                                                                                          
     9.83%     0.00%  Compute Task Po  [unknown]             [k] 0xffffffffffffffff                                                                                                                     
     9.43%     0.00%  ambition_game_b  ambition_game_bin     [.] <tracing_tracy::utils::StrCacheGuard as core::ops::drop::Drop>::drop                                                                   
     9.22%     0.00%  cargo            [unknown]             [k] 0xffffffffffffffff                                                                                                                     
     9.03%     0.55%  ambition_game_b  ambition_game_bin     [.] <tracing_tracy::TracyLayer<C> as tracing_subscriber::layer::Layer<S>>::on_close                                                        
     8.45%     0.00%  ambition_game_b  ambition_game_bin     [.] std::thread::local::LocalKey<T>::with                                                                                                  
     8.10%     0.00%  ambition_game_b  ambition_game_bin     [.] std::thread::local::LocalKey<T>::with                                                                                                  
     7.87%     0.00%  ambition_game_b  ambition_game_bin     [.] bevy_app::sub_app::SubApps::update                                                                                                     
     7.51%     0.20%  ambition_game_b  ambition_game_bin     [.] tracing_core::dispatcher::get_default                                                                                                  
     7.51%     0.00%  ambition_game_b  ambition_game_bin     [.] tracing::span::Span::new                                                                                                               
     7.43%     0.00%  Compute Task Po  ambition_game_bin     [.] std::thread::local::LocalKey<T>::with                                                                                                  
     7.31%     0.00%  ambition_game_b  ambition_game_bin     [.] <tracing_subscriber::layer::layered::Layered<L,S> as tracing_core::subscriber::Subscriber>::new_span                                   
     7.31%     0.00%  ambition_game_b  ambition_game_bin     [.] tracing::span::Span::make_with                                                                                                         
     6.96%     0.00%  ambition_game_b  ambition_game_bin     [.] _RNvNtCsgEmfK2I1SDS_4core3fmt5write                                                                                                    
     6.64%     0.58%  ambition_game_b  [kernel.kallsyms]     [k] asm_exc_page_fault                                                                                                                     
     6.61%     5.52%  ambition_game_b  ambition_game_bin     [.] core::slice::sort::unstable::quicksort::quicksort                                                                                      
     6.26%     0.00%  Compute Task Po  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_struct                                                       
     6.26%     0.00%  Compute Task Po  ambition_game_bin     [.] <ron::de::CommaSeparated as serde_core::de::SeqAccess>::next_element_seed                                                              
     6.25%     0.07%  Compute Task Po  ambition_game_bin     [.] <futures_lite::future::Or<F1,F2> as core::future::future::Future>::poll                                                                
     6.21%     0.26%  Compute Task Po  [kernel.kallsyms]     [k] asm_exc_page_fault                                                                                                                     
     6.07%     0.00%  Compute Task Po  ambition_game_bin     [.] <&mut ron::de::Deserializer as serde_core::de::Deserializer>::deserialize_seq                                                          
     6.07%     0.00%  Compute Task Po  ambition_game_bin     [.] <serde_core::de::impls::<impl serde_core::de::Deserialize for alloc::vec::Vec<T>>::deserialize::VecVisitor<T> as serde_core::de::Visito
     6.07%     0.00%  Compute Task Po  ambition_game_bin     [.] ambition_sprite_sheet::index_baked_table                                                                                               
     6.07%     0.00%  Compute Task Po  ambition_game_bin     [.] ron::options::Options::from_str_seed                                                                                                   
```

## Assets and render resources

UNAVAILABLE — no asset census rows in this bundle.

## Collection status

- `warm-build`: 0
- `perf-record`: 0
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 5189284 bytes

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

