# Profiling bundle

## What this measured

| fact | value |
| --- | --- |
| git commit | `9f606567a60f` on `main` |
| working tree | DIRTY — the binary is not this commit alone |
| cargo profile | `profiling` (`target/profiling`) |
| cargo features | `profile` |
| executable | `/home/joncrall/code/ambition/target/profiling/ambition_game_bin` |
| package / bin | `ambition_app` / `ambition_game_bin` |
| rust target | `x86_64-unknown-linux-gnu` |
| rustc | `rustc 1.95.0 (59807616e 2026-04-14)` |
| capture mode | `perf-run` |
| run command | `/home/joncrall/code/ambition/run_game.sh profiling --features profile -- --start-room hall_of_characters ` |
| host | `aivm-2404` |
| kernel | `Linux aivm-2404 6.8.0-110-generic #110-Ubuntu SMP PREEMPT_DYNAMIC Thu Mar 19 15:09:20 UTC 2026 x86_64 x86_64 x86_64 GNU/Linux` |
| workload census | on at 1 Hz |
| headless | no |
| scenario | `n/a` |

Release-level optimization with symbols and line tables kept, so this
is representative of shipped runtime performance and still attributable.

- model name	: 11th Gen Intel(R) Core(TM) i9-11900K @ 3.50GHz
- logical_cpus=12
- MemTotal:       65841168 kB

## Renderer

```text
AdapterInfo { name: "llvmpipe (LLVM 20.1.2, 256 bits)", vendor: 65541, device: 0, device_type: Cpu, device_pci_bus_id: "", driver: "llvmpipe", driver_info: "Mesa 25.2.8-0ubuntu0.24.04.2 (LLVM 20.1.2)", backend: Vulkan, subgroup_min_size: 8, subgroup_max_size: 8, transient_saves_memory: false }
```

**SOFTWARE RENDERING — READ THIS BEFORE THE NUMBERS BELOW.**

This run had no GPU: every pixel was rasterized on the CPU. Expect the
bulk of samples in llvmpipe/lavapipe threads and in unsymbolized `[JIT]`
frames, which are the rasterizer's runtime-compiled shaders -- perf can
never attribute those to a pass, a material, or a draw call. Game-code
symbols in this profile describe only the few percent left over.

This is NOT a measurement of GPU rendering performance, and it must not
be reported as one. Check `host-environment.txt` for why no GPU adapter
was selected (missing ICD, `VK_*`/`WGPU_*` override, no DRM render node,
headless session).

## Session

Observed span of the game's own log: **200.6s**.

## Frame time

121 census windows at 1 Hz. Worst windows by max frame:

```text
        t  frames    mean     p50     p95     p99      max
     19.2       1  7334.8  7334.8  7334.8  7334.8   7334.8
     82.4       1  2279.7  2279.7  2279.7  2279.7   2279.7
     80.4       1  2149.0  2149.0  2149.0  2149.0   2149.0
    108.6       1  2109.8  2109.8  2109.8  2109.8   2109.8
     74.5       1  2095.1  2095.1  2095.1  2095.1   2095.1
     65.4       1  2044.0  2044.0  2044.0  2044.0   2044.0
     88.3       1  2033.9  2033.9  2033.9  2033.9   2033.9
    145.5       1  1987.2  1987.2  1987.2  1987.2   1987.2
```

Full series: `frame_times.csv`.

60 frames over the 33.4ms spike threshold. Worst, with the
wall-clock second to look up in the other CSVs:

```text
   26.972s    7322.3 ms
   82.424s    2119.2 ms
   73.134s    2022.5 ms
   86.088s    1923.4 ms
   71.114s    1916.0 ms
   64.128s    1874.2 ms
   78.439s    1866.8 ms
   80.305s    1863.0 ms
   67.504s    1843.6 ms
   74.966s    1823.3 ms
```

Full list: `frame_spikes.csv`.

## Boot population (what decoded before the first room)

⚠ SAMPLE, NOT POPULATION: `[image]` prints only decodes >= 1.0 MP. This run decoded 294 images / 91.9 MP in total; 26 were notable enough to print.

```text
ordered by: frame
before hall_of_characters: 6 image(s), 22.1 MP
  unknown                  2    16.5 MP
  fx-sheet                 2     2.2 MP
  boss-sheet               1     2.0 MP
  character-sheet          1     1.4 MP
```

## Cameras and views

