# 10.7% of the process is GJK on two axis-aligned boxes

**2026-09-01.** `perf record` with call graphs, `hall_of_characters` at 130
bodies. The largest identifiable cost in the whole profile is parry2d's
**generic convex shape-cast**, called to sweep one AABB against another.

```text
integrate_velocity_clusters                        15.42%   inclusive
 └ World::first_body_sweep                         12.13%
    └ Aabb2d::sweep_hit                            11.27%
       └ parry2d cast_shapes                       10.67%
          └ DefaultQueryDispatcher::cast_shapes    10.21%
             └ cast_shapes_support_map_support_map  9.38%
                └ gjk::directional_distance         5.53%   self

tick_actor_brains                                  10.67%
 └ build_world_view                                 5.32%
```

**The movement sweep costs twice what perception construction does**, and I spent
this entire session on the perception side because that is where the phase census
pointed. The census was right that `Decide` was large; it could not say that
`Integrate`'s smaller number was made of something far more compressible.

## What `sweep_hit` actually does

`ambition_geometry::geometry::AabbExt::sweep_hit` builds two `parry2d::Cuboid`s
and calls `query::cast_shapes`. Both poses are `Pose::translation(...)` — **no
rotation, ever.** So this is an axis-aligned box swept against an axis-aligned box
along a straight line, and parry solves it with an iterative support-mapping GJK
because that is what the generic dispatcher does.

The closed form for that exact problem is the **slab method**: expand the static
box by the moving box's half-extents, sweep a point, take the max entry time and
min exit time across two axes. Roughly ten arithmetic operations, no iteration, no
dispatch.

## Why this was invisible

The phase census attributes to SETS, and `WorldPrepSet::Integrate` holds exactly
one system. It reported 0.637 ms/tick and there was nothing to split — so the
question "what is it made of" needed a symbol profiler, not a finer boundary.
Every measurement before this one asked *where*, and `perf` was the first to ask
*what*.

⚠ It also explains the earlier open discrepancy honestly: `Integrate`'s slope of
1.32 comes from more bodies sweeping against more bodies, and each sweep is
enormously more expensive than it needs to be. The constant is the compressible
part, not the shape.

## Scale

At 130 bodies, `Integrate` is 0.637 ms/tick of a ~1.3 ms simulation. If parry's
share of `sweep_hit` transfers, the closed form is worth a large fraction of that
— but **nothing here has been optimised yet, and no saving is claimed.** The next
entry either reports a measured win or reports that the fast path did not agree
with parry and was abandoned.

## The contract a replacement must keep

```text
time_of_impact   fraction of `delta` in [0,1]
normal1          parry's contact normal on the MOVING shape
penetration      stop_at_penetration + compute_impact_geometry_on_penetration:
                 an overlapping start returns toi 0 with real impact geometry
```

Plus the strict-touching rejection the wrapper already applies afterwards, which
is Ambition's own platformer semantics and not parry's: resting on a floor must
not block horizontal motion, sliding a wall must not block vertical motion.

⛔ Movement is the most safety-critical code in the engine. A replacement gets a
randomised differential test against the parry path before it is switched on, and
the overlapping-start case falls back to parry rather than reimplementing
minimum-translation geometry from guesswork.
