# The per-build cost never rose

**2026-09-01.** Re-running the three-arm decomposition with the census's startup
window excluded retires the campaign's last open mystery: **there was no
mystery.** Every term matches the shape predicted from its loop bounds.

## The decomposition, 6000-tick runs

```text
term            65       130   growth   predicted   share of growth
builds+use  0.1183    0.2314    1.96x       1.97x        63%
scan        0.0100    0.0416    4.16x       4.03x        17%
fixed       0.0437    0.0797    1.82x       2.00x        20%

per-build cost   125 ns  ->  124 ns
total Decide slope 65 -> 130:  1.04
```

`builds+use` is predicted at `n × kept(n)` — 65×14.6 → 130×14.4 = ×1.97. Measured
×1.96. The scan is predicted at `n × (n-1)` — ×4.03. Measured ×4.16.

## ⛔⛔ WHAT THIS RETIRES

Three earlier entries reported that **per-`PerceivedActor` construction cost
rises with population** — 86 ns → 160 ns, later 10.4 µs → 23.9 µs per build —
and built a hypothesis on it: that gather locality degrades as the source array
grows, at constant gather count. It was described as "the leading explanation",
then narrowed when the struct widths turned out to fit L1.

**It was an artifact of the startup census window.** A 1200-tick run at 65 bodies
produces fewer windows than one at 130, so the `0.000` startup row was a LARGER
share of the low-population mean — which manufactured exactly the signature of a
per-unit cost that rises with n. Sampled cleanly, per-build cost is **125 ns at
65 bodies and 124 ns at 130.** Flat.

⇒ The gather-locality hypothesis is withdrawn. There is nothing left for it to
explain.

## The model that now fits, with nothing left over

```text
builds  =  n × kept(n)      kept saturates near 14, so asymptotically LINEAR
scan    =  n × (n-1)        exactly quadratic, 17% of growth at 130
fixed   =  linear in n
```

And it reconciles the two slopes that looked inconsistent:

```text
9 -> 130   Decide slope 1.27    kept still RISING (≈5 at n=9, ≈14.6 at n=65)
65 -> 130  Decide slope 1.04    kept SATURATED, so builds are linear
```

The curve is superlinear only where `kept` is still growing. Past saturation the
phase is linear plus a small quadratic scan.

## What is still worth doing, and what is not

⭐ **The spatial index is the only remaining structural term** — the scan is
exactly `n²`, confirmed by prediction twice, and it is 17% of the growth at 130
and rising. Its share compounds while everything else's shrinks.

⛔ **Nothing else in `Decide` needs work.** The construction is linear and its
per-unit cost is constant, so a narrower `PerceivedActor`, a cheaper identity, or
better gather locality would each buy a proportional slice of a linear term — not
a shape change. The struct-width ratchet stays as a guard against regression, not
as a lead.

⚠ And the honest note: this is the third time this campaign that a number
survived several rounds of analysis before its MEASUREMENT was checked rather
than its INTERPRETATION. The interpretations were careful each time. The sampling
was not.
