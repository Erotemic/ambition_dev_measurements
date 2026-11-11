# Projectile motion after a root crossing

The source correction is `cad13e2eb`. Projectiles now continue through the
unused part of the tick after an admitted root crossing. Each motion leg uses
its own world-time interval and canonical root material. A contact can stop
the leg before the crossing. An accepted crossing maps pose, velocity, material
and carried acceleration through the common portal transfer operation.

Lifetime and the force kick occur once before the leg loop. The loop retains
the tick's one-target contact budget. Reflection and world bounce still end the
tick. A rejected leg keeps its previous admitted body; it does not publish a
false miss or repeat an old sweep. Root crossings use the existing hop budget.
A two-hop budget preserves the second admitted root when the third refuses.

Generic transit and projectile motion use the same prepared body transfer and
commit. The common commit also updates backside, straddle, cooldown, remap
provenance and the transit event. Generic carried-run preparation remains in
its existing movement owner. This packet does not add another geometry solver.
The old projectile admission wrapper is removed; the leg admits payloads in
the same source cells used by recipient contact. World contact uses the same
material-cast implementation. No runtime speed comparison is claimed.

`SampledPieceHit.toi` stays local to the leg. Its `world_time` retains the
fraction of the whole tick. `HitContact.time` uses that world time. The last
motion record retains its departure material and its own interval; it does not
join the entry and exit positions with a line through the room.

## Witnesses

H's authored 80/40 pair is checked in both directions. A fast shot reaches a
real held raptor beyond the exit in the same tick as root crossing. The actor
loses one HP. Launch speed follows the chart ratio and feel stays dimensionless.
The contact time follows the crossing time within the whole tick.

The surviving-shot witness covers both H directions and L's authored moving
exit. It checks the unused displacement from the accepted exit pose and mapped
velocity, one root event, persistent material, one acceleration map, one force
kick and one age advance. L's nonzero surface velocity is part of the check.

J's authored chain carries a shot through three root crossings in one tick.
It finishes at quarter scale with ordered crossing times. Its real enemy firer
is the target beside the exit, so ordinary faction and owner rules exclude that
recipient. With a two-hop budget, the same shot retains the second admitted
root and ages once. All portal backs retain their authored walls.

A returning shot contacts a body before H, crosses, then passes a second body
on the far side in the same tick. Only the first receives damage. Root rebasing
does not reset the existing one-contact-per-tick rule.

The portal runtime fixture now publishes body geometry and apertures before
motion. Its old composition ran transit late but lacked the admission snapshot.
The production path already supplies that snapshot. Typed refusal stays intact.

## Validation

### Source checkpoint `cad13e2eb`

All commands below passed. The source tree stayed fixed during each batch.

```bash
cargo test -j6 -p ambition_platformer2d_core -p ambition_combat -p ambition_portal2d -p ambition_projectiles -p ambition_projectile_combat --lib -- --test-threads=6
```

Result: 823 passed, 3 ignored; 881 passed, 1 ignored; 141 passed, 1 ignored; 29 passed, 0 ignored; 46 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_platformer2d_runtime --features portal --lib -- portal_ --test-threads=6
```

Result: 14 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_platformer2d_runtime --features portal --lib -- projectile_tests::collision --test-threads=6
```

Result: 42 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- projectile_portal_transit --test-threads=6
```

Result: 23 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- portal_similarity_slice --test-threads=2
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
python3 scripts/checks/check_no_warnings.py -p ambition_projectile_combat
```

Result: exit 0.

```bash
python3 scripts/checks/check_no_warnings.py -p ambition_app -p ambition_platformer2d_core -p ambition_combat -p ambition_portal2d -p ambition_projectiles -p ambition_projectile_combat
```

Result: exit 0.


### Merge checkpoint `b0e5bd404`

All commands below passed. The source tree stayed fixed during each batch.

```bash
cargo test -j6 -p ambition_combat -p ambition_portal2d -p ambition_projectile_combat --lib -- --test-threads=6
```

Result: 824 passed, 3 ignored; 141 passed, 1 ignored; 29 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_platformer2d_runtime --features portal --lib -- portal_ --test-threads=6
```

Result: 14 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_platformer2d_runtime --features portal --lib -- projectile_tests::collision --test-threads=6
```

Result: 42 passed, 0 ignored.

```bash
cargo check -j6 -p ambition_app
```

Result: exit 0.

```bash
cargo test -j6 -p ambition_app --test app_it -- projectile_portal_transit --test-threads=6
```

Result: 23 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- portal_similarity_slice --test-threads=2
```

Result: 2 passed, 0 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- a_hit_in_one_room_does_not_freeze_the_other --test-threads=2
```

Result: 2 passed, 0 ignored.

```bash
python3 scripts/checks/check_no_warnings.py -p ambition_projectile_combat
```

Result: exit 0.

```bash
python3 scripts/checks/check_no_warnings.py -p ambition_app -p ambition_platformer2d_core -p ambition_combat -p ambition_portal2d -p ambition_projectiles -p ambition_projectile_combat
```

Result: exit 0.


The runtime filters overlap. Their counts are not a count of unique tests.
The merged batch did not repeat Smash or workspace policy. Those passed on
the source checkpoint before the room timing merge. These commands do not
prove full workspace or feature-union acceptance.


Commands and terminal results are in
`target/portal-root-continuation/gates.json`. The tree stayed fixed during the
final run. `gates-before-fixture-fix.json` records the earlier runtime failure;
`runtime-portals-fix.log` records the focused correction. This is a headless
motion and renderer-transform witness, not a new GPU capture.

No authored map or art changed. General kernel and other free-body motion
continuation remain open. This packet also does not close moving-target hurt
CCD, guard geometry, attachment release, the full numeric/fault-effect law,
feature-union/workspace acceptance or device and performance gates. Full PSIM
acceptance remains open.
