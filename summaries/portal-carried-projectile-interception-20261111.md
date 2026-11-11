# Carried projectile interception

The source correction is `d795c9268`. A parry at a carried tip now stops
at the accepted contact prefix in the source chart. The returned velocity
is resolved in the physical contact chart. Portal translation and spin use
the existing material payload law. The response is mapped back to the source
chart and admitted over all occupied material before commit.

The old response used the projectile root as its impact position and reversed
the root velocity after full attempted movement. This could place a returned
shot beyond the catching contact and put its cue at the other portal mouth.
The prepared response now carries the accepted body. Combat owner, allegiance,
trajectory and old move/weapon stamp removal commit together. The parry catch
and heal wait for admission. A numerical refusal keeps the body and combat
writes uncommitted. The projectile clock still advances. A reflected shot ends
the tick, as the projectile contact protocol requires.

## Witnesses

The real-app H witness uses the authored 80/40 pair in both directions. A
fully constructed raptor, held at small physical scale, opens a counter window.
The projectile tip meets it before root transit. Reflection changes ownership,
keeps material, stops the root in front of entry, emits its cue at the recipient
and retreats in the source chart on the next step. Absorption consumes the
shot. Each catch heals one HP and emits no ordinary damage request.

The core witness includes translation and spin on both portal surfaces. It
checks physical contact velocity, conversion of the reflected velocity back
to the source chart, and admission of the full response. A larger response
refuses with numerical overflow. It does not become a miss.

## Validation

On `d795c9268`, all 881 core and 29 projectile-combat library cases pass.
One core probe remains ignored. All 37 runtime projectile-collision cases,
18 filtered projectile/held-projectile app cases, 73 active Smash cases and
35 workspace policy cases pass. Six Smash probes remain ignored. The assembled
app check and the default all-target warning check pass.

After audio merge `fec049833`, all 29 projectile-combat and 18 filtered app
cases pass again. The assembled app and default all-target warning checks
also pass. The numerical core is unchanged by that merge. The earlier full
Smash and policy runs are not post-merge runs.

```bash
cargo test -j6 -p ambition_platformer2d_core -p ambition_projectile_combat --lib -- --test-threads=6
cargo test -j6 -p ambition_platformer2d_runtime --lib -- projectile_tests::collision --test-threads=6
cargo test -j6 -p ambition_app --test app_it -- projectile_portal_transit --test-threads=6
cargo check -j6 -p ambition_app
cargo test -j6 -p ambition_demo_smash_app -- --test-threads=6
cargo test -j6 -p ambition_workspace_policy -- --test-threads=6
python3 scripts/checks/check_no_warnings.py -p ambition_app -p ambition_platformer2d_core -p ambition_projectile_combat
```

Commands and logs are in `target/portal-parry-contact/`. The source stays
fixed during the gate run. `gates.json` records terminal exit codes for the
runtime, app, Smash, policy and warning commands. `merged-gates.json` records
the checks after audio integration. `owners.log` records the
core and projectile-combat libraries. `app-parry.log` records the focused
four app cases before the final shared-fixture comment cleanup.

No authored map, art or capture resource changed. This packet does not prove
moving-target hurt CCD, general portal remaining-time continuation, world
bounce response across a portal chart, authored guard geometry, attachment
scale/release, the full numerical fault-effect law, feature-union/workspace
acceptance or device/performance gates. Full PSIM acceptance remains open.
