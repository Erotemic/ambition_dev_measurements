# The desktop hitch — asset preparation landing on gameplay frames

**Host: `toothbrush`** (i9-11900K, RTX 3090 — see `hardware.md`). Headless work on
`aivm-2404`, a VM guest on the same silicon. Campaign ran 2026-08-28 → 2026-08-29.

**The question:** the mean frame was healthy and the game still felt like it
hitched when entering and playing a match.

---

## What it turned out to be

Not the simulation. The overnight headless series (3.185 → 2.866 → 2.816 ms) was
real, still stands, and **was never an answer to this question** — it measured a
single-room match whose cast loads once before the window opens.

**It is asset preparation, and specifically the EXTRACT step.** Decode is already
async on the IO pool; what lands on a frame is
`extract_render_asset<GpuImage>` — 454.9ms max against a 0.1ms mean. "Decoded" is
upstream of "ready to draw", and only the second one costs a frame.

Evidence, run `desktop-timeline-run-20260829T143608Z` (28,291 frames):

- mean 7.77ms, p50 7.54, p95 9.89, p99 12.50 — **steady state is healthy**
- 24 frames over 33.4ms, **worst 516ms**, in five clusters 1-2s wide
- every cluster lands on an image-decode burst, **monotone in megapixels**:
  +72MP → 296ms, +128MP → 162ms, +307MP → 516ms
- one character is ~7 sheets of 4096x4096 ≈ 117MP ≈ **470MB of decoded RGBA**
- whole run: 424 images, 655.9MP, 2623.7MB; resident climbed 128 → 339 and
  never fell
- the GPU is not the problem: the transparent 2D pass is ~0.047ms

⚠ **The 516ms frame was the character GALLERY, not a match.** Match entry was
162ms. The headline that launched this campaign was imprecise, and finding that
out took a bisect that should have been a read of the route.

## What was changed

Verified landed, in rough order of confidence that it mattered:

| change | why |
|---|---|
| `warm_file_root_registry()` / `warm_baked_indexes()` | an 870-sheet `OnceLock<SheetRegistry>` was built lazily **by the first punch**, ~189ms inside `advance_move_playback` |
| `demand_rostered_character_sheets` | demand was raised when a BODY spawned (`Added<ActorConfig>`) — i.e. at the opening bell. The roster knows the cast at select time |
| staging/loading split + `MAX_CHARACTERS_MATERIALIZED_PER_FRAME = 1` | one bound governed two very different costs; see "the retraction" below |
| `report_late_match_critical_art` | names a rostered fighter still resolving after the bell, with the tick, into `late_match_critical_art.csv` |
| `hit_flash` read-compare-write | an unconditional `materials.get_mut` marks the asset modified every frame, forcing a GPU re-upload |
| `RetainedHudImages` | portraits and stock icons were re-loaded rather than retained |
| `schema_fingerprint` memoised | recomputed per call |
| profile tooling writes to this repo | bundles were landing in `target/` and being pruned; `tracy_zone_instances.csv` alone was 90.6GB |

**Measured effect on hardware, run 2 vs run 1:** worst in-play frame
**516.3ms → 78.4ms**; egui errors 28,353 → 0;
`prepare_assets<HitFlashMaterial>` 312.8µs/8.87s → 80.1µs/0.94s; bundle 28G → 642M.

⚠⚠ **Three caveats that keep that from being a clean win:**
- the spike **rate** is unchanged (0.855 vs 0.848 per 1000 frames)
- run 2's route **skipped `hall_of_characters`** — the room that produced the 516ms
- the means are **not comparable**: Tracy was 13.5% of cycles in run 1 and 18.7%
  in run 2

## What was retracted, and the lesson each bought

Full versions in `profiling-lessons.md`. Kept here as the record of what this
campaign actually cost.

1. **"StateTransition is 14% of a real room's frame."** Retracted. `[census]
   phases` bills wall time between markers, so GPU blocking lands in whichever
   phase brackets it — the phase scaled with PIXELS at 16x resolution.
2. **A noise floor of "~15%"**, assumed, from which came a rule that *"no group of
   fewer than ~500 systems can produce a measurable win"* — quoted to dismiss work
   and hardened into three documents. Measured: 4.4%, then 22.6%, then 7.4%,
   three blocks within one hour.
