# Did the Bevy 0.19 port cost frames? — and the compiler that was in the profile

**Host: `toothbrush`** (i9-11900K, RTX 3090 — see `hardware.md`). 2026-08-31.

**The question, as asked:** "I'm seeing more frame dips in ambition itself than I
remember yesterday." Explicitly **not** a bisect — the ask was to move forward and
optimize.

Runs: `desktop-timeline-run-20260831T210231Z` (call it **run 1**),
`…T212248Z` (**run 2**), `…T212831Z` (**run 3**). All three at
`b20324980c65`, `profiling` + `--features profile`, working tree dirty.

---

## The answer

**The dips are the known asset-preparation hitch of
[2026-08-29](2026-08-29-the-desktop-hitch.md), not the port.** Run 1 walked into
`hall_of_characters`; runs 2 and 3 did not. Nothing else separates them.

Run 1, from `runtime_census.csv` and `image_decodes.csv`, at game time 66.35s:

```text
t=65.32   bodies=2    archetypes=1813   live=3803
t=66.35   bodies=130  archetypes=1975   live=3898     ← the cast arrives
```

and across `t=65.9 → 69.2`, **71 spritesheet decodes**. Two characters own 43% of
the whole session's decode work:

```text
115.6 MP   7 sheets   noether_spritesheet
107.7 MP   7 sheets   perfect_cellular_automaton_spritesheet
-------------------
223.3 MP of the run's 519.5 MP, from two of ~40 characters
```

Run 1's 30 over-threshold frames sit in exactly three places: startup
(game_s 1.6–2.1), a small cluster at 51.8, and **22 of the 30 inside those 3.4
seconds of hall population**, peaking at 199ms.

⚠ **This is the windowed gallery capture the 08-29 entry said was still owed** —
"that measurement is still owed and needs a windowed capture". Run 1 is one.
It does not settle the `MAX_CHARACTERS_MATERIALIZED_PER_FRAME` sweep, because the
gallery is still covered; but the hall's cost on real hardware is now on record
at 130 bodies rather than inferred.

## ⛔ THE COMPARISON THE QUESTION WANTED CANNOT BE MADE FROM THIS DATA

Spike rate per 1000 frames, which is the 08-29 entry's own metric:

| run | spikes/1000 frames | decode MP | peak bodies |
| --- | --- | --- | --- |
| `20260829T143608Z` | 0.85 | 578 | 1 |
| `20260829T171902Z` | 0.90 | 151 | 1 |
| **`20260831T210231Z`** (run 1) | **2.19** | 519 | **130** |
| `20260831T212248Z` (run 2) | 0.24 | 82 | 0 |
| `20260831T212831Z` (run 3) | 0.95 | 92 | 3 |

Run 1 is two and a half times worse than either 08-29 run on that number. **That
is not evidence of a regression, and it is not evidence against one.** No prior
bundle in this repository ever reached more than 1 body: none of them entered a
populated hall. Comparing run 1 to them compares ROUTES, not builds.

The one thing that IS comparable across all five is the worst in-play frame, and
it moved the right way: 516ms on 08-29, **199ms** here, on a heavier hall.

⛔ **A frame-dip question needs the SAME ROUTE on both builds.** The
`--scenario` / `--workload` machinery exists for exactly this and was not used.
Whoever asks this next should drive one named route twice, not two sessions.

## What DID show up, and it is an instrument defect

**All three bundles have the compiler inside the perf capture.** `perf-report-by-thread.txt`
for run 2:

```text
30.94%  ambition_game_b
16.78%  Compute Task Po
 9.11%  ld.mold
 3.88%  rustc
 2.57%  opt cgu.00
 2.26%  lto cgu.00      … and eighteen more `lto cgu.NN` / `opt cgu.NN` threads
```

In run 1 the game's own symbols do not make the top six at all — every one is
`rustc`. `frame_spikes.csv` shows the gap directly: run 1's first frame is at
**wall 274.9s**, run 2's at 36.3s, run 3's at 34.6s. That is the build.

With the classifier corrected (below) and the summaries regenerated, the size of
it is not a third — it is most of the capture:

