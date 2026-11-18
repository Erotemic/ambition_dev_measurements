# Autoplay input at low frame rates

## Desktop evidence

The native capture `desktop-autoplay-native-20261118T141710Z` used source
`f692ffd4f417` on `calculex`. The only recorded working-tree change was the
measurement submodule. The Hall frame windows had medians near 150–200 ms.

The body crossed the Hall. On its return, it fell 400 px. At capture wall time
79.425 s, navigation reported `NoRoute` at `(1094.783, 6727.9995)`. It then
repeated this result from the same position. The capture does not cover the
full intended itinerary.

The follower ran once per rendered frame. Its jump prediction used the render
delta, while graph legs were validated in 60 Hz movement steps. At these frame
rates, one command could remain in use for many simulation ticks.

## Change

Commit `9c4c6490b4` samples autoplay controls at the local input tick boundary.
It uses the host step clock and the ordinary participant input path. The GGRS
host reuses the command when it requests the same input frame again. It does
not sample during historical replay. A policy press reaches one new tick;
held levels reach later ticks. A host-driven secondary seat keeps its input.
The game itinerary remains on Update so it can start a session.

No Hall content was changed. The graph and shared route follower remain the
movement authority.

## VM verification

Raw text logs and metadata are in
`profiles/autoplay-input-cadence-vm-20261118T1442Z/` in this repository. The
build used the unoptimized test profile. These are behavior checks, not
release performance measurements.

- Three selected app integration tests passed. They cover the Hall return
  with 200 ms render frames under GGRS, the full Hall and primeval round trip,
  and one press with later held input on fixed and bounded-rollback hosts.
  The selected run took 1238.08 seconds, including graph preparation.
- The Hall stress test was then marked as ignored for normal test runs. Its
  body did not change. Repeat it with `--ignored`.
- Input unit tests passed: 2 app tests and 19 GGRS host tests. These include
  repeated requests for the same input frame and a separate secondary seat.
- The graph-input unit test passed.
- The assembled app check passed. After publication ordering moved to a named
  boundary, the app check and the press catch-up test passed again.
- Five foreign-ordering checks passed.
- The broad Python run was stopped after 671 passes, 18 skips, and 11 failures.
  Nine failures came from a default interpreter without `tree_sitter_rust`.
  The related rerun with the repository interpreter passed all 15 cases.
  One ordering failure was fixed by the named publication boundary. The asset
  coverage failure names missing targets for `fell_sheep`, `math_show_car`,
  and `math_show_goat`; their authored files are outside this change.
- Workspace policy: 33 passed and 2 failed. All 11 reported violations name
  files outside this patch. The failures are in `engine_policies` and
  `game_policies`, as in the earlier desktop preparation task.
- The full headless feature gate refused to start with 38.5 GB free in the
  bound target store. It requires about 40 GB. Existing artifacts were kept.

## Capture comparison

The Tracy warm build was still active in the shared checkout when the input
changes were made. Its metadata was written before those edits. It can contain
other build inputs than the native capture even if its metadata names the same
base commit. Use both captures for diagnosis. A comparison of full runs needs
both commands to use the same committed source and to visit the same rooms.

## Restart and desktop follow-up

The tool runtime restarted while the broader rollback and Smash integration
checks and the merged input build were active. Those processes ended without
a final verdict. The Smash unit suite had passed all 18 tests. Do not count the
unfinished integration runs as complete. The VM kernel log was not readable,
so the cause of the restart was not confirmed.

The later native capture `desktop-autoplay-native-20261118T153023Z` names
`5acadeade9fb` and records only measurement changes. It completes the Hall and
primeval itinerary repeatedly on the desktop. It does not report `NoRoute`.
The app compile check also passed after the upstream merge. The navigation
and input implementation files did not change in that merge.

The maintainer then requested local work only, with no pushes or pulls, for
the demo. Follow-up verification uses one compiler job and narrow checks.
