# Desktop preparation: calculex captures and aivm-2404 measurements

The desktop inputs are from `calculex`, machine
`087907b3a9744438bc0fefc2137d6da3`, i7-7700HQ, Intel HD Graphics 630.
The VM measurements are from `aivm-2404`, machine
`716a275cbdcb4fe3889ae543368500f7`, i7-7700HQ, six logical CPUs,
Linux 6.8.0-147-generic. Rendering uses llvmpipe, LLVM 20.1.2.
These VM measurements are not desktop frame budgets.

## Inputs and limits

Both desktop captures name engine commit `cd20bb47f057`:

- `desktop-timeline-run-20261118T045103Z` requested Tracy, but the capture tool
  stayed at `Connecting to 127.0.0.1:8086...`. It produced no trace or zone data.
- `desktop-timeline-run-20261118T052207Z` disabled Tracy. Its sampled cycle
  share for `sprite-sheet-wa` is 16.82%. Its worst frame census value is
  531.8 ms. One arrival window contains 456 images and 195.7 MP of pixels.

Both use `Auto` frame limiting and `Fifo` presentation. Their routes and
capture lengths differ. Do not compare them as a Tracy overhead experiment.
Both stop individual spike logging after 60 entries. A reported zero after
that cap cannot prove that later play had no spikes. Schedule phase attribution
also reports `render_blocking`; it cannot isolate CPU work in these captures.

## Changes

| Commit | Work removed | Verification |
| --- | --- | --- |
| `e90558386e` | File opens restarted sprite publication scans. Access-only reports now stop before the scan queue. | Ten publication reads caused ten scans before the change and zero after it. Both versions detect a write. The real reload fixture uses a published library. |
| `6a338f31ef` | Each room snapshot copied and hashed the complete character catalog. The existing `PreparedValue` owner now shares its data and digest. | Retained-service identity tests and catalog construction tests. The construction digest domain advances to version 2. |
| `810cba0e0b` | Tween bounds sampled total travel. They now use the conservative chord error bound `acceleration * interval_length² / 8`. | Combined translation, rotation, mirrored scale, and the published clipping corpus. |
| `4154964107` | Camera targets allocated, copied, and uploaded blank CPU pixels. They now use `Image::new_uninit`. | Offscreen GPU capture and target readback. Capture retains `COPY_SRC`. |
| `e86ee7b4b0` | Quality realization decoded tables already checked at publication. The immutable generation now retains all admitted tables. | Quality selection still works after its metadata source is removed. The mutable decode cache, fallback decoder, and three warm-up workers are removed. |
| `0772afb205` | Landmark reuse compared the total row count. A removed full table could survive in the projection when a quality table replaced its row. | Replacing a full table with a quality table retires its landmarks. A retained old generation keeps its own projection. |

## Repeated VM measurements

Raw receipts and probe sources are local under
`profiles/desktop-efficiency-vm-20261118T061609Z`.
`metadata.json` identifies the host, code, catalog input, and publication hash.
`results.json` contains every sample. The selected publication hash is unchanged
across the measurements.

Five before/after process pairs alternate order. The geometry probe alternates
both function bodies in one process after a warm-up pair. Preparation of its
461 inputs is outside the timer. The tier result is the median of five process
medians; each process median excludes its first pass and uses five later passes.
The initial one-shot probes are superseded by these alternating measurements.

| Measured work | Before median | After median | Ratio |
| --- | ---: | ---: | ---: |
| 100 copies of the 540-character catalog | 76.013 ms | 0.001 ms | Shared data replaces deep copies |
| 10 retained construction snapshots and digests, including the first digest | 808.645 ms | 91.217 ms | 8.87x |
| Tween envelopes for 461 full-resolution part tables | 50.784 ms | 14.445 ms | 3.52x |
| 1,383 quality selections across Half, Quarter, and Potato | 418.965 ms | 19.035 ms | 22.01x |

The catalog probe uses the exact catalog text at `cd20bb47f057`. Its driver is
optimized, but the linked workspace libraries use the development profile.
The tier probe also uses development-profile libraries. The geometry probe
compiles the production function bodies with `rustc -O`; its color helpers do
not affect geometry. These are isolated work measurements, not whole frames.

Catalog snapshot samples range from 796.717 to 1,037.670 ms before, and
84.273 to 153.456 ms after. Geometry samples range from 49.136 to 51.908 ms
before, and 13.784 to 15.105 ms after. Tier process medians range from
358.294 to 496.281 ms before, and 18.475 to 19.502 ms after.

Tier-probe preparation remains about 9.4 seconds. Its process peak RSS rises
from 287.6 to 300.8 MiB: a 13.3 MiB increase while the generation retains its
quality metadata. This measurement does not establish the steady memory cost
of a running game. A generation releases its tables when its last reader leaves.

The input desktop capture has only about 1.04 MB of CPU target pixels at its
peak. Target allocation is therefore an additional efficiency correction,
not evidence that target uploads caused its largest spikes. Real image decode
and upload work still uses the existing asset readiness and upload budget.

## Runtime verification

The assembled `headless` and `capture_scene` tools build from the merged engine.
The hub runs 600 active simulation ticks with 18 bodies. The town runs
300 active ticks with seven bodies. These use the real simulation harness;
they are not a measurement of the windowed host's frame budget.

The Low-quality 640x360 hub capture succeeds on llvmpipe. The inspected image
contains the robot, world, and HUD. GPU readback also saves the pause and room
field targets. The raw bundle contains the image, art snapshot, target manifest,
and logs. A second capture drives the body-warp presentation. Its GPU readback
contains nonempty 288x288 and 576x576 impostor targets and their
unpremultiplication targets. The inspected atlas contains the body pixels.
No generated image is committed.

The selected library run passes 1,607 tests. The three reload targets pass five
tests. After the landmark correction, the complete sprite library passes
150 tests, with two existing ignored probes. The Smash app passes 18 unit tests
and 56 integration tests, with six existing ignored tests.

The workspace policy run passes 33 tests and fails `engine_policies` and
`game_policies`. Its findings concern existing dependency allowlists, optional
character authority, direct pose/velocity writes, and a render reference to
`ControlledSubject`. The reported source lines are present in `cd20bb47f057`.
Seven reported files are byte-identical to that baseline. The runtime manifest
has later port additions, but its reported camera dependency also predates this
work. The full verdict is in `validation-policy-and-smash.log`.

The final merge `2336b87802` also includes incoming native hit reception work.
`cargo check -p ambition_app` passes on that merge. The combat library passes
842 tests, with three existing ignored tests. The damage library passes
52 tests. The runtime captures and timings identify `0772afb205` in their
metadata; the final assembled check covers the later merge.

This lane does not cover the complete workspace matrix, all opt-in features,
doctests, external consumers, Android, web, or a new desktop hardware capture.

## Open

Measure the desktop frame tail on the same route without Tracy. Use the complete
frame census; the capped individual spike list is insufficient. Confirm the
Tracy client connection before relying on zone data. The changes remove measured
repeated work, but they do not prove that every desktop spike is eliminated.
Resolve the existing workspace policy findings through their domain owners.