```text
        t  cameras  active  world  offscr  views
      2.2        4       3      1       0      1
      9.9        4       3      1       0      1
     24.7       18      17      1      14      1
     32.2       18      17      1      14      1
     39.3       18      17      1      14      1
     47.5       18      17      1      14      1
     56.3       18      17      1      14      1
     67.2       18      17      1      14      1
     78.3       18      17      1      14      1
     90.3       18      17      1      14      1
    100.4       18      17      1      14      1
    110.6       18      17      1      14      1
    119.7       18      17      1      14      1
    129.4       18      17      1      14      1
    140.2       18      17      1      14      1
    150.2       18      17      1      14      1
    158.2       18      17      1      14      1
    165.5       18      17      1      14      1
    173.2       18      17      1      14      1
    181.5       18      17      1      14      1
    191.7       18      17      1      14      1
```

Peak world-rendering cameras: **1** at t=2.2s.

The world was drawn **once** per frame throughout: one active
world-rendering camera, no portal capture and no second view. Repeated
world rendering is not what this run's frame cost is.

Distinct cameras seen, by role:

```text
             hud  Front HUD Camera
      local_view  Main Camera
       offscreen  rigged impostor camera, rigged impostor unpremultiply camera
           other  Cube pause camera, Cube scrim display camera
```

Per-sample rows: `camera_views.csv`.

## Portal and offscreen workload

Peak active portal capture rigs: **0** of 0 at t=2.2s.

```text
        t  rigs  active  budget
      2.2     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     21.7     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     34.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     47.5     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     63.3     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
     82.4     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
    100.4     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
    116.8     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
    132.8     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
    150.2     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
    163.2     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
    176.0     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
    191.7     0       0  res<=128 depth=0 captures<=1 updates/frame<=1
```

Full series: `portal_activity.csv`.

Peak offscreen image render targets: **14** (largest dimension 2304px) at t=12.0s.

Full series: `render_target_census.csv`. ⚠ `cpu_bytes` there is the CPU-side copy an image still holds; a target uploaded and dropped reports 0 and is still costing VRAM.

## Scene and ECS workload

```text
             entities  archetypes   bodies  players
     start       2048        1509        0        0
       end      16384        1883      138        1
      peak      16384        1883      138        1
```

Entity count rose by 14336 and then held flat at 16384 for the rest of the session — the shape of a scene
spawning once, not a leak.

Peak sprites: **3765** (2960 visible), text2d 35, per-view projections 7 at t=12.0s. Full series: `draw_census.csv`.

Peak registered systems across visible schedules: **3445** in 34 schedules.

## Render passes

Mean and max over the sampled frames, from Bevy's `RenderDiagnosticsPlugin`.

Pass time, milliseconds:

```text
          mean            max  samples  diagnostic (ms)
        19.142         46.873      118  render/upscaling/elapsed_gpu
        11.017         64.906      119  render/ui/elapsed_gpu
        10.155         26.198      113  render/main_opaque_pass_2d/elapsed_gpu
         3.324          9.928      119  render/main_transparent_pass_2d/elapsed_gpu
         0.154          5.002      113  render/main_opaque_pass_2d/elapsed_cpu
         0.056          0.653      119  render/ui/elapsed_cpu
         0.022          0.949      118  render/upscaling/elapsed_cpu
         0.008          0.028      119  render/main_transparent_pass_2d/elapsed_cpu
```

Pipeline statistics, counts per frame:

```text
          mean            max  samples  diagnostic (count)
    331776.000     331776.000      113  render/main_opaque_pass_2d/fragment_shader_invocations
    269033.866    3029055.000      119  render/ui/fragment_shader_invocations
       561.244       1642.000      119  render/ui/vertex_shader_invocations
       280.588        820.000      119  render/ui/clipper_primitives_out
       280.588        820.000      119  render/ui/clipper_invocations
         4.000          4.000      113  render/main_opaque_pass_2d/vertex_shader_invocations
         2.000          2.000      113  render/main_opaque_pass_2d/clipper_invocations
         2.000          2.000      113  render/main_opaque_pass_2d/clipper_primitives_out
         0.000          0.000      119  render/main_transparent_pass_2d/vertex_shader_invocations
         0.000          0.000      119  render/main_transparent_pass_2d/fragment_shader_invocations
         0.000          0.000      119  render/main_transparent_pass_2d/clipper_invocations
         0.000          0.000      119  render/main_transparent_pass_2d/clipper_primitives_out
```

- CPU pass timings: **measured** (4 spans).
- GPU pass timings: **measured** (4 spans).
- Pipeline statistics: **measured** (12 diagnostics).

Full series: `render_diagnostics.csv`.

## Bevy systems and zones (Tracy)

> **Timer caveat.** this CPU does not advertise an invariant TSC (constant_tsc + nonstop_tsc); TRACY_NO_INVARIANT_CHECK=1 was set so the game could run. Tracy zone durations here come from a timer the kernel does not vouch for: treat their RATIOS as sound and their absolute microseconds as approximate.


