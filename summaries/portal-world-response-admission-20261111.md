# World response admission through scaled portals

The source correction is `10f8eaf95`. A selected rebound now admits its
returned velocity over every occupied material cell. The world contact and
its checked response have separate results. Contact geometry orders the leg.
An earlier terminal hit or interception retires a later response. A required
world response must pass before pose, bounce budget, hit ledger, guard rewards
or damage writes. Query refusals remain typed query failures.

## Evidence

The old implementation accepted a root rebound of `-1.8e38` through a 2x
portal chart. Its carried speed exceeded `f32` range. The focused check failed
before the correction, in `overflow-before.log`. The new check keeps the
contact witness and returns typed numerical overflow from its response.
The inverse 0.5x control accepts the same root speed. The existing moving-pair
check still verifies relative surface velocity in the root chart.

The runtime check retains a later rebound whose root value would overflow.
With no earlier recipient, it preserves the pose, velocity and bounce budget,
advances lifetime and retires the old sweep. An earlier ordinary hit consumes
the shot. An earlier parry returns it and heals its catcher. Neither operation
requires the later rebound to be admitted. No failed query becomes a miss.

The real-app witness uses H's authored 80/40 pair in both directions. Its
fixture adds a far support wall and a local gravity zone. Both portal backs
keep their authored hosts. The projectile bounces before root transit, keeps
its root material, emits the cue at the far contact and retreats on the next
tick without another bounce. The world response still ends current-tick
travel. This is a headless witness; it is not a new GPU capture.

## Validation

On `10f8eaf95`, all 434 owner cases pass: 359 shared-runtime, 46 projectile
model and 29 projectile-combat cases. All 38 runtime projectile-collision,
19 filtered projectile/held-projectile app, 73 active Smash and 35 workspace
policy cases pass. Six Smash probes remain ignored. The assembled app check
and the default all-target warning check for app/shared-runtime/projectile
model/projectile-combat pass.

After authored-command merge `eff565e6d`, all 434 owner and 19 app cases pass
again. The assembled app and default all-target warning checks also pass.
Runtime, Smash and policy were not repeated after that merge.

```bash
cargo test -j6 -p ambition_platformer2d_shared_tangle -p ambition_projectiles -p ambition_projectile_combat --lib -- --test-threads=6
cargo test -j6 -p ambition_platformer2d_runtime --lib -- projectile_tests::collision --test-threads=6
cargo test -j6 -p ambition_app --test app_it -- projectile_portal_transit --test-threads=6
cargo check -j6 -p ambition_app
cargo test -j6 -p ambition_demo_smash_app -- --test-threads=6
cargo test -j6 -p ambition_workspace_policy -- --test-threads=6
python3 scripts/checks/check_no_warnings.py -p ambition_app -p ambition_platformer2d_shared_tangle -p ambition_projectiles -p ambition_projectile_combat
```

The exact commands and terminal results are in
`target/portal-world-response/gates.json`. The source stayed fixed during those
commands. `merged-gates.json` records the post-merge checks. The logs use the same directory. `app-bounce-first.log` records the
focused two-direction app witness before the final gate batch.

No authored map or art changed. This packet does not prove moving target hurt
CCD, general portal continuation, every moving-support response, the complete
numerical fault effects, feature union and full workspace acceptance or device and
performance gates. Those requirements remain open in the PSIM plan.

## Timing merge

After room-hitstop merge `a0568d91b`, all 434 owner, 38 runtime projectile and
19 filtered projectile app cases pass again. Both 80/40 slice cases pass,
including partial support, persistent scale and parity, reverse travel and
rewind/replay. Both room-freeze cases pass. The assembled app check and the
default all-target warning check also pass. The source stayed fixed during
this run. `target/portal-world-response/hitstop-gates.json` records its exact
commands and terminal results. These two additional app filters were used:

```bash
cargo test -j6 -p ambition_app --test app_it -- portal_similarity_slice --test-threads=2
cargo test -j6 -p ambition_app --test app_it -- a_hit_in_one_room_does_not_freeze_the_other --test-threads=2
```

Smash and policy were not repeated after this timing merge. Full PSIM,
feature union, device and performance acceptance remain open.
