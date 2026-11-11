# Native loose-item flight — 2026-11-11

Source: `70c9fbf75`. Upstream merge: `55864a788`.

Loose items use native material casts against raw room solids. The movement
plan retains each accepted leg and its world clock. Forces apply once.
World contacts and root crossings use the remaining tick. Root scale and
reflection persist. Native hurt queries use the accepted legs. Impacts and
portal events wait until the complete plan passes admission.

Righting uses the last free leg, its root link and its duration. A contact
or root crossing at tick end leaves no free leg for the turn. Free-flight
history is retired after its contacts and root transfers are processed.
Support carry keeps its path for the shared transit phase.

Bounce and slide remain dimensionless. Rest speed uses canonical units.
Impact speed uses the physical contact chart and a checked wide magnitude.
Items without rebound stop on support; a wall does not settle them in air.
The accepted-prefix query and linear separation proof have one shared owner.
The proof accounts for the remaining world-tick interval.

## Validation

- Without portals: 58 held-item tests pass.
- Selected portal features: 60 held, 887 core, 138 portal and 30
  projectile-combat tests pass. Two owner probes remain ignored.
- The assembled app check, 23 projectile/held-projectile app checks,
  all four 80/40 slice checks, 73 active Smash checks and 35 policy checks
  pass. Six Smash probes remain ignored.
- The warning check found an unused prepared pose. After the pose was
  committed to the staged value, all 60 held tests and the default
  all-target warning checks pass again. No policy exception was added.
- After the upstream merge: app compilation, four schema checks, both
  held-projectile portal checks, 73 active Smash checks and the default
  all-target warning checks pass.

The new guards cover two thin-wall bounces in one tick, a scaled reflected
exit-chart bounce and reverse crossing, and recipient refusal before pose
or portal-event commit. The refusal guard also checks righting in the new
root chart and physical contact speed.

Logs and commands are in `target/portal-item-motion-gates`. The JSON receipt
retains each batch, including the corrected warning failure.

## Limits

No full workspace, device or GPU gate ran for this checkpoint. The warning
check covers default features; the owner batch selects the named portal
features. It does not prove the full workspace feature union.

The slice took 426.93 seconds after righting integration, versus 305.60
seconds before it. These runs are uncontrolled. They are a profiling lead,
not a performance verdict. R5 remains open.

Full PSIM still needs general actor and support-carry continuation, moving
recipient CCD, terminal effect timing, other birth paths, full held
geometry, the bounded numeric law and fault effects, and controlled
performance and device/GPU gates.
