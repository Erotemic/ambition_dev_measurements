# Shared spatial grid query cost

## Desktop evidence

`desktop-autoplay-pair-20261118T204859Z` uses `dc289d119bed`. The native pass
completed its 300-second window. The first Hall graph took 21.978 seconds from
Building to Going, compared with 24.281 seconds in the older native capture and
55.690 seconds in the previous paired capture. All three graphs have 81 surfaces,
1,932 links, and 23,828 rollouts. The new run crossed the Hall, returned to the
hub, and continued through the primeval rooms.

The new result does not repeat the large slowdown in the previous capture.
These separate runs do not isolate the cause of that slowdown or establish the
frame-time effect of the cast admission change.

The Tracy pass failed again with status 139 after about 82.3 seconds. The
collector stayed at `Connecting to 127.0.0.1:8086...` and saved no trace. The
bundle has no crash stack. The cause remains unknown.

`Grid::near` accounts for 2.13% of all sampled cycles in the new native run.
It serves shared movement and navigation queries. This is the measured owner
for the next optimization.

## Change

Implementation commit: `2cd629891f`.

The shared grid now stores cell lists in a sorted table after construction.
Queries use binary search instead of tree traversal. A cached list contains
each indexed entry once, in source order. A query that uses the global fallback
copies this list instead of collecting and sorting repeated cell entries.

Exact bounds filtering, layer visibility, and contact order are unchanged.
The extra global list costs four bytes per indexed base entry, per built grid.
The current and swept grids remain separate, lazy, shared caches.

A trial hash table helped larger worlds but slowed local queries in a small
world by about 5–12%. The sorted table gave better results across the tested
sizes and was selected instead.

## VM measurement

The probe calls the production `Layered::near` API. Both core versions compile
at optimization level 3; both probe binaries compile with `rustc -O`. The
baseline is `dc289d119bed`. Six executions alternate their order across three
rounds. Each execution has three samples per case, for nine samples per version.

There are 24 cases: 100, 2,000, or 10,000 entries; query spans of 16, 128, 512,
or 100,000 units; and a plain base or a layer with every seventh entry hidden.
The grid is warm. Each local sample has 100,000 queries; each global sample has
3,000 queries. Exact bounds filtering and result traversal are included.

| Query group | Baseline time divided by new time |
| --- | --- |
| Local, 100 entries | 0.98–1.22x |
| Local, 2,000 entries | 1.11–1.23x |
| Local, 10,000 entries | 1.02–1.45x |
| Global fallback, all tested sizes | 2.09–7.37x |

All result checksums match. The smallest local query with a hidden layer was
about 2% slower, or 2.4 ns per query. These results measure warm queries in
synthetic layouts. They do not measure initial grid construction, full graph
builds, or desktop frame time.

Raw source, build logs, CSVs, metadata, and comparison results are in
`profiles/spatial-grid-vm-20261118T2206Z/`.

## Verification

The behavioral check compares indexed results with a linear scan in visible
list order. It covers local and global regions, point contacts, negative
coordinates, hidden entries, added entries, swept bounds, and unbounded geometry
in both the base and the layer.

All 906 core library tests passed; one existing test was ignored. The core used
optimization level 3, one compiler job, and one test thread.
`cargo check -p ambition_app -j 1` passed. This lane covers core behavior and app
compilation. It does not cover a rendered route, app integration tests, or the
full workspace test suite.
