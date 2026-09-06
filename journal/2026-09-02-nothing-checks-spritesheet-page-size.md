# Nothing checks spritesheet page size

**2026-09-01.** The largest published sprite page is **8800×16227** — 142.8 MP,
~571 MB decoded. The only texture-dimension clamp in the repository applies to
**backgrounds**. Sprite pages are unguarded, and seven of them exceed wgpu's
*default* limit.

## The survey

Every published PNG, read from its IHDR (symlinked web copies resolved so the
same file is not counted twice):

```text
variant          files   worst side   >2048  >4096  >8192
sprites            554        16236      64     23      7
sprites_0_5x       542         8118      24      7      0
sprites_0_25x      540         4059      13      0      0
sprites_potato     536         1015       0      0      0
sprite_packs       456         2048       0      0      0
```

```text
wgpu downlevel_webgl2      2048   -> 101 published files exceed it
wgpu Limits::default()     8192   ->   7 files exceed it
typical desktop Vulkan    16384   ->   the worst page fits, by 148 pixels
```

## Two pipelines, one capped

`sprite_packs` is the ULTRAPACK path. Its tiers set `page_size` explicitly —
`full 2048`, `half 1024`, `quarter 512`, `potato 256` — and the packer clamps to
it. **Nothing exceeds 2048 there.**

The `sprites*` variants come from the older publication path, whose `page_size`
policy is loose. `noether` packs to 4096×4096; `pointed_polygon` packs to
8800×16227 in the same tier. That is not an authoring choice, it is two
characters landing on different sides of a policy nobody is asserting.

## ⛔ What this is NOT

**It is not the hall's decode cost.** The capture at 130 bodies decodes 20
images totalling **75.9 MP**, largest 14.9 MP (`giant_gnu`), and neither polygon
sheet appears. `pointed_polygon` and `pugnacious_polygon` are not in the hall's
LDtk cast at all — they are selectable characters, so the exposure is a match
that seats them, not the gallery.

⚠ I went looking for the asset hitch and found this instead. It is a **latent
portability hazard**, not the measured hitch, and the two should not be
conflated: the hitch is `extract_render_asset<GpuImage>` at ~455 ms and is still
open.

## The guard

`scripts/tests/test_sprite_page_dimensions.py` pins the four numbers per variant
as a **ratchet**: worst side, and the count over each of the three limits.
Lowering them is the improvement; a failure means a sheet got bigger, which is
the direction that ships a character a device will refuse to draw.

⭐ It reads the IHDR directly rather than importing an image library, because it
has to run where there is no Pillow. It resolves symlinks, because the web asset
tree links to the same files and following both would make the ratchet a count
of how many trees reference a file rather than how big it is.

Poisoned twice: a lowered ratchet fails with the grown dimension named, and a
survey that finds nothing fails on the premise guard rather than passing
vacuously.

⚠ The first version derived the variant from `path.parent.name` and silently
dropped `sprite_packs` entirely — the one variant that proves the packer's cap
works. Nesting one level deeper was enough to make the guard blind to its own
control.

## Also found, and not pursued

Three dangling symlinks in `game/ambition_app/web/assets/`, all pointing at
`perfect_cellular_automaton_spritesheet.3.png` in variants that now publish two
pages. Harmless — the sheet RON references only the pages that exist — but they
are stale links in a shipped tree. The other 273 broken links there are
generated audio and licensed fonts, which are expected absent, and the
repository's own symlink policy tests pass.