`perf` cannot produce this: a Bevy system is not a native symbol, and a
render pass is a graph node rather than a function. Counts matter as much
as totals -- a cheap zone entered ten thousand times is a scheduling
problem, not a slow function.

## Whole session, ranked by total time

```text
  total_ms     count   mean_us    max_us  worst  zone
  194887.7         1 194887737.1 194887737.1   100%  bevy_app
  194770.0         1 194770039.3 194770039.3   100%  render thread
  191940.5       145 1323727.9 7263886.9     4%  update
  187224.4       145 1291202.8 7188048.7     4%  sub app{name=RenderApp}
  187214.5       145 1291134.2 7188014.7     4%  schedule{name=RenderRecovery}
  187053.9       145 1290026.6 7186367.5     4%  system{name="bevy_render::run_render_schedule"}
  187031.5       145 1289872.1 7186307.6     4%  schedule{name=Render}
  177980.8       145 1227453.9 6109974.7     3%  system{name="bevy_render::renderer::render_system"}
  177976.6       145 1227424.7 6109626.1     3%  main_render_schedule
  105960.3       145  730761.0 6599423.4     6%  sub app{name=RenderExtractApp}
   85970.4       145  592899.3 2464205.7     3%  main app
   85966.5       145  592872.2 2464190.5     3%  schedule{name=Main}
   44566.6       145  307355.7  650683.4     1%  schedule{name=PreUpdate}
   43628.8       145  300888.3  646076.4     1%  system{name="bevy_ggrs::schedule_systems::run_ggrs_schedules<bevy_ggrs::GgrsConfig<ambitio
   39617.7      1995   19858.5  241665.0     1%  ggrs{name="HandleRequests"}
   39518.6      1995   19808.8  241632.2     1%  schedule{name="AdvanceWorld"}
   39450.4      1995   19774.7  241571.2     1%  schedule{name=AdvanceWorld}
   39140.1      1995   19619.1  240875.7     1%  system{name="<bevy_ggrs::GgrsPlugin<bevy_ggrs::GgrsConfig<ambition_platformer2d_core::cont
   39073.2      1995   19585.6  240854.4     1%  schedule{name=GgrsSchedule}
   24407.3       145  168326.3  344059.0     1%  schedule{name=RunFixedMainLoop}
   24227.6       145  167086.7  341058.0     1%  system{name="bevy_time::fixed::run_fixed_main_schedule"}
   24093.6      2302   10466.4   53416.1     0%  schedule{name=FixedMain}
   15941.8      2302    6925.2   49354.6     0%  schedule{name=FixedPostUpdate}
    9632.0       145   66427.5 1821416.2    19%  schedule{name=RenderGraph}
    7027.1       145   48462.4  110535.3     2%  system{name="bevy_core_pipeline::schedule::camera_driver"}
    6828.9      2266    3013.7   24185.5     0%  schedule{name=Core2d}
    5814.7       145   40101.3  799205.0    14%  schedule{name=Update}
    3853.1      2302    1673.8   27194.0     1%  schedule{name=FixedFirst}
    3738.3      1995    1873.9   17967.2     0%  schedule{name=ReadInputs}
    3726.1    217340      17.1   13692.0     0%  multithreaded executor
    3659.7      2302    1589.8   25005.6     1%  schedule{name=FixedLast}
    2889.1       145   19924.9  100448.4     3%  schedule{name=Last}
    2881.2         1 2881226.2 2881226.2   100%  plugin build{plugin="ambition_app::app::plugins::AmbitionGameSimulationPlugin"}
    2662.8       145   18364.0   71460.2     3%  schedule{name=PostUpdate}
    2120.3      1965    1079.0   16405.6     1%  system{name="ambition_platformer2d_actor_monolith::features::ecs::actors::update::integrat
    1967.4       145   13568.0  998188.1    51%  schedule{name=ExtractSchedule}
    1930.6       145   13314.4 1723710.2    89%  system{name="bevy_core_pipeline::schedule::submit_pending_command_buffers"}
    1893.3       130   14563.6 1723448.5    91%  queue_submit{count=44}
    1594.7      2448     651.4   16998.4     1%  system{name="bevy_transform::systems::mark_dirty_trees"}
    1545.2      1965     786.4   13809.0     1%  system{name="ambition_platformer2d_actor_monolith::features::ecs::actors::update::tick_act
```

`worst` is the share of the zone's whole-session total spent in its single
slowest call. A zone at 90% is not a per-frame cost at all -- it is one
hitch that a total cannot distinguish from steady work. Rank those with the
steady-state table below, and find WHEN they hit in `frame_spikes.csv`.

## Steady state, ranked by recurring cost

