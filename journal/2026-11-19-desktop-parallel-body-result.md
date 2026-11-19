# Desktop result after parallel body integration

## Capture and method

Compare native passes from `desktop-timeline-run-20261119T140515Z`, at
`0c4ba616483d`, and `desktop-timeline-run-20261119T195056Z`, at
`1f52c6dffd59`. The second source contains the body integration change
`7a79333f52`. Both report only a dirty measurements submodule. Both use the
profiling Cargo profile, the Intel HD Graphics 630 Vulkan adapter, High quality,
full parallax, four-sample MSAA, and uncapped Immediate presentation. Neither
uses an actor population cap or brain override.

Select census rows with wall time from Hall entry +25 seconds, inclusive, to
entry +33 seconds, exclusive. This interval follows the first navigation-ready
status in both runs and precedes departure. Weight frame means by frames and
simulation phase means by ticks. Do not average per-window quantiles into a
session quantile.

| Measure | Previous native | New native |
| --- | ---: | ---: |
| Hall entry, wall seconds | 40.815 | 41.923 |
| Selected interval, wall seconds | 65.815 to 73.815 | 66.923 to 74.923 |
| Census windows | 8 | 8 |
| Sampled frames | 123 | 226 |
| Simulation ticks | 496 | 491 |
| Bodies | 538 | 538 |
| Mean frame, ms | 67.230 | 36.240 |
| Approximate FPS from mean frame | 14.87 | 27.59 |
| Integration, ms per tick | 6.378 | 2.823 |
| Targeting, ms per tick | 1.313 | 1.605 |
| FeatureViewSync, ms per tick | 1.349 | 1.592 |
| Observe, ms per tick | 0.942 | 1.099 |
| Sum of measured simulation phases, ms per tick | 14.462 | 12.681 |
| PreUpdate, ms per frame | 59.186 | 28.276 |
| Update, ms per frame | 3.565 | 3.907 |
| PostUpdate, ms per frame | 2.420 | 2.217 |

Frame time is 46.1% lower, or about 1.86 times the frame rate. Integration wall
time is 55.7% lower, or about 2.26 times faster. The results support the moving
crowd VM measurements. The source range also contains other work, and the camera
positions differ, so this is not an isolated desktop A/B of one commit. Both
runs have 16,460 sprite entities in the selected interval. Visibility changes
with the camera. Almost all of the frame-time reduction is in PreUpdate, which
contains the fixed simulation driver.

The former entry+23 to entry+35 comparison also shows the gain: 67.158 to
35.404 ms per frame and 6.391 to 2.820 ms per integration tick. The narrower
interval above avoids the end of graph preparation in the new run.

Computed phase values and weights are in
[the comparison data](../summaries/desktop-timeline-run-20261119T195056Z-comparison.json).

## Remaining work

Targeting and view publication now each cost about 1.6 ms per tick. Together,
they use about one quarter of measured simulation phase time. Target selection
still has a candidate scan per actor. A spatial selection design must preserve
room, faction, team, grudge, and stable-distance tie rules, including exact
room-wide nearest selection. View publication needs an allocation and copy
measurement before a representation change.

The native 66% to 75% perf slice supports these candidates: target selection is
3.88% of samples, `MoveSpec::strike_reach` is 1.76%, and allocation and memory
copy symbols appear on the main and compute threads. Sample shares are not
absolute CPU-time comparisons. Inspect move-reach computation and allocation
ownership before adding a cache. The whole-run async grid and ledge-probe
samples also include navigation preparation.

The first Hall navigation-ready status moves from 59.964 to 65.650 wall seconds,
or from 19.149 to 23.727 seconds after entry. Both graphs still report 81
surfaces, 1,932 links, and 23,828 rollouts. This is elapsed time to a log marker,
not an isolated graph-build CPU measurement. Parallel body work can compete
with graph preparation for CPU time; the trace does not isolate that effect.

These findings select further engine work. They do not justify reducing the
Hall cast, skipping resting actors, or changing actor tick rates.

## Tracy and memory evidence

The Tracy pass exits with status 137 during startup. The saved trace has two
frames, 4,092 zones, and a 12.76-second span. It contains no Hall gameplay and
cannot support per-system or GPU attribution for this run. The native pass
completes its 100-second capture.

Runtime limits are 7,908 MiB for the Tracy game and 3,954 MiB for the collector.
Both scope results say `success`, despite the game status 137. Those results do
not identify an OOM cause. No kernel kill record is included.

Available host RAM rises from 10,436,852 KiB before the Tracy build at 19:50:58
UTC to 29,438,168 KiB after the build at 20:06:29 UTC. Capture starts after that
change. This is consistent with a VM or another large process leaving during
the build. It does not prove which process left or why. Runtime and export
limits do not cover compiler memory. Build job concurrency remains unchanged.

## Validation

The comparison reads the recorded CSV rows directly. Body counts, quality,
source revisions, graph-ready markers, and Tracy status are checked against the
bundle. No production code changes and no new build or game capture are part of
this analysis.
