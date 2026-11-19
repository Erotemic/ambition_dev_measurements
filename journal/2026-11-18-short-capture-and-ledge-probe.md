# Short capture and ledge probe cost

## Short desktop capture

`desktop-autoplay-pair-20261118T225749Z` uses `ef8b2d3e98be` and a 100-second
limit per pass. The native pass completed. The Tracy pass crashed at about
87.96 seconds with status 139. Its collector log still contains only
`Connecting to 127.0.0.1:8086...`. No trace was saved. The shorter limit did not
resolve the failure. A limit below the crash time could avoid that crash but
would not establish a working connection.

The native run crosses the Hall and returns to the hub. Compare the first
visit with `desktop-autoplay-pair-20261118T204859Z`:

| Phase | Earlier capture | New capture |
| --- | --- | --- |
| First hub graph | 3.542 s | 3.651 s |
| First Hall graph | 21.978 s | 22.253 s |
| Hall graph ready to far-side goal | 7.488 s | 7.411 s |
| Far-side goal to hub return | 9.019 s | 8.955 s |

The Hall graph counts match: 81 surfaces, 1,932 links, 23,828 rollouts.
This comparison shows no clear graph build improvement from the grid change.
The revisions also differ in upstream sprite and room-prop work, so this is
not an isolated measurement of that change.

Whole-run sampled cycle shares are not a matched comparison: the old pass lasts
300 seconds and the new pass lasts 100 seconds. The new report still identifies
the shared ledge probe as a leading cost, at 2.49% of sampled cycles.

The desktop frame-phase census is marked `untrustworthy=render_blocking`.
Its schedule timings cannot establish which system owns the delay. Use the
native samples and the later headless Tracy trace for attribution.

## Ledge probe change

Implementation: `6a2193b1e4`.

The probe now checks the head-height band and side reach before tilt and
world-space body-envelope preparation. It also streams line ledges instead of
collecting a temporary vector. Candidate order, nearest-contact selection,
tilt limits, world bounds, and clearance checks are unchanged. The same probe
serves live bodies and navigation rollouts.

The optimized production-core probe has 36 cases: 1, 100, or 2,000 blocks;
down, right, or oblique gravity; both wall sides; and accepted or refused
positions. It has one nearby solid and distant remaining solids, with no chains.
Six executions alternate order across three rounds. Each execution has three
samples per case, for nine samples per version. Each sample runs
`max(100000 / block_count, 100)` queries.

Both core versions use optimization level 3. Both drivers use `rustc -O`.
All contact bits and match counts agree.

| Blocks | Accepted query speed relative to baseline | Refused query speed |
| --- | --- | --- |
| 1 | 1.11–1.20x | 1.41–1.64x |
| 100 | 1.47–1.65x | 1.94–2.16x |
| 2,000 | 1.47–1.65x | 1.93–2.07x |

Raw source, build logs, CSVs, metadata, and comparison results are in
`profiles/ledge-probe-vm-20261118T2343Z/`. These results cover the combined
probe change in synthetic block layouts. They do not measure the line iterator
change in a chain-heavy world, a complete graph build, or desktop frame time.

The accepted probe still calls `World::body_overlaps_any` twice. That helper
scans the whole block list. Indexed clearance is a further candidate. It needs
care with invalid envelopes: the clearance box subtracts two units from each
half extent, so a very small body can produce negative extents. A spatial query
must preserve or explicitly replace those overlap semantics before it can
replace the linear scan.

## Tracy tooling

Implementation: `e8c9b748ab`.

The installer used the first cached `tracy-client-sys` header. If an older
client was cached, it could select an older tool version for the current game.
It now selects the dependency version from `Cargo.lock`, then reads that
version's vendored header. Four tool tests passed, including a cache with both
old and current clients.

The locked client in this checkout vendors Tracy 0.14.0. This does not prove
which tools the desktop used. The supplied capture has no protocol-mismatch
message. Later inspection identified skipped native constructors in the desktop
binary; see `2026-11-19-tracy-native-constructor-repair.md`. The version-selection
fix is separate from that linker defect.

## Verification

All 906 core library tests passed; one existing test was ignored. This includes
the existing rotated-gravity, tilted-line, clearance, and ledge-carry tests.
The run used one compiler job, one test thread, and an optimized core.
The Smash demo check also passed: 18 unit tests and 56 integration tests, with
six existing integration tests ignored. This run preceded the linker change
and upstream integration. The VM used mold 2.30.0 for that check.