3. **"0 late decodes."** A nested-quote `awk` error; the truth was 53 of 53.
4. **`live=1` fired on 53/53** — true and useless, because in a play-through
   gameplay is live almost always. Fixed by phase-scoping in the analysis layer
   (boot / streaming / settled).
5. **A per-player cost from one run's knockouts.** Invalid: the population went
   `4 → 3 → 2 → 1` monotonically, so the comparison was time-ordered. The data
   convicted itself — the `2` bucket cost more than the `3` bucket.
6. **A "blank render" bug report.** The bug was the `320x240` argument that had
   been typed, copied from an unrelated command.

### The retraction worth reading in full

`MAX_CHARACTERS_MATERIALIZED_PER_FRAME = 1` was set, broke
`hall_transition_cover`, and was retracted to 0 — correctly, at the time.

**The reason it broke is the interesting part.** One bound governed two halves
that cost wildly different amounts: staging a character (resolve an id, put a
string in a set) and loading one (~470MB of RGBA). Rationing both meant the CAST
arrived in a dribble, which is a *different* defect — characters demanded after
their actors spawn, in frame, uncovered — and that test exists precisely to catch
it: *"a Hall that trickled its characters in ten at a time still fails the bill."*

The fix was to split them: stage and declare everything on the frame it is
demanded, ration only the decodes. The bound is back at 1 and the test passes,
**having failed at 1 before the split**, so the guard is live rather than
theoretical.

⚠ The sweep that justified `1` (0 → 31 simultaneous decodes/1049ms; 1 → 14/222ms)
was measured on the **gallery**, which is covered by a loading foreground. It has
never shown a win on an uncovered frame. **That measurement is still owed and
needs a windowed capture.**

## What is open

**Blocked on Jon's decision:**
- **The three `opt-level = 0` pins** in the root `Cargo.toml` (`ambition_render`,
  `ambition_platformer2d_runtime`, `ambition_app`) make the dev build **42%
  slower** and buy only 1-2% of a rebuild. Justified by "render never runs in the
  headless benchmark" — a bad reason for the build you PLAY. ⛔ Do not change
  profile policy without a paired windowed dev-vs-profiling run, Tracy OFF.
- **Residency and eviction.** Resident images only ever grow. Pinned = current
  stage + selected fighters + shared; evictable = previously previewed; LRU is
  enough. ⛔ Without a budget, any speculative prewarm becomes "load the cast".
- **Arming the feature-combination checks**, which needs the 2-of-3-red gated job
  green first.

**Not blocked, not done:**
- **Separate and instrument the stages**: file IO → decode → `Assets<Image>` →
  GPU upload/prepare. The census currently reports decode completion only, and
  the cost is downstream of it.
- **Kill the re-decodes**: 30x `<runtime-generated>` (the font atlas, ~16MB a
  match) and 3-4x per `*_portraits.png`. ⚠ The HUD cache may already have taken
  the portrait half — unverified, needs a new run.
- **`enforce_session_contract`** (~292µs traced/frame) and
  **`prepare_assets<HitFlashMaterial>`** (~313µs traced/frame): ask why they recur
  every frame. Tracy inflates ~2.4x.

**Unrelated but outstanding:** the `actor → performer` rename reached the catalog
and the sprite roster but not the art FILES — `character_catalog.ron` names
`sprites/performer_spritesheet.*` while the files on disk are `actor_*`, in all
three quality tiers. Four `ambition_app` tests are red on it.

## For whoever picks this up on other hardware

1. Read `hardware.md` first, then **re-measure the noise floor on your host**
   before designing anything.
2. `toothbrush` is a fast desktop with a 3090. **Everything here that is a
   millisecond is a `toothbrush` millisecond.** The counts and the structure
   travel; the timings do not.
3. The two instruments most likely to earn their keep on a slower machine already
   exist: `late_match_critical_art.csv` (a rostered fighter still resolving after
   the bell) and `image_arrivals.csv` (the arrival rate, which predicts the
   extract spike better than any cumulative total).
4. ⛔ Do not build a new residency service. About 60% of one is live in
   `crates/ambition_platformer2d_actor_monolith/src/character_runtime/mod.rs` —
   `CharacterLoadDemand` is the demand token, `materialize_demanded_character_sheets`
   fulfils it, `converge_character_residency_to_active_quality` re-tiers it. What
   is missing is prewarm state and eviction.
