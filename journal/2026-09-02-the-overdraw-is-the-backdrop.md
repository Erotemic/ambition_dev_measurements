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
