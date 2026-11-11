# Native settled-item support — 2026-11-11

Source: `fcccb3b32`. Portal-gun merge: `2317b79cb`. Policy fix: `01a3c3bf3`.

Settled items probe canonical material against raw room geometry at the
support's previous pose. A rotated AABB corner cannot supply false support.
Far-side support supplies a velocity through the native contact path. The
shared translation authority admits carry. Refusal keeps the accepted pose
and settled state. Static accepted bodies avoid redundant change marks.
Projectile and item responses share surface-velocity conversion.

The far-side guard found a portal-cut boundary normal that blocked tangent
carry. The translation authority now uses native boundary planes to prove
continued separation of full linear shapes. Their clipped subsets cannot
collide in that case. Curved contacts keep the continuous solver result.

## Checks

- Final owner batch: 56 held, 887 core and 359 shared tests pass; one core
  test remains ignored. Guards cover false corner support, scaled far-side
  platform carry, refusal and recovery.
- Before merge: four real-app slices and two chained-limb checks pass.
  Smash: 73 pass, six ignored. Policy: 35 pass. App and default-feature
  all-target warning checks pass.
- After the ordinary held portal-gun merge: app, 57 held tests, schema 459
  baseline (four tests), all four slices and default app/held warnings pass.
- The merge exposed an obsolete required-file policy for the deleted gun
  files. `01a3c3bf3` removes that layout assertion. All 35 policy tests pass.

The [command manifest](portal-item-support-20261111.json) records each batch.
Logs use `target/portal-item-support-gates/` and
`target/portal-item-support-owners-complete.log`.

## Cost lead and open scope

The debug slice took 292.11 s before merge and 296.89 s after merge; the
previous birth-admission batch took 170.72 s. These separate validation runs
are not a controlled benchmark. A 15-second CPU sample captured 15,792
samples, but the stripped development binary gives no function attribution.
R5 needs a controlled, symbolized before/after measurement.

Dynamic item translation, bounce and body strikes still use the old root
AABB path. Their native sweep and remaining-time continuation are open.
Full PSIM, feature-union, workspace, GPU and device acceptance remain open.
