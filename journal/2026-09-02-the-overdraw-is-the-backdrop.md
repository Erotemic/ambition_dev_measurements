# The overdraw is the backdrop

**2026-09-01.** The weak-GPU plan asks to *attribute transparent screen coverage
by semantic layer before changing rendering architecture*. Attributed:
**96% of it is the parallax backdrop.**

## The measurement

`capture_scene water_world player --fit-room`, 1280x720, offscreen, with the
draw census split by render layer:

```text
total sprite area      14,564,876 world-units^2
  parallax backdrop    13,933,609            96%
  gameplay world          631,267             4%

4 backdrop sprites carry 96% of it; 57 gameplay sprites carry 4%.
```

⛔⛔ **WORLD UNITS, NOT PIXELS — AND THE CENSUS SAID SO BEFORE I MISREAD IT.**
`report_draw_census`'s own doc comment: *"WORLD UNITS, NOT PIXELS, AND THE NAME
SAYS SO. Turning these into screen pixels needs each sprite's view and that
view's projection, which is a per-view question this per-world pass has no
business answering."*

The first version of this entry divided by `1280 x 720` and reported "15.8x
coverage" and "3.8x the viewport each". **Those numbers were dimensionally
invalid** and are withdrawn. A world-space area over a pixel count is not a
multiple of anything.

⭐ What survives is the RATIO, which is what that comment endorses — *"a ratio
does not care about the unit"*. Four backdrop sprites hold 96% of the drawn area
and fifty-seven gameplay sprites hold 4%.

⚠ AND EVEN THE RATIO HAS A CAVEAT. Parallax draws on its own render layer, and if
that camera's projection differs from the gameplay camera's, equal world areas
are not equal screen areas. The split is a ratio of WORLD-SPACE area by layer;
converting it to screen coverage needs the per-view projections this pass
deliberately does not read.

## ⛔⛔ AND THE DEFAULT CAPTURE OMITS THE BACKDROP ENTIRELY

Three rooms reported `area_parallax=0` and looked like rooms with no backdrop:

```text
quality=default   sprites_visible=57   sprite_area=   631,267   area_parallax=         0
quality=ultra     sprites_visible=61   sprite_area=14,564,876   area_parallax=13,933,609
```

`spawn_parallax_layers` early-returns when the parallax budget is disabled, and a
software rasteriser seeds the **Potato** tier, which disables it. So every
`capture_scene` photograph taken without `AMBITION_QUALITY_PROFILE=ultra` is a
picture of the room **with its sky missing** — and the tool's usage text warns
about exactly this for screen EFFECTS (*"⛔ PAIR IT WITH
`AMBITION_QUALITY_PROFILE=ultra`: the Potato tier scales screen shaders to
zero"*) without mentioning that the backdrop goes the same way.

⇒ A 23x difference in measured coverage between two runs of the same room,
decided by an environment variable the command line does not mention.

## What this means for the rendering campaign

```text
cut a backdrop layer      ~24% of all drawn area, each (4 layers, 96%)
cut gameplay sprites       4% total -- there is nothing there to win
```

The lever is the backdrop's layer COUNT and its blending, not the actors. That is
also consistent with the framebuffer-scale result already measured (51.0 -> 20.1
ms p50 by capping scale and dropping MSAA): both act on fill, and fill is what
this room is spending.

⚠ **THIS DOES NOT REPLACE THE HARDWARE A/B.** `D-RASTER-3` asks for framebuffer
scale and MSAA separated on real weak-GPU hardware, and it says not to substitute
software rendering. That still stands — this is a COUNT of world-space area,
which is the same on any rasteriser, and it says WHERE the drawn area is, not
what removing it costs in milliseconds on an Iris.

## Addendum: the lever already exists, and here is what each rung buys

`ParallaxBudget::max_layers` is already tiered. Measured in `water_world`,
`--fit-room`, 1280x720, drawn area in world units:

```text
tier     layers   total area     parallax  par %  vs potato
potato        0      631,267            0     0%       1.0x
low           2    5,932,474    5,301,207    89%       9.4x
medium        3    9,717,833    9,086,566    94%      15.4x
high          4   14,564,876   13,933,609    96%      23.1x
ultra         4   14,564,876   13,933,609    96%      23.1x
```

```text
dropping the 4th layer   removes 4,847,043 of 14,564,876  = 33% of ALL drawn area
dropping to two layers   removes 8,632,402                = 59%
```

⭐ **THE LAYERS ARE NOT EQUAL, AND THE LAST ONE IS THE BIGGEST.** Steps of
5.30M / 3.79M / 4.85M — the fourth layer alone is a third of everything the room
draws. A "just drop one layer" change is not a quarter of the backdrop, it is a
third of the frame's fill.

⚠ **`high` AND `ultra` ARE IDENTICAL HERE** — both leave `max_layers: None`, so
the ladder has no parallax rung above `medium`. Whatever Ultra is for, it is not
more backdrop.

## What it does and does not tell D-RASTER-3

⭐ It prices the third variable. The row records the weak-GPU win as *"both
DPI/framebuffer cap and MSAA changed together"* — and if the treated arm also
moved the quality TIER, then parallax layer count moved with it, and the 2.54x
is three variables, not two. The knobs the row names
(`AMBITION_MAX_SCALE_FACTOR`, `AMBITION_MSAA`) are tier-independent, so this is a
question to check rather than a defect found.

⛔ It still does not replace the hardware A/B. Drawn area is a count; what a
third less fill is worth in milliseconds on an Iris is a timing, and timings on a
software rasteriser are the substitution `D-RASTER-3` forbids.
