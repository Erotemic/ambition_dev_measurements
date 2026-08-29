# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `70898d77b75e` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `visible,profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/smash_demo` |
| package / bin | `ambition_demo_smash_app` / `smash_demo` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.97.1 (8bab26f4f 2026-07-14)` |
| capture mode | `timeline-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile smash ` |
| host | `toothbrush` |
| kernel | `Linux toothbrush 6.8.0-138-generic #138-Ubuntu SMP PREEMPT_DYNAMIC Fri Jul 31 22:41:49 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `n/a` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=16
- MemTotal:       131727480 kB

## Renderer

UNAVAILABLE — no `AdapterInfo` line was found in the captured logs, so
this bundle cannot say which adapter drew. See `host-environment.txt`.

## Session

Observed span of the game's own log: **0.5s**.

## Frame time

UNAVAILABLE — no frame-time rows were captured.

No frames crossed the 33.4ms spike threshold.

## Cameras and views

UNAVAILABLE — no camera census rows reached this bundle.

## Portal and offscreen workload

No portal capture rigs were reported in this run.

## Scene and ECS workload

UNAVAILABLE — no ECS census rows reached this bundle.

## Render passes

UNAVAILABLE — no render-pass rows reached this bundle. `RenderDiagnosticsPlugin`
is installed by the presentation census plugin; a bundle with no rows either
ran with `--no-census` or never rendered a frame.

## Bevy systems and zones (Tracy)

UNAVAILABLE — no Tracy capture:

```text
the launched binary has no tracy client
binary: /home/joncrall/code/ambition/target/profiling/smash_demo
features: visible,profile
rebuild with --features profile (the default unless --no-tracy was passed)
```

Without it there are no per-Bevy-system or per-render-pass zone timings;
`perf` reports native symbols, which cannot be mapped back to a system.

## Which phase of the frame owned the time

UNAVAILABLE — no `[census] phases` rows in this bundle. The phase marks
are registered only when `AMBITION_PROFILE_CENSUS` is set at App build
time, so a run that enabled the census later has none.

## Observer effect (what the profiler itself cost)

```text
 100.0%  build tooling
```

No profiler threads were sampled, so nothing but `perf` itself was
observing the game and the frame times in this bundle are the honest ones.
A `build tooling` share is the launcher's own `cargo` resolving the build;
it competes for cores but is not attributed to the game.

## Where the native time went

```text
  91.6%  game binary + its Rust/C deps
   8.4%  kernel
```

From `perf-report-by-dso.txt`. If the top bucket is not the game binary,
ranking game symbols is ranking the wrong machine layer.

This split is by SHARED OBJECT, not by thread: statically linked
profiler, allocator, and runtime code all report as the game binary.
Read it together with the observer-effect section above.

Top native symbols:

```text
    23.23%  cargo    cargo                 [.] _RNvXsj_NtCs2AWtUsOyxgP_3std4pathNtB5_10ComponentsNtNtNtNtCs4NRVxsYgnAr_4core4iter6traits12double_ended19DoubleEndedIterator9next_back  -      -         
    19.68%  cargo    cargo                 [.] _RNvMs9_CsSxDvyMnmZV_4globNtB5_7Pattern3new                                                                                             -      -         
    14.76%  cargo    libc.so.6             [.] malloc                                                                                                                                  -      -         
    10.64%  cargo    cargo                 [.] OPENSSL_LH_insert                                                                                                                       -      -         
     8.79%  cargo    cargo                 [.] msblob2key_does_selection                                                                                                               -      -         
     8.41%  cargo    [kernel.kallsyms]     [k] __alloc_pages                                                                                                                           -      -         
     5.64%  bash     libc.so.6             [.] _nl_find_locale                                                                                                                         -      -         
     5.52%  cargo    libc.so.6             [.] _int_free                                                                                                                               -      -         
     3.32%  cargo    ld-linux-x86-64.so.2  [.] _dl_relocate_object                                                                                                                     -      -         
```

## Assets and render resources

UNAVAILABLE — no asset census rows in this bundle.

## Collection status

- `warm-build`: 101
- `perf-record`: 101
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 36708 bytes

## Files in this bundle

| file | contents | present |
| --- | --- | --- |
| `summary.md` | this file | yes |
| `metadata.txt / metadata.json` | build, commit, host, and capture settings | yes |
| `host-environment.txt` | CPU, GPU, DRM nodes, Vulkan ICDs, graphics env overrides | yes |
| `timeline.md` | per-window perf symbols labelled with the game's own log markers | yes |
| `frame_times.csv` | per-census-window frame-time percentiles | no |
| `frame_spikes.csv` | every frame over 33.4ms, with its wall-clock second | yes |
| `frame_windows.csv` | the always-on 5s frame census | yes |
| `camera_views.csv` | one row per camera per sample: role, target, size, layers | no |
| `view_totals.csv` | camera/active/world-rendering/offscreen counts per sample | no |
| `runtime_census.csv` | entity, archetype, component, body, and player counts | no |
| `draw_census.csv` | sprite/text/projection population and visibility | no |
| `render_target_census.csv` | offscreen image targets and their bytes | no |
| `render_diagnostics.csv` | Bevy per-pass CPU/GPU times and pipeline statistics | no |
| `portal_activity.csv` | portal capture rigs and the budget bounding them | no |
| `asset_activity.csv` | cumulative decode work and resident images | no |
| `image_decodes.csv` | every notable texture decode, with its path | yes |
| `schedule_census.csv` | registered system counts per sample | no |
| `schedule_phases.csv` | per-frame milliseconds in each main-schedule phase | no |
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | no |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | no |
| `tracy.trace` | the raw Tracy trace, for the GUI | no |
| `perf_windows/` | one flat perf report per time slice | yes |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

