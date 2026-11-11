# Guard regions under physical body material

Source: `065676df3`. The guard publisher now retains the authored rectangle
in canonical body coordinates, with the body's accepted full material map.
It uses the same canonical feet offset as hurt material. It no longer places
guards with gravity alone or replaces a turned guard with its world AABB.

The strike resolver partitions that exact region through BodyGeometry. Only
the broad phase uses bounds. Front and open-top coverage run in canonical
axes after undoing the contact path and root material. Coverage and projection
keep typed numerical refusals. A refused guard query prevents the recipient's
hit or deflect write; ranked sibling selection keeps the same rule. It does
not turn a refusal into an accepted miss. No second collision solver was added.

The local guard witness covers 80/40 and 40/80 links, both root parities,
turned root material, far-side front/back/top coverage, an empty corner in the
world AABB, and a typed material refusal. A temporary AABB replacement made
the empty-corner assertion fail. The replacement was removed before the final
batch. The earlier coverage test now states inverted physical material instead
of supplying a different gravity vector for the same material.

The app witness uses the real Shock Warden and its authored shield metadata.
With decisions held and poses fixed, half-scale reflected and double-scale
turned guards publish the body's material, omit empty bound corners, and turn
an owner-following strike without HP loss. Existing Warden behavior still
checks a normal deflect and counterattack, a strike from above, and guard break
through a change of form. The test observation helper asks for explicit world
bounds after the canonical field change.

The first app fixture aimed a world-anchored volume at the guard centre. Such
a volume originates at its own centre and does not establish a front attack.
After that correction, its defender turned when the attacker moved. The final
fixture holds decisions and poses while the real guard publisher and combat
resolver run. No production facing or damage rule was changed for the fixture.

## Validation

```bash
cargo test -j6 -p ambition_combat --lib -- --test-threads=6
```

Result: 825 passed, 3 ignored.

```bash
cargo test -j6 -p ambition_app --test app_it -- shock_warden_shield --test-threads=4
```

Result: 4 passed, 0 ignored.

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
python3 scripts/checks/check_no_warnings.py -p ambition_combat -p ambition_app
```

Result: exit 0.


The tree stayed fixed during the final batch. Stage logs and terminal results
are at `target/portal-guard-gates/`. The deliberate AABB failure is at
`target/portal-guard-poison.log`. These are headless shape and contact checks;
no new GPU or device capture was made. No authored map or art changed.

## Remaining scope

This packet establishes authored guard shape, partition and coverage. It does
not establish chart-correct deflect response velocity or all projectile guard
policies. Autonomous facing still needs a material-chart check: the actor movement
path writes controller facing directly into canonical facing. The fixed-pose
guard witness does not validate that behavior. Held-item landmark placement
and physical release still need the
common material law and custody admission. General kernel and other free-body
remaining-time continuation, moving-target hurt CCD, full numeric fault
identity/effects, performance/device gates and full PSIM acceptance remain open.
The full workspace and feature-union matrix were not run for this packet.


## Deflect response charts

Source `16d3d2ff6` keeps the meeting strike and guard maps with the deflect
message. The response no longer compares distant root positions as though
they share a chart. The two contact-frame launches use the guard's mapped
velocity as their baseline, then return through the two root maps. Portal
surface translation and spin enter the affine velocity conversion at the
chosen response position. Guard-authored speed units follow guard material;
hitstop and lockout durations do not scale. Carried run comes from the root
launch. A shared velocity change does not change the relative response.

The resolver prepares both launches before changing either body, move
playback, guard count or recoil clocks. A refused launch reports its numeric
fault and leaves those states unchanged. A contact-map refusal does not enter
the strike's hit ledger. This does not add stable fault IDs or fault effects.

The contact position remains the existing midpoint estimate of the meeting
piece centres. This change does not calculate a swept contact time or point,
add body angular-velocity reception, or complete moving-target hurt/guard CCD.
Those limits remain part of the open contact and motion work.

Three behavioral checks cover the added laws. A contact-chart matrix carries
either the strike or guard through half and double scale, both conventions,
portal translation/spin and a shared velocity change. A real strike-resolver
case checks rebound, push, clocks and one deflect per strike through scaled
portals. A refusal case checks that neither body nor guard state changes.
The existing arbitrary-gravity guard cases also pass.

### Source validation

The following commands passed on one fixed source tree:

```bash
cargo test -j6 -p ambition_combat -p ambition_characters --lib -- --test-threads=6
```

```bash
cargo check -j6 -p ambition_app
```

```bash
cargo test -j6 -p ambition_app --test app_it -- shock_warden_shield --test-threads=4
```

```bash
cargo test -j6 -p ambition_app --test app_it -- portal_similarity_slice --test-threads=2
```

```bash
cargo test -j6 -p ambition_demo_smash_app -- --test-threads=6
```

```bash
cargo test -j6 -p ambition_workspace_policy -- --test-threads=6
```

```bash
python3 scripts/checks/check_no_warnings.py -p ambition_characters -p ambition_combat -p ambition_app
```

Results: 828 combat tests passed (three ignored), 546 character tests passed,
four Warden cases passed, both reverse/replay/render slice cases passed,
73 Smash tests passed (six ignored), and 35 policy tests passed. The app and
default all-target warning checks passed. The slice runs real GGRS rewinds
and a second bit-identical scene replay; it is not a guard-CCD acceptance case.

### Merge validation

After upstream cutscene and host-authority merge `10b07aef7`, these commands
passed on one fixed tree:

```bash
cargo check -j6 -p ambition_app
```

```bash
cargo test -j6 -p ambition_app --test app_it -- shock_warden_shield --test-threads=4
```

```bash
cargo test -j6 -p ambition_app --test app_it -- rollback_schema_baseline --test-threads=4
```

Results: the app check, four Warden cases and four schema cases passed.
The broad source checks were not repeated after the merge. A later comment
edit removes the word "exact" from the contact-position description; it does
not change behavior. Logs and terminal results are at
`target/portal-guard-response-gates/`.

No full workspace, feature-union, new GPU or device gate was run. General
remaining-time continuation, other motion models, moving-target CCD and
root epochs, physical attachment placement/release, stable numeric fault
identity/effects, runtime measurements and full PSIM acceptance remain open.
