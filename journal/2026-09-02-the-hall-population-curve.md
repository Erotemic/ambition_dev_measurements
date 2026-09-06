# The hall population curve

**2026-09-01.** Same room, same build, population varied 17 → 130 by
`AMBITION_ACTOR_POPULATION_CAP`. `Decide` is **superlinear at ~n^1.6**.
`Targeting` — the one that documents itself quadratic — is **linear**.
And `Integrate` is now the largest sim phase.

## The curve

`hall_of_characters`, headless, no Tracy, `profiling` build, 1200 ticks, last
three census samples averaged. Two independent repetitions; every point agrees
within **2%**.

```text
   n    Decide   µs/body   Targeting   Integrate
  17    0.0147      0.87      0.0065      0.0437
  33    0.0555      1.68      0.0142      0.1086
  65    0.1302      2.00      0.0224      0.2110
 130    0.4123      3.17      0.0532      0.6365      ms/tick
```

```text
log-log slope, 17 -> 130
  Decide      1.64
  Integrate   1.32
  Targeting   1.03
```

Cost per body nearly **quadruples** across the range — 0.87 → 3.17 µs — which is
what superlinear means in the only units that matter for a room budget.

## ⛔ It is superlinear, and it is NOT n²

The obvious story is "every actor builds a `PerceivedActor` for every other, so
n²". The measurement says 1.64, not 2.0, and the doubling ratios are not even
monotonic:

```text
  17 ->  33   population x1.94   cost x3.76
  33 ->  65   population x1.97   cost x2.35
  65 -> 130   population x2.00   cost x3.17
```

⚠ **A clean n² would give a steady x4 and it does not.** The reason is in the
code and it matters for the design: the actor channel is clipped —
`viewport.contains(p.pos)` — for every body whose perception is `Sighted`, and
only an `Omniscient` body walks the whole room. So the real shape is
**O(n × visible)**, and *visible* depends on how the cast is distributed, not on
how many there are.

That is why the ratios wobble. Capping admits the FIRST n authored placements,
so each population is a different spatial arrangement, and the visible fraction
per actor changes with it.

⇒ **Density, not count, is the independent variable.** Which is precisely the
claim `bounded-perception-and-attention.md` is built on, arrived at here from the
other direction: a fixed grid alone would not fix this, because a crowd in one
region re-creates the same walk inside one cell.

## ⛔⛔ `select_actor_targets` is linear. Measured.

Slope **1.03** over a 7.6x population range. It carries the comment

```rust
// The current implementation is O(n²) in the all-actor case.
```

and it is the reason two reviews named it as the prime suspect. At 130 bodies it
is 0.053 ms/tick — 1.9% of the frame's simulation cost — and it grows like n.

The comment is not wrong about the *worst* case; it is wrong about this one, and
nobody had checked which case the hall is. **Third candidate this campaign has
killed by measuring rather than reading**, after the O(n²) body-contact pairing
and the `Arc<str>` identity idea.

## Integrate is now the biggest phase, and it is superlinear too

```text
                 n=130
  Integrate      0.637 ms/tick     slope 1.32
  Decide         0.412             slope 1.64
```

Before the peer-snapshot fix, `Decide` was 0.85 and `Integrate` 0.66. Halving
`Decide` moved the crown. A slope of 1.32 on the movement kernel is a lead worth
one measurement — **not** a campaign, and explicitly not an argument for a
physics engine until something names the term.

## What the knob is

`AMBITION_ACTOR_POPULATION_CAP=n` admits the first n authored actor placements
and refuses the rest. Unset means no cap, at zero cost, and a test pins that
default because every uncapped number in this journal depends on it.

⛔ A capped run is **not the shipped hall**, so the cap is printed on the census
row (`actor_cap=64`) rather than left to the reader's memory of which shell set
which variable.

⚠ It caps by AUTHORED ORDER, not by distance or salience. Deterministic and
repeatable — the same cap admits the same cast every run — but a capped hall is
not a smaller hall, it is a different one, and the wobble above is partly that.

## Addendum: Integrate's slope is not algorithmic

`WorldPrepSet::Integrate` holds exactly one system, `integrate_sim_bodies`, so
there is nothing to split. A count probe asked whether anything its per-body loop
scans grows with population:

```text
cap=17   blocks=34  platforms=0  overlay_blocks=0  gate_solids=0  water=0  contact_empty=true
cap=65   blocks=34  platforms=0  overlay_blocks=0  gate_solids=0  water=0  contact_empty=true
cap=130  blocks=34  platforms=0  overlay_blocks=0  gate_solids=0  water=0  contact_empty=true
```

**Constant.** Thirty-four static blocks whatever the population. No moving
platforms, no overlay solids, no gate solids, no water. And `contact_empty=true`
at every population — which retires `BodyContactSnapshot::field_for`, the genuine
O(n²) body pairing, by **direct observation** rather than by the inference used
in the earlier entry.

⇒ Per-body cost still rises 2.57 → 4.90 µs across 17 → 130 bodies, and **no data
structure this system reads is bigger at 130 than at 17.** The superlinearity is
not an algorithm.

Two candidates remain, and this measurement does not separate them:

- **Memory system.** 130 bodies through a twelve-plus-component query with
  optional/sparse members is a different cache story from 17.
- **Density.** More bodies in the same room means more of them are genuinely in
  contact, sweeping and resolving. Work per body rises because the *situation* is
  busier, not because a list is longer.

⛔ Either way: **no broadphase, no spatial index and no physics engine addresses
this**, because there is no growing scan for them to accelerate. If it is the
second candidate it is not a defect at all — it is the room doing more.

⚠ The honest next step is an instruction-retired comparison at two populations,
not another timing. A wall-clock slope cannot tell "more cache misses per body"
from "more collisions per body".
