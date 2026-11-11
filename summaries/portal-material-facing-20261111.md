# Control facing and accepted material facing

Source: `e5a0e4ac1`. Ordinary bodies now project facing between canonical
material axes and the movement reference side. Scale does not change this
sign. Reflection and turn do. A perpendicular request cannot be expressed by
a facing flip and gives no override. Crawler route direction keeps its own
policy meaning and converts through material parity for control and travel.

Brain snapshots publish facing in control axes. The kernel converts a request
once, then the selected movement policy applies it. Axis requests use the
existing air, ledge and turnaround rules. The actor writer after the kernel is
removed. A refused movement candidate can no longer have facing overwritten
by that writer. Crawler neighbor steering submits a route request instead of
changing the body before admission.

Neutral human input now sends zero, which leaves facing to the movement
policy. It does not reissue a stored pose as a request. Conducted poses and
limb strikes project material facing into control axes. A limb sweep uses the
host's physical side. Concrete melee requests take their origin and canonical
facing from the accepted BodyKinematics, rather than a turn request. The old
bare-origin resolver signature is removed.

The core checks include reflected crawler travel on block faces and an angled
chain. The normal envelope of the existing curved contact query now bounds
signed plane motion, so fast tangential travel does not exhaust the work
budget during a small righting turn. The general conservative envelope stays
in force when the tighter estimate cannot be used. Typed contact refusals and
the work limit stay in place.

For root material, signed plane distance has a derivative at the interval
midpoint and a bounded second derivative. In the obstacle frame, the linear
pivot offset turns at the obstacle rate; the material arm turns at the rate
difference. The bound uses those two rates and the relative linear velocity.
It applies to every vertex of the retained conservative region. Each clip
retains every source point that can contact during the interval. An
unavailable finite estimate retains the original enclosure; it cannot accept
an unproved miss. A temporary signed-distance probe used seed 20261111,
1,000 profiles and 132,000 time samples, with no bound violations. This is not a gameplay speed comparison.

The core checks cover half and double scale, turn, both parities, both facing
requests, neutral input and the Axis air-facing lock. A temporary identity
projection made the physical-front assertion fail. It was removed before the
final batch.

The Warden app witness now keeps his brain active. Each scale case has its
own scene, with positions held while the controller can turn. The real guard
publisher and resolver turn a strike at his physical front under reflected
half scale and turned double scale. The existing Warden counterattack, top
opening and break/form cases remain part of the check.

## Validation

### Source checkpoint `e5a0e4ac1`

All commands below passed on one fixed source tree.

```bash
cargo test -j6 -p ambition_platformer2d_core -p ambition_platformer2d_actor_monolith -p ambition_characters -p ambition_boss_encounter -p ambition_combat --lib -- --test-threads=6
```

Result: 234 passed, 0 ignored; 546 passed, 0 ignored; 825 passed, 3 ignored; 600 passed, 0 ignored; 884 passed, 1 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- shock_warden_shield --test-threads=4
```

Result: 4 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- portal_similarity_slice --test-threads=2
```

Result: 2 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- projectile_portal_transit --test-threads=6
```

Result: 23 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- both_gnu_ton_fists --test-threads=2
```

Result: 2 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- a_gnu_ton_fist_hits_once_after_three_cuts --test-threads=1
```

Result: 1 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- actor_phase_split --test-threads=4
```

Result: 2 passed, 0 ignored.

```bash
cargo check -j6 -p ambition_app
```

Result: exit 0.

```bash
cargo test -j6 -p ambition_demo_smash_app -- --test-threads=6
```

Result: 18 passed, 0 ignored; 55 passed, 6 ignored; 0 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_workspace_policy -- --test-threads=6
```

Result: 0 passed, 0 ignored; 35 passed, 0 ignored; 0 passed, 0 ignored.

```bash
python3 scripts/checks/check_no_warnings.py -p ambition_platformer2d_core -p ambition_characters -p ambition_platformer2d_actor_monolith -p ambition_boss_encounter -p ambition_combat -p ambition_app
```

Result: exit 0.


### Merge checkpoint `09f0afbc4`

All commands below passed on one fixed source tree.

```bash
cargo test -j6 -p ambition_platformer2d_core -p ambition_platformer2d_actor_monolith -p ambition_characters -p ambition_boss_encounter -p ambition_combat --lib -- --test-threads=6
```

Result: 234 passed, 0 ignored; 546 passed, 0 ignored; 825 passed, 3 ignored; 600 passed, 0 ignored; 884 passed, 1 ignored.

```bash
cargo check -j6 -p ambition_app
```

Result: exit 0.

```bash
cargo test -j6 -p ambition_app --test app_it -- shock_warden_shield --test-threads=4
```

Result: 4 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- portal_similarity_slice --test-threads=2
```

Result: 2 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- rollback_schema_baseline --test-threads=4
```

Result: 4 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- a_room_cutscene_starts_under_a_rewind --test-threads=2
```

Result: 2 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- the_conversation_its_replay_does_not --test-threads=2
```

Result: 2 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- actor_phase_split --test-threads=4
```

Result: 2 passed, 0 ignored.

```bash
python3 scripts/checks/check_no_warnings.py -p ambition_platformer2d_core -p ambition_characters -p ambition_platformer2d_actor_monolith -p ambition_boss_encounter -p ambition_combat -p ambition_app
```

Result: exit 0.


The merged batch did not repeat Smash, workspace policy, projectile or GNU-ton
cases. Those passed at the source checkpoint before the room-state merge.
The merged batch checks the affected room, control and replay integration.

The following extra case passed on `09f0afbc4`:

```bash
cargo test -j6 -p ambition_demo_sanic --lib -- momentum_body_crosses_the_ramp_full_loop_and_runout_without_stalling --test-threads=1
```

Result: one passed, 109 filtered out. The test took 5.28 s after compilation.
This does not establish the full Sanic or workspace result.


The source tree stayed fixed during each batch. Logs and terminal results are
at `target/portal-facing-gates/`. The deliberate defect is recorded at
`target/portal-facing-poison.log`. An early app build reported a missing Sanic
manifest in Bevy's derive macro. The manifest was present with unchanged
metadata. The final build passed without a Sanic source change.

## Remaining scope

This packet fixes the reflected-facing error found by the guard witness.
It does not close the full physical integration of the other motion models.
General kernel and other free-body remaining-time continuation, moving-target
hurt CCD, guard response velocities, held-item placement/release, complete
numeric fault identity/effects, device/performance gates and full PSIM
acceptance remain open. No authored map or art changed. No new GPU or device
capture was made. The full workspace and feature-union matrix were not run.


## Wall and ledge contact facing

`42fe3ccfd` maps the wall normal and ledge inward direction from the control
frame into canonical material axes before each facing write. Both ledge
acquisition and continued hanging use this rule. A perpendicular request
keeps the current facing. The wall check covers half, ordinary and double
scale under both parities. The ledge check covers acquisition and hanging
from both sides, under half and double scale, turn and reflection.

These commands passed on the fixed source tree:

```bash
cargo test -j6 -p ambition_platformer2d_core -p ambition_platformer2d_actor_monolith --lib -- --test-threads=6
cargo check -j6 -p ambition_app
cargo test -j6 -p ambition_demo_smash_app -- --test-threads=6
```

Results: 885 core tests passed (one ignored), 600 actor tests passed, the
assembled app check passed, and 73 Smash tests passed (six ignored).
Terminal results and logs are at `target/portal-ledge-facing-gates/`.
No full workspace, feature-union, GPU or device gate was run for this change.
The remaining scope above stays open.
