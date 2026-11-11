# Physical charge births — 2026-11-11

Source: `b8788d097`. Merge: `ef56f8599`.

Charge births use the body's material hand and canonical box fallback.
Neutral aim follows physical facing. Explicit aim keeps its reference frame.
Hand shots and charge shots share physical feet-plane clearance. The body
maps the gap; the independent shot keeps its authored root extents.

A failed birth stays a typed numeric fault. It spends no meter or cooldown,
emits no shot, and does not become a missing-hand fallback. The charge view
omits a refused birth. Hand-muzzle clearance precedes firing state changes.
The old duplicate hand-offset helper is removed.

## Checks

- Combat: 828 pass, three ignored. Actor: 600 pass. Projectile combat: 30 pass,
  including refusal without resource, cooldown or spawn changes.
- Real app: three robot checks pass, two probes ignored. Half-scale reflected
  charge and release share a position; neutral fire follows reflected facing.
- Runtime projectile checks: 54 pass. One initial sideways-fixture failure
  expected a rotated pose but set only acceleration. The fixture now states
  physical material too; the assertion stays in place.
- Smash: 73 pass, six ignored. Policy: 35 pass. App and default-feature
  all-target warning checks pass. The merged app check also passes.

The [command manifest](portal-charge-birth-20261111.json) records scope and
results. Local logs use `target/portal-charge-gates/`. The runtime rerun
replaced its initial log; the manifest retains the initial failure status.
These runs are validation, not benchmarks.

## Open scope

Birth positions are in the shooter's root chart. Selecting and admitting the
far-side chart for a hand through an aperture remains open. This change does
not close held-object shape/scale/orientation, free-item collision, general
motion continuation or the other PSIM work. No full workspace, exhaustive
feature-union, new GPU capture, device or performance gate ran here.
