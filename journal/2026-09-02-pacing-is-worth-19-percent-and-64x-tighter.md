# Pacing is worth 19%, and 64× tighter

**2026-09-01.** `MAX_CHARACTERS_MATERIALIZED_PER_FRAME`'s own comment said the
measurement justifying it was still owed. Part of that debt is now paid: bounding
cuts total spike time **729 → 593 ms** across the hall's load burst, and collapses
the run-to-run spread from **17% to 0.3%**.

## The sweep

`capture_scene hall_of_characters --fit-room --warmup 700`, an **offscreen GPU**
capture, so the render-world extract this bound exists to spread actually runs —
the previous sweep was headless, where it does not. One binary, arms selected by
environment, three interleaved reps each after the machine settled.

```text
bound      total spike time per burst      spread
  0 (off)     807, 699, 680  -> 729 ms      17%
  1           593, 594, 592  -> 593 ms       0.3%

  2           645, 802                       unseparated from 1
  4           653, 635                       unseparated from 1
```

⭐ **The stability is the better half of the result.** A hitch that lands
differently every run is one nobody can tell they have fixed. 593/594/592 is a
number you can regress against; 807/699/680 is not.

## ⛔ Worst-frame cannot separate these arms

The obvious statistic is the worst frame in the burst, and it is **bimodal**:

```text
bound=2   worst frame   49 ms and 275 ms on consecutive runs
bound=4   worst frame  282 ms and  34 ms on consecutive runs
```

A max over a handful of frames lands on whichever frame happened to catch the
batch. Total spike time over the whole burst integrates that away. The existing
comment's `222.3 / 393.1 / 1049.0` figures are worst-frames from single runs and
should be read with this in mind — they are why "bounding at all is what matters"
was the only conclusion drawn from them, which was the right call.

## What is still owed

⚠ This is a **software rasteriser** on a **covered** transition. The case the
bound actually exists for is an UNCOVERED frame on real hardware, and that still
needs a windowed capture. The comment now says so precisely rather than saying
"the honest measurement is owed" without saying which part.

## Two things checked rather than assumed

`take_bounded(0)` short-circuits to `take()`, so `0` genuinely means unbounded —
the comment's "SET TO 0 (UNBOUNDED)" is accurate and not a `for _ in 0..0` that
silently loads nothing.

And the constant reads `1` while a `⛔⛔ SET TO 0` paragraph sits above it, which
looks like a stale comment. `git log -L` on the line says otherwise: `1 → 0
(retracted) → 1` after the staging/loads split landed. The paragraph is preserved
history explaining a round trip, and the value is current.
