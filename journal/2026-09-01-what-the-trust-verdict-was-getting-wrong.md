# What the trust verdict was getting wrong

**2026-09-01.** Every bundle in `profiles/` has been regenerated; the numbers in
them did not change, the sentences about them did.

The "Observer effect" section is the one a reader consults to decide whether the
rest of a bundle can be quoted. Three separate things in it were wrong, and one
of them was wrong about a capture written specifically to prove a fix.

## `build tooling` was two different things

`cargo` and `rustc` were the same bucket. They are not the same fact:

| bundle | game | codegen | launcher | old verdict | now |
|---|---|---|---|---|---|
| `desktop-perf-run-20260901T003332Z` | 88.0% | **0.0%** | 9.4% | "compile inside the capture: 9.4%" | CLEAN, 0% codegen |
| `desktop-timeline-run-20260831T212248Z` | 48.3% | **50.5%** | 0.1% | 50.6% "build tooling" | COMPILE-CONTAMINATED |

The first row is the capture that validates the warm-build repair. Its own
thread table has **zero** compiler workers — no `rustc`, no `lto cgu.NN`, no
`ld.mold`. The 9.4% is the `cargo run` launcher waiting on a child that had
nothing to do. The summary printed `compile inside the capture: 9.4%` anyway,
which the measurement journal beside it directly contradicts.

The verdict then inferred "a compile happened" from `build_share > game_share`,
which is wrong in both directions: 70% game against 20% rustc reads CLEAN, and
88% game against 9% idle cargo reads suspicious. Only codegen dilutes a native
profile, so only codegen decides now — at a floor of 1%, below which the shift
is smaller than perf's own run-to-run spread.

## Re-running every bundle found a hole the reasoning had not

The old rule was wrong about what it MEANT but accidentally covered a case the
replacement did not. `desktop-timeline-run-20260829T020516Z` is 94.4% `cargo`
and 5.6% `bash`: **the game contributed zero samples.** Judged on the codegen
bucket alone that is 0.0% compiler and reads CLEAN — a verdict blessing a
capture with no game in it.

So the floor is now asked directly: a native profile is quotable only when the
game dominates the capture, whatever the rest of the cycles turn out to be. It
is checked LAST, so a capture whose missing game has a named cause still gets
told which one — sending a reader to `warm-build.status` beats telling them the
obvious.

*This did not come from reading the classifier. It came from running it over all
fourteen bundles and reading the column.*

## One verdict still held two incompatible sentences

Making the verdict a single value stopped the report reaching two conclusions.
It did not stop one conclusion from containing two contradictory paragraphs, and
every contaminated state printed both:

```text
⭐ Everything keyed to GAME TIME is unaffected ...
    ... a few lines later ...
every frame time, zone duration and plugin-build number here is inflated too.
```

The first was only ever true of a COMPILE, which runs beside the game and mostly
before `exec`. Tracy runs *inside* the process the census is recorded by, so it
inflates the very frame times `frame_times.csv` holds. The paragraph is
conditional now, and there is a test asserting a profiler-contaminated report
cannot contain it — plus a premise guard asserting a compile-contaminated one
still does, because "which half of this bundle survives" is the most useful
sentence in a contaminated report and deleting it is not a fix.

## The DSO table was never a breakdown

`perf report` defaults to `--children`: a sample is credited to every shared
object on its call stack. `desktop-perf-run-20260901T003332Z` therefore printed

```text
216.4%  game binary + its Rust/C deps
 22.8%  kernel
```

under a heading that reads like a partition. `scripts/profile_desktop.sh` now
passes `--no-children` to that report, so captures written from here on do
partition. Bundles that predate it say so above the table rather than letting a
reader subtract one row from another.

## What is unchanged

Every frame number, spike count and census row. This is the layer that says
whether to believe them, and it is the only layer that moved.
