# The slab sweep halves Integrate

**2026-09-01.** `WorldPrepSet::Integrate` goes **0.477 → 0.223 ms/tick** at 130
bodies, a **53%** cut, by replacing parry2d's generic convex shape-cast with the
closed-form swept-AABB test it was approximating.

## The measurement

One binary, both arms selected at runtime, interleaved, `hall_of_characters`
headless at 130 bodies, 1500 ticks, last four census samples averaged:

```text
rep   parry    slab
 1    0.4745   0.2243
 2    0.4795   0.2220
 3    0.4782   0.2233
mean  0.4774   0.2232      -53.2%
```

Variance under 1% across reps. End-to-end, a 1200-tick headless run went
**3.26 s → 2.82 s** (−13%, three interleaved reps) — smaller than the phase cut
because most of that wall clock is population-independent startup and asset work.

⭐ **ONE BINARY, TWO ARMS.** Every earlier before/after in this campaign compared
two builds, and one of them quietly compared a number against a baseline taken
two fixes earlier. A runtime switch removes that whole class of error; it was
deleted before commit.

Smaller drops elsewhere in the same runs — `Decide` 0.248 → 0.219, `Combat`
0.088 → 0.076 — because line-of-fire and hit resolution reach the same helper.

## What changed

`AabbExt::sweep_hit` built two `parry2d::Cuboid`s at `Pose::translation` poses —
**no rotation anywhere in this path** — and called `query::cast_shapes`, which
dispatches to an iterative support-mapping GJK. For an axis-aligned box swept
against an axis-aligned box the answer is the slab method: expand the static box
by the moving box's half-extents, sweep the centre as a point, take the latest
entry against the earliest exit. Two axes, no iteration, no dispatch.

## ⛔ The fast path declines the penetrating start

An overlapping pair needs parry's `compute_impact_geometry_on_penetration`
geometry, which is a minimum-translation question rather than a sweep.
Reimplementing that in the movement kernel from guesswork is the trade this
repository does not make, so `slab_sweep` returns `None` there and `parry_sweep`
runs — rare in practice, identical when it happens.

`parry_sweep` therefore is **not dead code**, and it is also the oracle.

## The guard

`the_closed_form_matches_the_general_solver_on_separated_boxes` sweeps 40,000
deterministic pseudo-random configurations, keeps the >20,000 separated pairs,
and requires the two solvers to agree on time of impact and on the contact
normal. Premise guards assert the generator actually produced separated pairs and
that over 1,000 of them were hits — otherwise agreement is two `None`s.

Two poisons, both caught:

```text
normal sign flipped                          -> "normal points the other way"
forgot to expand by the moving half-extents  -> "one solver saw a hit and the other did not"
```

⚠ **THE NORMAL IS COMPARED AS THE MOVEMENT KERNEL CONSUMES IT** — which axis is
cancelled, and in which direction — not component-wise. On a near-flush contact
parry's GJK converges to a corner and returns `(-0.0016, 0.9999986)` where the
exact normal is `(0, 1)`. Demanding component equality would assert that the
closed form reproduces the general solver's numerical noise, which is backwards.

⭐ The differential test earned its keep twice on the first run: `normal1` is the
**moving** shape's outward face normal, so it points ALONG the motion, and I had
it inverted. That would not have panicked. It would have slid bodies along the
wrong face.

## Suites

`ambition_geometry` 57/57 · `ambition_platformer2d_core` 536/536 ·
`ambition_platformer2d_actor_monolith` 1206/1206 · `ambition_app` 198/198.
