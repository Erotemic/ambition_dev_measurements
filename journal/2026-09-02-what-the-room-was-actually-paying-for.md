# What the room was actually paying for

**2026-09-01.** The campaign had cut the hall's decision phase from 0.852 to
0.234 ms/tick without ever answering the question underneath it: *what is that
work FOR?* Three arms of one room answered it in an afternoon, and two of the
three things I believed on the way there were wrong.

## The measurement

Same room, same build, same host. Three interleaved reps per arm, medians, 3000
ticks. The only variable is which brain all 129 authored NPCs get.

```text
phase                          statues   brutes    smash    smash Δ
WorldPrep.Decision.Decide        0.340    0.730    0.377      +11%
WorldPrep.Integrate              0.253    0.332    0.252        0%
Combat                           0.115    0.232    0.117       +2%
WorldPrep.Decision.Targeting     0.034    0.036    0.033       -3%
```

- **statues** — the authored cast. All 129 are `brain_override: "stand_still"`.
- **brutes** — `ambition::melee_brute_striker`: acts, but reads no world view.
- **smash** — the `ambition::medium_striker` profile, `template: Smash`, one of
  the only two arms that CONSUME a world view.

**Building the views is the cost.** The peer-independent remainder is 0.039, so
88% of the statues' 0.340 is constructing peer lists, views and memory.

**Reading them is nearly free.** 129 brains that genuinely consume a 129-actor
view cost 11% more and move nothing else.

**Acting is what costs.** The brutes read nothing and cost 115%, with
`Integrate` +31% and `Combat` +102% behind them.

And `Targeting` — the one quadratic anybody found — is 0.033-0.036 across all
three arms. A busy room does not spend its time searching.

## The knobs this needed, and the trap in both

`AMBITION_ACTOR_BRAIN_OVERRIDE` forces a preset; `AMBITION_ACTOR_BRAIN_PROFILE`
forces an autonomous profile. Two knobs and not one because **the preset road
cannot reach a perception-reading brain**: every preset the catalog names lowers
to an arm `tick_simple_state_machine` answers, and that function takes no
`WorldView` argument at all. `Fighter` and `Smash` are reachable only through a
profile.

Both resolve against **each character's own provider**, and the hall is a
cross-provider gallery, so a bare name dies on the first `mary_o` character.
Qualify them.

## What the fix would buy, measured rather than argued

A throwaway probe — applied, measured, reverted, never committed — skipped view
construction and belief maintenance for brains that cannot consume a view, which
is exactly what ADR 0034's first increment does for a `None` declaration. The
perception-reading arm is the control, because the probe leaves it untouched:

```text
Decide, statues   0.340 -> 0.024 ms/tick   -93%
Decide, smash     0.377 -> 0.387            +3%   (control)
```

**0.316 ms/tick**, ~17% of the headless hall frame, and the largest sim phase
becomes a rounding error. The +3% is what licenses the −93%: without it, "we
rebuilt and the number moved" would explain the whole thing.

It also supersedes the estimated 0.039 peer-independent remainder. With
construction and belief maintenance both gone, the floor measures **0.024**.

⛔ The probe *is* the checksummed-state change, not a cheaper cousin of it — it
skips `believed_target`. The number exists so the decision is made against a
measurement, not so the change is landed quietly.

## Three things I had wrong

**The campaign measured a host that does not roll back.** `--start-room` is not
a room selector: `cli_direct_entry()` returns true for it, and `--headless`
branches on that to the direct sandbox instead of the production shared host.
The sandbox installs no rollback host — no `GgrsSchedule` among 20 schedules —
and the bundle's own stderr had been saying so on line 24 the whole time. The
numbers are valid for the simulation systems, which is where the work happened.
They are not a frame budget.

**Then I costed that discovery wrong.** I read "zero-distance `SyncTestSession`"
and concluded the shipped host saves and checksums every registered component
every frame, at 130 bodies. ggrs skips the entire save path at
`check_distance: 0` — its own comment says *"we can skip all the saving"* — and
that is what a local session runs at. The type named a capability; the parameter
decided the cost. Four lines of a dependency I never opened.

**And the obvious optimization does not exist.** `world_view` is a local, not a
component, so skipping its construction for a brain that cannot receive one
looks free. It is not: the view is consumed twice, and the first consumer is
`believed_target`, which writes `PerceptionMemory` — checksummed rollback state
— for every body whatever its brain. There is no performance-only version of
this change, which is precisely why ADR 0034 exists.

## A fourth thing I had wrong, found by review

The parent sim-phase marks were installed `.after(phase)` with no upper bound —
nineteen of them — while the actor-decision marks one level down carried the
correct reasoning in a comment I had written myself: *"EACH MARK NEEDS BOTH
EDGES. `.after(Targeting)` alone has no upper bound."* The census file's own doc
asserted the opposite, that `.after(phase)` lands a mark "exactly at that phase's
trailing edge".

Repaired and re-measured, same arm, 3 reps:

```text
@130                one-sided   bracketed   shift
Decide                 0.340      0.405     +19%
Integrate              0.253      0.298     +18%
Targeting              0.033      0.050     +52%
```

So every absolute per-phase number above is low by roughly this much, and the
smaller the phase the worse the relative error. **The A/B conclusions survive**
— both arms carried the same bias, and the three-arm table and the ceiling probe
are differences measured the same way — but nothing here should be quoted as an
absolute cost or a share of a frame.

Worse than one-sided: eleven of the phases are configured with no `.chain()` at
all, so their buckets are differences between marks nobody sequenced. A serial
partition of unordered work is fiction however carefully the marks are placed.
They are named in `SIM_PHASE_UNORDERED` and on the census row now.

## What the next person should not repeat

- Do not quote a hall number as a frame budget without checking which host
  produced it. Read line 24 of the bundle.
- Do not cost a dependency's work from its type name. Find the guard that
  decides whether it does the work and read the value it is configured with.
- Do not go looking for a free version of the perception gate. Two consumers.
- Do not build the spatial index. `Targeting` did not move across three arms
  that changed everything else about what the room was doing.
- When you write a rule down for one instrument, grep for every other instrument
  of the same kind. The correct reasoning sat one level down for a week.
- Before billing phases as a partition, check the schedule actually orders them.
