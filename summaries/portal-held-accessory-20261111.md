# Held accessory across two reflections

The source correction is `04a1625b1`. The capture control is `db953edef`.
This receipt covers station I in portal_lab. It does not close full PSIM
acceptance or physical attachment scale and release behavior.

The robot picks up the authored javelin at tick 61. The tool stages an approach
at tick 75 with upward velocity. After this staging, both crossings use
continuous control and gravity. There is no position reset between crossings.
The body crosses at tick 82, clears the upper opening at tick 87, and crosses
back at tick 95. Reflection parity is retained between crossings and restored
after the second. The item stays in custody throughout.

The film exposed a source-wall defect. A held sprite's centre can be behind
the opening while its holder remains in front. Both held-picture paths now
publish `ReachesFrom` at the holder root and `PresentationOf` for the owner.
The common carry uses the owner's piece budget. The focused guard checks a
refused hop limit and exact source and exit mesh bounds for a spear shape.

## Render witness

The two films use Vulkan llvmpipe, Low quality, 1280 by 720, windows disabled,
and fixed views at zero and ninety degrees. Each film has 140 frames.
They have 138 common simulation ticks, including 111 ticks with the item held.
Player and item physics, and the held sprite's world transform and flips,
match at all common ticks. Before/after physics match at 136 common ticks.

At tick 81, the held capture has two visible pieces in each view. The source
piece has minimum render X -543.999974; its portal face is X -544. The exit
piece has minimum render X -544.000026. The small difference is float rounding.
The source tip no longer draws in the wall. Partial, cleared and return
frames were inspected in both views.

```bash
xvfb-run -a env AMBITION_QUALITY_PROFILE=low VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/lvp_icd.json target/debug/capture_scene portal_lab 1270,720 out/f.png 1280x720 --player-at 1850,872@60 --player-holds attack@61 --player-holds none@62 --player-at 1228,872@75:-270,-450 --player-holds left@75 --player-holds right@82 --player-holds left@84 --player-holds right@97 --player-holds none@105 --warmup 55 --frames 140 --stride 1 --tick-per-frame --camera-zoom debug --portal-effect off --portal-convention reflection --art-snapshot --camera-roll 0
```

Repeat with a separate output directory and `--camera-roll 90`. Fixed-focus
roll uses the common camera resolver. The snapshot records all sprites and
the actual main-camera transform. Local artifacts are under
`target/portal-i-held/fixed/`: `gates.json`, `witness.json`, the two films and
art snapshots. `target/portal-i-held/final/` retains the pre-fix witness.

## Validation

All 791 active owner tests pass: presentation 133, renderer 497 and view 161.
Two view probes remain ignored. All 35 policy cases pass. The capture tool
build, seven tool tests and assembled app check pass. The app, renderer,
presentation and view all-target warning check passes under default features.
The assembled app check passes after the upstream geometry-observer merge.
Logs use `target/portal-i-fix-`; the gate file is
`target/portal-i-fix-gates.json`. Tool tests use
`target/portal-i-tool-gates.json`. The merge check is
`target/portal-i-merged-check.log`. This is a focused lane; the full workspace,
non-default features, hardware GPU and mobile acceptance are not covered.
