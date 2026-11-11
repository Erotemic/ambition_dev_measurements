# GNU-ton independent limb render witness

The source correction is `9d6f9da4f`. This is a staged diagnostic scene in
`gnu_ton_arena`. It does not add portals to the authored portal lab. It does
not close full PSIM acceptance or provide hardware GPU or mobile measurements.

The witness exposed three faults. A generated limb picture used its host's
physical root. Generated animated roots had no composed-image demand. A
cached image could stop drawing before its finishing pipeline was ready.
The source correction gives each limb picture its own material root, declares
image demand for all animated roots in a portal room, and keeps the image
active until its source images and both GPU passes are ready. The readiness
check uses the image's own views and pipelines. Unrelated shaders do not hold
it active. An unchanged ready image still rests.

## Evidence

The final film has 40 frames at ticks 210 through 249. It uses Low quality,
1280 by 720, Vulkan llvmpipe and a fixed view. Eight diagnostic apertures appear
at tick 220. The natural phase-two boss pose continues without a pose override.
The main image and private targets were inspected at tick 230.

The left fist has source path `[]` and carried paths `[4]`, `[4,0]` and
`[4,0,2]`. The carried scale is 0.5. The last path is reflected. The right fist
has source path `[]` and carried path `[6]`, with scale 2 and reflection.
Both pictures name their own native limb body. They have separate capture
layers. The sum of canonical piece areas equals the full body area for each
fist: 11816.4793 square units. The maximum world-area residual from the scale
squared law is 0.00152 for the left fist and 0.0102 for the right fist.
The left final fragment is a partial lower slice, not a full hand.

Before the readiness correction, the giant's 2304-square source target had
pixels but its finishing target was transparent. After the correction, all
three source and finishing image pairs have pixels. The main image retains
the giant body and the carried fist fragments. All six image cameras are
inactive at tick 230. This confirms that the correction preserves caching.

Five carry families allocate 12,713,984 bytes, or 12.125 MiB, with no material
proxy or reduced-resolution fallback. The three image pairs have dimensions
2304, 1152 and 288 pixels per side. Their RGBA descriptors total 53,747,712
bytes, or 51.2578125 MiB. These image pairs are outside the carry-texture
budget. These are descriptor counts, not measured device allocations or
performance results.

The private target manifest, readbacks, frame snapshots and `witness.json`
are under `target/portal-gnu-multiple/final/`. The receipt records the command
below because local target artifacts are disposable.

```bash
xvfb-run -a env AMBITION_QUALITY_PROFILE=low VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/lvp_icd.json target/debug/capture_scene gnu_ton_arena 900,720 target/portal-gnu-multiple/final/f.png 1280x720 --player-at 400,1200@60 --boss-hp 0.45 --warmup 250 --frames 75 --stride 1 --tick-per-frame --camera-zoom debug --portal-effect off --portal-convention reflection --portal-budget 4,64,32 --art-snapshot --portal-at 220:100:577,910:0,-1:89.7 --portal-at 220:101:400,200:0,1:44.85 --portal-at 220:102:1515.4,915:0,-1:89.7 --portal-at 220:103:800,200:0,1:179.4 --portal-at 220:104:400,201:0,-1:44.85 --portal-at 220:105:400,400:0,1:44.85 --portal-at 220:106:400,401:0,-1:44.85 --portal-at 220:107:400,600:0,1:44.85 --dump-targets-at 230 --frames 40
```

## Validation

The renderer passes all 524 tests with `portal_render`. This includes an
animated root without controller labels and an unchanged image that waits for
GPU readiness, rests, and repaints after its source image changes. The host
passes 25 tests. All seven capture-tool tests pass. The tool build, assembled
app check, and default all-target warning check for app, renderer and host pass.
The three native GNU-ton cases cover simultaneous independent paths, contacts
with separate recipients in one tick, and chained damage with scaled launch
and rewind. They pass. All 35 workspace policy cases pass.

Logs and gate results use `target/portal-gnu-multiple/`: `readiness-tests.log`,
`host-tests.log`, `final-gates.json` and `acceptance-gates.json`. The final
source film is `final/`; earlier failing captures remain beside it.

This lane omits the full workspace and feature union, hardware GPU and mobile
acceptance, and performance comparison. The film is a render witness. The
separate native app cases establish damage and replay; the film alone does
not establish those results.

After upstream merge `4b3f1d6ff`, the app check, all three GNU-ton cases,
all 35 policy cases and the default all-target warning check pass again.
The projectile recipient refusal/resume case also passes with the remote
invalid recipient still present. The merge results are in `merged-gates.json`,
`merged-*.log` and `merged-recipient-refusal.log` in the same target directory.
