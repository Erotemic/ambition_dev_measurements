# Carried projectile material and launch units

The source corrections are `a91231348` and `4825f1f1c`. They close the
pre-root projectile contact gap against sampled hurt material. General
remaining-time continuation and moving-target hurt sweeps remain open.

A projectile's physical sweep already used admitted material cells. Its
damage query swept the full root box instead. The damage query now receives
the same canonical source cells. It builds the source once per leg and passes
it to body, boss and breakable contact queries. The old unpartitioned polygon
entry is removed. Affine cells keep both sets of cuts in one native query.
Curved cells use bounded interval exclusion. Target circles stay analytic.
A failed computation remains a typed refusal.

Each source cell keeps its own conservative physical bounds. A single box
around both ends also covers empty space between portals. The recipient
query must not treat that space as occupied. The query stores the bounds
that admission already validates. World and recipient queries reuse them.
This is a derived query cache, not another collision authority. No runtime
speed comparison is claimed.

The contact prepares its launch magnitude before movement or hit commit.
Melee and projectiles share the core unit conversion. `LaunchSpeed` follows
the source-to-victim material map. `FeelScale` stays dimensionless. Body
eligibility is checked before geometry. An invalid ally cannot refuse an
otherwise valid hostile contact.

## Behavioral witnesses

The authored H pair retains widths 80 and 40. The app witness fires through
both directions. It uses a fully constructed raptor actor, at small physical
scale, held in place to isolate source motion. The actor receives one point
of damage and loses exactly one HP. Contact occurs before the projectile root
transits. The check runs with ordinary `FeelScale(0.85)` and an authored launch.
The feel value stays 0.85 at both ratios. The launch speed equals the ordinary
resolved speed times 0.5 or 2. The held actor does not prove post-contact
motion or moving-target collision.

The spinning K exit also carries a shot into sampled hurt material between
matching endpoint poses. The check observes the named contact. The core
circle check retains an analytic target and rejects the empty corner of the
swept envelope. Its affine control misses the far target without the source
partition and falsely reaches the cut-away root region. The admitted source
has the opposite answers. An occupied-start check keeps the physical contact
point inside the target.

The recipient refusal/resume check retains both a remote invalid body and
an invalid ally beside the valid hostile body. Repairing the hostile body
permits contact while both other budgets remain invalid. A core check puts
an invalid body between disconnected source cells. It is rejected by broad
phase before its partition can refuse.

## Validation and limits

The final source passes 880 core, 821 combat and 29 projectile-combat tests.
Four owner probes remain ignored. The 17 filtered projectile/held-projectile
app cases and both 80/40 slice cases pass. The slice includes exact partial
support, persistent scale/parity, reverse travel and rewind/replay. All 35
policy cases, the assembled app check, and the default all-target warning
check for app/core/combat/projectile-combat pass.

All 73 active Smash cases pass on the payload correction before the immutable
bounds reuse. Six Smash probes remain ignored. After bounds reuse, the owner,
projectile app, slice, policy and warning checks above pass. The full workspace,
feature union, device/GPU and performance gates are not covered here.

Commands and results are in `target/portal-source-contact/`: `final-gates.json`
records the first contact packet; `units-gates.json` records the payload packet;
`accepted-gates.json` records the final source with the real health witness and
bounds reuse. `merged-gates.json` records the upstream integration checks.
The exact owner and app commands are:

```bash
cargo test -j6 -p ambition_platformer2d_core -p ambition_combat -p ambition_projectile_combat --lib -- --test-threads=6
cargo test -j6 -p ambition_app --test app_it -- projectile_portal_transit --test-threads=6
cargo test -j6 -p ambition_app --test app_it -- portal_similarity_slice --test-threads=2
cargo check -j6 -p ambition_app
cargo test -j6 -p ambition_workspace_policy -- --test-threads=6
python3 scripts/checks/check_no_warnings.py -p ambition_app -p ambition_platformer2d_core -p ambition_combat -p ambition_projectile_combat
```

No authored map or art is changed. Full PSIM acceptance remains open. It still
needs general motion continuation, moving-target hurt queries, surviving
contact response checks, other motion models, guard geometry, physical
attachment scale/release, the complete bounded numeric law and fault effects,
seam-lab coverage, and device/performance acceptance.

After upstream merge `0714b76b7`, the assembled app check and all 17 filtered
projectile/held-projectile app cases pass again. This is the merged result
recorded in `merged-gates.json`.
