# The scan is the problem, not the view

**2026-09-01.** Each hall actor is offered 129 peers and keeps **14.4**. That
number does not change when the room holds 65 instead of 130.

## The measurement

A count probe inside `build_world_view`, `hall_of_characters`, headless:

```text
population   offered   kept   kept_frac   omniscient
     65        65.0    14.6      0.225        0.000
    130       129.0    14.4      0.112        0.000
```

`extent = [480, 320]` — the `Sighted` tactical extent, and **no body in the hall
is `Omniscient`**, so the viewport filter is live for every actor.

## What it means

**The view is already bounded. The scan is not.**

```text
kept per actor      ~14.4   CONSTANT in population   -> the work is O(n)
offered per actor    n - 1   GROWS with population    -> the scan is O(n²)
```

At 130 bodies, **89% of every actor's scan is discarded**. The room is large
enough that a body's tactical extent contains about fourteen others whether the
cast is 65 or 130 — so the expensive per-peer construction is already
population-independent, and the superlinear term measured earlier (`Decide`,
slope 1.64) is the **scan over candidates that will be thrown away**.

## ⛔ This changes what to build first

`bounded-perception-and-attention.md` proposes three things. This measurement
prices them, and they are not equal:

```text
attention budget (top-K exact)   the viewport ALREADY delivers ~14, which is the
                                 K the design was going to impose. Nothing to win
                                 in the hall today.

crowd aggregation                only earns its keep once `kept` itself grows,
                                 i.e. under real crowding. Not the hall.

spatial index                    THE WHOLE WIN. Take the scan from 129 to the
                                 occupants of nearby cells and the O(n²) term is
                                 gone, with the output unchanged at ~14.
```

⭐ **AND IT VALIDATES THE DESIGN'S CENTRAL CLAIM FROM THE OTHER SIDE.** The doc
argues density, not count, is the independent variable. Here is that in one
column: population doubled, `kept` did not move. What sets the work is how many
bodies share a viewport, and the hall's answer is fourteen.

⚠ It also names the condition under which the other two pieces become necessary:
when `kept` starts rising with population, the room has become dense enough that
the viewport is no longer a bound. The probe that measured this is the one to
re-run then — `kept_frac` climbing back toward 1.0 is the tell.

## Still not measured

Which part of the remaining `Decide` is the scan and which is the ~14 builds. The
inference above rests on two measured quantities (constant `kept`, growing
`offered`) plus the measured slope, not on a decomposition. An instruction-retired
comparison at two populations would settle it.
