# The work and the time move opposite ways

**2026-09-01.** The unattributed superlinear term has a name. It is not a count.
**The same construction costs 86 ns at 65 actors and 160 ns at 130.**

## The decomposition, three arms off ONE binary

`AMBITION_PROBE_ARM` selects the arm at runtime, so 65 and 130 are compared
against the same machine code. (The previous decomposition subtracted a 0.098
baseline taken from a binary two fixes older than the number it was subtracted
from. That is how the discrepancy got in.)

```text
term                     n=65     n=130   time ×   work ×   per-unit 65   per-unit 130
builds + use            0.0820   0.2990    3.65     1.97          86 ns        160 ns
scan + viewport test    0.0182   0.0293    1.61     4.03        4.38 ns       1.75 ns
no peer work at all     0.0235   0.0617    2.63
shipped total           0.1237   0.3900    3.15
```

## ⭐ The two terms move in OPPOSITE directions

```text
the SCAN     work ×4.03   time ×1.61   -> per check 4.38 ns -> 1.75 ns   FASTER
the BUILDS   work ×1.97   time ×3.65   -> per build   86 ns ->  160 ns   SLOWER
```

The scan is a streaming pass over one contiguous `Vec` that every actor reads in
the same order. Quadrupling it makes each check **cheaper** — prefetch wins, and
the quadratic count everyone reached for is the part that behaves best.

The builds do the opposite. Twice as many, each nearly twice as slow. That is not
an algorithm; **the same instructions are missing cache more often.**

## What is actually being paid for

1,873 `PerceivedActor` values per tick, each freshly `collect`ed into a per-actor
`Vec` — roughly 220 KB of allocate-write-discard every tick, touched once. At 65
actors that working set still behaves; at 130 it does not.

⇒ The lead is **allocation and locality in the view, not the size of the view**.
`integrate_sim_bodies` already keeps a `Local<Vec<_>>` scratch across bodies and
across ticks for exactly this reason, and says so in its comment. `build_world_view`
allocates a fresh `Vec` per actor per tick.

## ⛔⛔ What this retires — the third correction in one day

```text
"the O(n²) body-contact pairing"      dormant; contact_empty=true, measured
"select_actor_targets is O(n²)"       slope 1.03, measured
"Arc<str> for actor identity"         0.04 ms, measured
"the spatial index is the whole win"  0.032 ms, 8%, measured
"the superlinear term is a count"     it is per-unit COST, measured
```

Every one of these was a plausible reading of real source. **Five in a row, and
the last two were mine, published and then withdrawn.** The pattern is stable
enough to state as a rule: in this codebase, the term that grows fastest in COUNT
has never once been the term that grows fastest in TIME.

## The discrepancy is closed

`builds ×3.65` against `shipped ×3.15` and `scan ×1.61` reproduces the total. The
earlier gap — builds ×1.97 against Decide ×3.17 — came entirely from assuming
per-unit cost was constant. It is not, and that assumption was doing all the work
in the model.

## Next

Reuse a scratch buffer for the actor channel instead of allocating per actor per
tick, and re-measure per-build cost at both populations. If 160 ns falls back
toward 86 ns, the lead is confirmed and the fix is small. If it does not, the cost
is in reading the peers rather than writing the view, and the answer is a narrower
`PerceivedActor`.
