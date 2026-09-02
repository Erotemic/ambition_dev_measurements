# Sprite clip-guard census — the whole roster

## What this measured

| fact | value |
| --- | --- |
| date | 2026-09-02 |
| instrument | `scripts/measure_clip_population.py` |
| instrument commit | `c6a9712` on `d129-composited-frames` (`ambition_sprite2d_renderer`) |
| interpreter | `tools/ambition_sprite2d_renderer/.venv` (native `resvg-py`) |
| scope | every target `discover_all_targets()` returns |
| result | **209 targets in 1007s — 0 would not render; 34 flagged, 465 edges** |

## ⛔ The first COMPLETE population, and why the earlier numbers were short

Every previous D129 count was taken without native `resvg-py`, so 22 SVG targets
could not render: the figure of **27 flagged / 362 edges was over the 187 that
did**. `resvg-py` is a DECLARED dependency of the renderer
(`pyproject.toml: "resvg-py>=0.3"`) — the gap was the interpreter in use, not the
machine, and the renderer's own README documents the `.venv` that has it.

⭐ **`hunny_horror_boss` is what the gap was hiding**: 59 edges, severity 0.402,
third on the worklist, absent from every earlier count. `paradox_barber` (9),
`data_lovelace` (14) and `charley_beagle_svg` (1) are also new.

✔ And twelve targets that had only ever been "not measured" are now a real CLEAN
result, including `perfect_cellular_automaton`, `noether`, `patent_clerk`,
`carl_stargan` and `player_robot_v3`.

## ⚠ It RANKS; it does not classify

`severity = stake × min(ratio, 1)` — how much ink arrives at the boundary, times
how squarely. **It is a worklist order. No row is evidence that a frame is
wrong.** The instrument cannot separate a tip from a cut, because the drawing
canvas IS the logical frame and the ink beyond it was never rendered:
`pirate_admiral`'s plume (a tip) and `super_sanic`'s historical spike (a cut)
have the same profile and both land exactly on the guard's 0.5 threshold.

⛔ The `mary_o_v2` SVG lineage (v2 12 edges, v2_tall 5, v2_fire 3) is NOT the
composited pixel-art Mary-O whose flush-standing was ruled a false positive —
different targets, different authoring road, tapering rather than flat profiles.
Do not carry that conclusion across, and do not pad those canvases on it.

## The run

```text
target                             edges  worst  stake  FLAT TAPER ABRUPT  worst edge profile
smirking_behemoth_boss               101  1.000  1.000    78     5     18  bottom [208, 208, 208, 208, 208, 208, 208]
super_mary_o_pipe_body                 2  0.828  0.828     2     0      0  top    [53, 53, 53, 53, 53, 53, 53]
super_mary_o_pipe_top                  1  0.828  0.828     1     0      0  bottom [53, 53, 53, 53, 53, 53, 53]
paul_diracula                         29  0.477  0.492     1    21      7  bottom [63, 65, 65, 64, 64, 62, 62]
hunny_horror_boss                     59  0.402  0.431    35    12     12  bottom [69, 70, 71, 72, 73, 74, 74]
paradox_barber                         9  0.329  0.388     0     8      1  bottom [62, 63, 66, 68, 70, 71, 73]
snakes_on_a_cartesian_plane            2  0.320  0.338     1     1      0  bottom [54, 56, 56, 56, 56, 56, 57]
le_beast                              39  0.295  0.336     0    28     11  right  [43, 44, 46, 47, 49, 47, 47]
data_lovelace                         14  0.285  0.338     0    10      4  bottom [54, 55, 56, 58, 60, 62, 64]
super_mary_o_flag_pole_body            2  0.281  0.281     2     0      0  top    [9, 9, 9, 9, 9, 9, 9]
super_mary_o_flag_pole_top             1  0.281  0.281     1     0      0  bottom [9, 9, 9, 9, 9, 9, 9]
davy_hylbert                          44  0.266  0.266     0    21     23  right  [34, 32, 31, 30, 32, 33, 34]
busy_beaver                            7  0.256  0.256     0     5      2  right  [41, 39, 36, 35, 34, 33, 33]
mary_o_v2_tall                         5  0.256  0.256     1     2      2  bottom [41, 39, 39, 37, 32, 31, 30]
mary_o_v2                             12  0.250  0.250     0     6      6  bottom [40, 39, 39, 38, 32, 31, 30]
pipi_tau                              38  0.213  0.234     0    13     25  left   [30, 30, 30, 32, 32, 33, 33]
galwah                                 5  0.198  0.198     0     0      5  bottom [19, 18, 17, 16, 15, 15, 15]
puppy_slug                            17  0.174  0.208     0    17      0  left   [20, 21, 21, 22, 24, 24, 24]
mary_o_v2_fire                         3  0.105  0.181     0     3      0  top    [29, 31, 33, 35, 37, 44, 50]
leib_knives                            3  0.091  0.111     0     2      1  left   [32, 33, 33, 33, 33, 35, 39]
hypatia_prime                         13  0.078  0.078     0     3     10  bottom [10, 6, 6, 9, 4, 4, 4]
mantis_lancer                          2  0.077  0.100     0     2      0  bottom [24, 24, 26, 25, 27, 28, 31]
snakes_on_a_paper_plane                1  0.070  0.100     0     1      0  bottom [16, 18, 20, 23, 22, 20, 18]
charley_beagle_svg                     1  0.065  0.117     0     1      0  top    [15, 19, 21, 23, 24, 25, 27]
stochastic_parrot                      1  0.052  0.086     0     1      0  bottom [11, 11, 13, 15, 15, 14, 18]
georg_canter                          13  0.049  0.052     3     1      9  right  [15, 12, 11, 12, 14, 15, 16]
flying_spaghetti_monster_boss         29  0.044  0.044     4    19      6  left   [28, 27, 28, 27, 28, 28, 27]
oiler_vfx                              1  0.029  0.055     0     1      0  right  [7, 7, 6, 6, 5, 9, 13]
raptor_stalker                         1  0.029  0.029     0     0      1  bottom [7, 7, 6, 6, 5, 5, 4]
pirate_admiral                         2  0.027  0.055     0     2      0  top    [7, 9, 9, 10, 10, 11, 14]
pirate_lookout                         2  0.027  0.055     0     2      0  top    [7, 9, 9, 10, 10, 11, 14]
pirate_navigator                       2  0.027  0.055     0     2      0  top    [7, 9, 9, 10, 10, 11, 14]
pirate_quartermaster                   2  0.027  0.055     0     2      0  top    [7, 9, 9, 10, 10, 11, 14]
pirate_raider                          2  0.027  0.055     0     2      0  top    [7, 9, 9, 10, 10, 11, 14]

severity = stake x min(ratio,1): how much ink arrives at the boundary, times how squarely.
```

Full per-target/per-edge data with inward profiles is in the run's
`census.json` (not committed — `profiles/` is ignored by design).
