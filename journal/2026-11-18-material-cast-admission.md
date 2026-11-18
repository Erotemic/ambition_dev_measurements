# Material cast admission cost

## Desktop evidence

`desktop-autoplay-native-20261118T153023Z` uses `5acadeade9fb` on the desktop.
It completes the autoplay route. Async compute accounts for 38.50% of sampled
cycles. Its leading symbols include `map_volume` at 4.26% of all sampled cycles,
plus similarity-map construction and material cast preparation. The first Hall
graph takes about 24.3 seconds from Building to Going.

## Change

Local implementation commit: `9027c87f7b88061e4c91940a377cbf51a5a6c76e`.

A cast now validates its numeric projection without constructing a discarded
shape. It reuses admission for an unchanged map and reuses source bounds.
Shifting a similarity map retains the admitted basis and scale, while checking
the new translation and its inverse. Collision geometry and route policy are
unchanged.

## VM measurement

Raw source, build logs, CSVs, metadata, and results are in
`profiles/material-cast-admission-vm-20261118T160546Z/`.
The driver links the production core. Both versions compile that core and the
driver at optimization level 3. Before and after executions alternate. Each
of the 16 cases has nine samples per version and 100,000 casts per sample.
The table gives the range of median preparation rates across its four cases.

| Shape | Preparation rate relative to baseline |
| --- | --- |
| aabb | 1.35–1.86x |
| obb | 1.56–2.30x |
| circle | 1.30–1.68x |
| convex | 2.00–2.60x |

Moving world-space axis-aligned boxes changed from 44.85 ms to 33.18 ms per
100,000 casts. Moving mapped boxes changed from 51.82 ms to 33.62 ms.
All bounds checksums match. The probe has no portal cuts or source rotation;
it covers translated rotated boxes and a scaled, reflected map. These are
kernel results. No new desktop frame-time result is claimed.

## Verification

- All 905 core library tests passed; one existing test was ignored. This run
  used optimization level 3 for the core and one compiler and test thread.
- New checks cover preserved map basis and inverse, equivalent numeric refusal
  for validation and projection, and a valid start with an invalid endpoint.
- `cargo check -p ambition_app -j 1` passed.
- No parallel builds or broad integration sweeps were started for this patch.

Changes remain local at the maintainer's request. No push or pull was performed
for this optimization.
