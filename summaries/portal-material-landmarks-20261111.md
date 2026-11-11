# Material landmark placement — 2026-11-11

Source: `5db057506`. Merge: `f0af28d7c`.

Landmarks use the canonical rig feet offset, canonical facing and the body's
accepted material map. Gravity and the root AABB no longer place them.
A failed map stays a typed numeric fault. It cannot select the missing-hand
fallback. The fixed hand also passes through the material map.

Item position readers keep the prior position on refusal. Item views omit
refused rows. The default held-item renderer reads the published simulation
hand when the art has no hand. A hand muzzle resolves its hand before firing
state changes. Other muzzle types do not query a hand.

## Checks

- Six owner libraries: 2,202 active tests pass; five probes remain ignored.
- Final actor library: 600 pass. Final renderer fallback witness: one passes.
- Real-app pet contact: two pass. Real-app portal slice: three pass, including
  partial contact, reverse crossing, the renderer map, item release and replay.
- Smash: 73 pass; six probes remain ignored. Policy: 35 pass. Primeval: seven pass.
- App checks and default-feature all-target warning checks pass. The final
  hand-muzzle refinement passes the actor, app and warning checks again.
- After the wall-climbing merge, the app check passes. The earlier behavioral
  checks precede that merge; they were not repeated for it.

The [command manifest](portal-material-landmarks-20261111.json) records each
serial batch. Local logs use `target/portal-landmarks-owners.log` and
`target/portal-landmarks-gates/`. These are validation runs, not benchmarks.

## Open scope

These points are in the body's root chart. Portal-path attachment admission,
the held object's own shape/scale/orientation, charge-shot placement, pet
approach distances, free-item collision and the rest of PSIM remain open.
No full workspace, exhaustive feature-union, new GPU capture, device or
performance gate ran for this change. This receipt does not close those gates.
