# The overdraw is the backdrop

**2026-09-01.** The weak-GPU plan asks to *attribute transparent screen coverage
by semantic layer before changing rendering architecture*. Attributed:
**96% of it is the parallax backdrop.**

## The measurement

`capture_scene water_world player --fit-room`, 1280x720, offscreen, with the
draw census split by render layer:

```text
viewport                  921,600 px
total sprite area      14,564,876 px   = 15.8x coverage
  parallax backdrop    13,933,609 px   = 15.1x    96%
  gameplay world          631,267 px   =  0.7x     4%

four backdrop layers  ->  3.8x the viewport EACH
```

⭐ **THE GAMEPLAY SPRITES DO NOT COVER THE SCREEN ONCE.** 0.7x. Every actor,
prop, projectile and effect in the room together paints less than one screen. The
transparent fill is four full-screen panels, each drawn at nearly four times the
viewport.

⚠ 15.8x is not the 5.3x the weak-GPU capture reported, and the difference is
`--fit-room`: framing the whole room makes the camera-relative panels large
relative to the capture viewport. The RATIO between layers is the finding; the
absolute multiple belongs to this framing.

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
cut a backdrop layer      ~3.8x viewport of transparent fill, each
cut gameplay sprites       0.7x total -- there is nothing there to win
```

The lever is the backdrop's layer COUNT and its blending, not the actors. That is
also consistent with the framebuffer-scale result already measured (51.0 -> 20.1
ms p50 by capping scale and dropping MSAA): both act on fill, and fill is what
this room is spending.

⚠ **THIS DOES NOT REPLACE THE HARDWARE A/B.** `D-RASTER-3` asks for framebuffer
scale and MSAA separated on real weak-GPU hardware, and it says not to substitute
software rendering. That still stands — this is a COUNT of coverage, which is the
same on any rasteriser, and it says WHERE the fill is, not what removing it costs
in milliseconds on an Iris.
