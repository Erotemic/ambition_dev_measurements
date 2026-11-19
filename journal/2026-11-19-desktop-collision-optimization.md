# Desktop paired profile: movement and collision work

## Capture

The source capture is `desktop-timeline-run-20261119T025214Z` at
`b18365198531`. Both launches use the profiling Cargo profile on calculex,
an Intel Core i7-7700HQ with Intel HD Graphics 630 and Mesa Vulkan. The
100-second Tracy launch precedes a separate native launch without Tracy.

Tracy saved 100.2 seconds, 8,972,444 zones, and a 190.26 MB trace. The CSV
export and window reduction completed. Its process status is 137 after the
capture deadline; its last log reports repeated Ctrl-C during shutdown. The
bundle labels that status as a diagnostic failure, but the trace is present
and decodes. The native log spans 100.8 seconds. Neither launch shows the old
signal-11 failure or protocol mismatch.

The native actor crosses the Hall, returns through the hub, and continues into
the primeval rooms. The Hall graph takes 21.953 seconds from `Building` to
`Going`, against 22.253 seconds in the previous native capture. That difference
does not establish a performance improvement. Both graphs have 81 surfaces,
1,932 links, and 23,828 rollouts.

## Attribution

Tracy self time for body integration is 18.071 seconds across 2,583 calls,
about 7.00 ms per tick. Other simulation totals include targeting at 2.762
seconds, perception at 2.541 seconds, brain updates at 1.993 seconds, and body
contacts at 1.460 seconds. These are zone wall durations, not sampled CPU time.
The render thread's large self duration includes waiting; it is not proof of
CPU work in the renderer.

The native cycles report has about 16,000 samples. Leading functions include
the spatial grid, ledge probes, material-cell construction, polygon contact,
and body integration. Hazard path checks, the hazard gate, and rebound lookup
also appear. Inspection found that hazard and rebound queries scan every block,
including distant blocks of other kinds. This cost is paid by navigation
rollouts and by ordinary bodies through the same movement kernel.

The two launches have different startup and route timing. Their frame times
must not be combined as one run or used to estimate Tracy overhead directly.

## Implementation

Endpoint hazard and rebound queries now use the room's existing spatial index.
Swept hazard queries share the body-path candidate query used by solid sweeps.
The exact contact tests, hazard interior, grazing rule, and rebound source
priority stay the same. The query covers the full path, not only its endpoint.

A material cast now retains its source polygon for repeated contact candidates.
Unclipped material borrows that polygon instead of copying it for each contact.
Clipped material still uses the same polygon clipping operation.

An unclipped swept cell keeps its eight bound vertices on the stack. Its
projection and outward rounding stay the same. Clipped cells retain the prism
solver.

No Hall content, actor population, controller policy, or visual quality setting
was changed.

## VM measurements

Raw evidence: `profiles/movement-collision-vm-20261119T034333Z/`.

The driver links the production core library, built with the development
profile and an opt-level 3 override for the core. It uses the system allocator.
It has no ECS host, renderer, or GGRS. The before and after arms run in balanced
order with nine samples per case. Each body sample steps 128 ordinary bodies
for 60 ticks at 60 Hz. Position and velocity bits are hashed each tick; all final body
fields are hashed outside timing. Envelope coordinate bits are also checked.
All compared checksums agree.

| Case | Before | After | Change |
| --- | ---: | ---: | ---: |
| Body tick, 8 blocks | 6.955 us | 7.259 us | 4.4% slower |
| Body tick, 100 blocks | 8.097 us | 7.627 us | 5.8% less time |
| Body tick, 2,000 blocks | 21.870 us | 7.675 us | 64.9% less time |
| Swept cells, scale 0.5 | 358.6 ns | 316.9 ns | 11.6% less time |
| Swept cells, scale 1 | 346.1 ns | 315.4 ns | 8.9% less time |
| Swept cells, scale 4 | 339.8 ns | 307.7 ns | 9.4% less time |

The small-world increase is about 0.30 us per body tick. This is consistent
with spatial query overhead, but the changes were measured together. The larger-world gain removes work that grows with
unrelated geometry. These measurements do not establish desktop FPS gains.

A hash-map replacement for grid cells was rejected. It helped the largest
local-query cases but regressed most small and medium cases. A direct streamed
vertex fold was also rejected after it made cell construction slower. Neither
candidate is in the final source.

## Verification

The core suite passed 907 unit tests, with one existing unit test ignored.
The allocation measurement and one doctest remain explicitly ignored. The new
behavioral test compares indexed hazard and rebound results against a visible
ordered scan, including thin hazards, long sweeps, hidden and added geometry,
and overlapping rebound surfaces.

`cargo check -p ambition_app --features profile -j 1 --config
profile.dev.package.ambition_platformer2d_core.opt-level=3` passed before the
merge and again on `9b771663ac`. The final check took 2 minutes 6 seconds.

`cargo test -p ambition_demo_smash_app -j 1 --config
profile.dev.package.ambition_platformer2d_core.opt-level=3` passed on that same
merged source: 18 unit tests and 56 integration tests, with six existing
integration tests ignored. It had no doctests. The cold build took 46 minutes
10 seconds; integration execution took 666.21 seconds. This is a verification
receipt, not a matched host performance comparison.

The implementation is `e8da76b21a`. The benchmark isolates its core changes.
The host checks also include upstream native publication, scripted contact,
and presentation changes from the merge. The full workspace, web, and Android
matrices were not run.

The VM target bind was checked before the builds. After all Cargo processes
finished, 244 incremental cache sessions created since this task's first build
were removed. Their creation times and paths are recorded in the raw evidence.
The local target backing was checked again before removal. Older sessions,
compiled libraries, and linked binaries were retained. Free space rose from
1.61 GiB to 10.00 GiB. Existing measurement edits and profile summaries were
left unchanged.