The same zones with each one's SLOWEST call removed, so a one-time
compile/load/build cannot outrank work that recurs. `per_call_us` is what
the zone costs on an ordinary frame; multiply it by the frame count to see
what removing it would actually buy.

```text
 steady_ms  per_call_us     count    min_us  zone
  184676.7    1282476.8       145  387002.6  update
  180036.4    1250252.5       145  216034.2  sub app{name=RenderApp}
  180026.4    1250183.7       145  215865.8  schedule{name=RenderRecovery}
  179867.5    1249079.8       145  215219.0  system{name="bevy_render::run_render_schedule"}

Full report: `tracy_summary.md`. Raw trace: `tracy.trace`.

## Which phase of the frame owned the time

Mean milliseconds per frame over 145 frames, summing to 1319.88ms:

```text
  739.34 ms   56.0%  outside
  309.32 ms   23.4%  PreUpdate
  169.91 ms   12.9%  RunFixedMainLoop
   44.05 ms    3.3%  Update
   22.03 ms    1.7%  Last
   20.24 ms    1.5%  PostUpdate
    7.82 ms    0.6%  StateTransition
    4.43 ms    0.3%  SpawnScene
    2.74 ms    0.2%  First
```

Wall against CPU, per phase. `cpu/wall` is roughly how many cores the phase kept busy: near zero is a STALL (wall time with nothing running), around one is serial work, above one is parallel work.

⛔ NOT `wall - cpu`. The census reads `CLOCK_PROCESS_CPUTIME_ID`, which sums EVERY THREAD, so that difference goes negative on any parallel phase and is not a stall. This table printed it as one until 2026-09-02.

```text
    wall      cpu  cpu/wall   phase
  739.34  3309.82     4.48   outside
  309.32  1379.89     4.46   PreUpdate
  169.91   762.99     4.49   RunFixedMainLoop
   44.05   197.45     4.48   Update
   22.03   114.19     5.18   Last
   20.24    98.76     4.88   PostUpdate
    7.82    36.88     4.72   StateTransition
    4.43    22.77     5.14   SpawnScene
    2.74     8.21     3.00   First
```

⛔⛔ **THIS SPLIT IS NOT CPU WORK.** The game emitted `untrustworthy=render_blocking` for this run: the census attributes wall time between markers, so time spent BLOCKED on the GPU lands in whichever phase brackets it. A phase at 30% here may be waiting, not working, and nothing in the numbers distinguishes the two.

Use it for the frame TOTAL and for comparing one run against another taken the same way. To attribute CPU cost to a phase, take a run with no rendering — or get per-system zones, which need Tracy to actually connect.
```text
```

From `[census] phases`, which needs no profiler and works on every
platform that can write to stderr. `outside` is the gap between the end
of `Last` and the next `First`: present/vsync wait when windowed, the
runner loop when headless. A phase with no mark of its own is charged to
the phase before it, so these are frame shares rather than schedule
totals. Full series: `schedule_phases.csv`.

## Observer effect (what the profiler itself cost)

## Where the native time went

## Assets and render resources

- Decoded images: 105 → 294 (91.9 MP, 367.8 MB of decode work).
- Images resident at end: 266.
- **Resident image BYTES at end: 367.8 MB.** This is the number a residency budget is chosen against; a room-to-room walk gives its shape, and the hall was 2153 MB before sheets went render-world-only.

Decode counts only ever rise. A rise with no new room is the same asset
being decoded again; `image_decodes.csv` names which.

- Busiest arrival window: **145 images (7.4 MP)** at 11.0s. Each is extracted into the render world once, so this is what a frame spike is made of.

⚠ 20 GENERATED image(s) were allocated during gameplay (48.0 MP) — atlases or render targets, not content. Real cost, but nothing to demand earlier.

✔ No notable texture decoded while gameplay was live.

Textures decoded more than once:

```text
  21x  <runtime-generated>
```

## Collection status

- `warm-build`: 0
- `perf-record`: 137
- `perf_report`: 0
- `perf-report-by-dso`: 0
- `perf.data`: 604342940 bytes

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
| `tracy_summary.md / tracy_zones.csv` | per-Bevy-system and per-render-pass zones | yes |
| `tracy_zone_windows.csv` | the same zones bucketed into time windows | yes |
| `tracy.trace` | the raw Tracy trace, for the GUI | yes |
| `perf_windows/` | one flat perf report per time slice | no |
| `perf_report.txt` | whole-run flat perf report | yes |
| `perf-report-by-dso.txt` | which shared object owned the CPU | yes |
| `game-stderr-stamped.txt` | the game's own log, stamped with seconds since launch | yes |
| `perf.data` | the raw perf capture | yes |