| run | build tooling | the game itself |
| --- | --- | --- |
| run 1 | **92.4%** | 7.5% |
| run 2 | **50.6%** | 48.3% |
| run 3 | **69.7%** | 29.8% |

⛔ **Run 1 is 92% compiler.** Its "Top native symbols" list was a list of rustc's
hot functions, presented under a heading that says it ranks the game.

**Cause, found in the source rather than guessed:** `scripts/profile_desktop.sh`
warm-builds by replaying the `build_arg` list from `run_game.sh --print-plan` with
a bare `cargo build`. But `run_game.sh` exports `CARGO_INCREMENTAL=0` for every
release-level profile — deliberately, because `.cargo/config.toml`'s
`[build] incremental = true` overrides `[profile.profiling] incremental = false`.
The warm build therefore compiled an **incremental** variant, cargo rejected it
when the perf-recorded launch asked for the non-incremental one, and the whole
graph was rebuilt under `perf record`. The build ran twice and the second one was
billed to the profile.

**Fixed:** `--print-plan` now emits `build_env=` rows and the profiler applies
them. Guarded by `scripts/tests/test_the_profiler_warm_build_matches_the_launch.py`
(3 arms, poison-verified: removing the env loop turns the first one red).

⚠ **What that contaminates, and what it does not.** The
`perf-report-*` files, the "Where the native time went" table and the "Observer
effect" table in all three summaries are diluted and their percentages should not
be quoted. Everything keyed to `game_s` — `frame_times.csv`, `frame_spikes.csv`,
`runtime_census.csv`, `image_decodes.csv` — comes from the game's own stderr
census and is **unaffected**; the analysis above rests only on those.

⛔⛔ **AND THE SECTION THAT EXISTS TO FLAG THIS SAID EVERYTHING WAS FINE.** The
"Observer effect" table buckets threads by command name, and its needles were
`cargo`, `rustc`, `bash`. But a cargo build spawns one `rustc` per crate and puts
the CYCLES in the threads that fork off it: `ld.mold` for the link, and up to
`2 * jobs` LLVM threads named `lto cgu.NN` / `opt cgu.NN`. None of those match.
So run 1 printed `build tooling 12.7%` for a capture that was 92.4% compiler, and
then printed *"the profiler cost 0% of sampled cycles — low enough that the
measurements below stand on their own"*, because that sentence only ever looked
at Tracy's share.

**Fixed**, three ways, guarded by `scripts/tests/test_profile_thread_buckets.py`:
the needles now include the linker and codegen threads (taken from run 2's real
thread table, not guessed); the summary emits a loud warning whenever build
tooling out-costs the game, naming what it dilutes and what it does not; and
PipeWire's `pw-data-loop` finally lands in `audio` instead of in the game. The
three 08-31 summaries have been regenerated in place.

⚠ **Still under-reported: Tracy.** Its cost inside the game binary cannot be
separated by THREAD, and the table is a thread table — so `profiler (Tracy)`
reads 0.7% on run 2 while `TracyLayer::on_event` and its
`sort_unstable_by_key::<String>` are the top two game-binary symbols at ~2.2%
combined. Splitting that needs a symbol-level tally beside the thread one.

## What is open

**The lever, not blocked, not done:** two characters are 7 sheets of ~4096² each
and 43% of a session's decode. The 08-29 entry says not to build a new residency
service — `CharacterLoadDemand` / `materialize_demanded_character_sheets` /
`converge_character_residency_to_active_quality` are ~60% of one already, missing
prewarm and eviction. Reducing what a hall demands (fewer pages, lower tier for
gallery previews, or eviction) is upstream of all of it and is the biggest single
number on the table.

**Owed measurement:** a same-route A/B across the port, using `--workload`. Until
then "did the port cost frames" is unanswered, and this entry does not answer it.

**Not fixed:** the observer-effect table cannot see Tracy's cost inside the game
binary, because it buckets by thread and that cost is on the game's own threads.
A symbol-level tally from `perf_report.txt` beside the thread one would close it;
until then `profiler (Tracy)` is a floor, not a measurement. The build-tooling
half of the same defect IS fixed — see above.
