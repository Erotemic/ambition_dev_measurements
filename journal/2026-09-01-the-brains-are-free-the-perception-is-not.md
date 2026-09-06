# The brains are free; the perception is not

**2026-09-01.** Two probes decompose the hall's cognition cost completely.
130 actors think in **0.098 ms/tick**. Building what they think *about* costs
**0.76 ms** — 89% of `Decide`, 88% of the whole decision chain.

## The chain, top to bottom

`hall_of_characters`, headless, `bodies=130`, no Tracy, `profiling` build.

```text
frame            ~10.5 ms   (windowed, 2 -> 130 bodies)
 PreUpdate          83% of the growth
  WorldPrep          91% of the simulation's share
   ActorDecision     0.958 ms/tick     96% of the bucket once split correctly
    Decide           0.861             88% of the decision chain
     peer clone      ~0.38             44% of Decide
     view walk       ~0.38             44%
     everything else  0.098            11%   <- the actual brains
   Integrate         0.599
   BeforeIntegrate   0.037
   ContactDamage     0.014
```

## The two probes

Both changed behaviour and were reverted; only the TIMING was read. Baseline
`Decide` = 0.861 ms/tick.

| probe | change | `Decide` | vs `Integrate` |
|---|---|---|---|
| baseline | — | 0.861 | 1.28 |
| A | `view_peers` emptied, `peers_seen_by` STILL CALLED | 0.482 | 0.80 |
| B | `peers_seen_by` removed as well | **0.098** | 0.15 |

⭐ **A AND B WERE SPLIT DELIBERATELY.** One probe that removed both would have
proved "peers are expensive" and left the two halves unattributed. Emptying the
list while still paying for the clone separates them: `build_world_view`'s
per-peer walk is A−B's complement (~0.38 ms), and `peers_seen_by`'s clone is
A−B (~0.38 ms). They are almost exactly equal, which no source reading predicted.

⚠ **`Integrate` drifted between runs** (0.665 → 0.601 → 0.640), so the raw
millisecond column alone would be arguing from a moving baseline. The ratio
column is the second instrument, and it moves 8.4x where the raw number moves
8.8x. Two instruments agreeing is what makes this quotable.

## What it means

`tick_actor_brains` builds `build_world_view` unconditionally —
`actors/update.rs:547`, and the comment says so: "built ALWAYS for the brain's
tactical queries". `peers_seen_by` clones every other `PerceptionPeer`, each
carrying an owned `String id`; the view walk then clones each id again into a
`PerceivedActor`.

At 130 actors that is ~16.8k struct clones with a heap allocation each, per tick,
twice. The hall's cast is authored `brain_override = "stand_still"` and
`tick_stand_still` emits neutral, so **every one of those allocations is
discarded unread.**

The 0.098 ms remainder is the honest cost of 130 brains deciding, plus the self
view, projectiles and the collision-world scan. It is 0.6% of a 16.7 ms frame.

## ⛔⛔ What this FALSIFIES

**`select_actor_targets` is not the problem.** It documents itself `O(n²) in the
all-actor case` and it was the leading suspicion. Measured: **0.059 ms/tick**,
5% of the decision chain, 0.35% of the frame. A day spent optimizing the
documented quadratic would have bought nothing.

That is the second candidate this campaign has killed by measuring it rather than
reading it — the first was the O(n²) body-contact pairing, dormant in the hall.
Both were real quadratics in the source. Neither was the bug.

## What is still open

**The scaling curve.** Cloning `n-1` peers for each of `n` actors is O(n²) BY
CONSTRUCTION, so this one does not need a curve to know its shape — but the
constant matters for choosing a target, and the curve is what proves a fix
worked. The hall's cast is authored in LDtk with no population knob.

**Whether `Prepare` (0.057) and `Observe` (0.055) hide the same pattern at
smaller scale.** Together they are 0.11 ms and worth one look, not a campaign.
