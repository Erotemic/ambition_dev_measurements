# Paired desktop capture review

The maintainer supplied `desktop-autoplay-pair-20261118T181136Z` and restored
permission to push and pull. The capture uses `402d27dc55d8`, with the cast
admission change and the Tracy-first paired command.

## Native pass

The native pass completed its 300-second window. Its first Hall visit reached
the far side, returned to the hub, and continued through the primeval rooms.
The second Hall visit also crossed and returned. The navigation failure from
the earlier input-sampling implementation did not recur in these visits.

The first Hall graph has 81 surfaces, 1,932 links, and 23,828 rollouts in both
this capture and `desktop-autoplay-native-20261118T153023Z`. The time from
Building to Going increased from 24.281 to 55.690 seconds. The first hub graph
also increased from 3.996 to 7.125 seconds. The first Hall crossing after graph
completion increased from 7.607 to 16.897 seconds. These measurements show a
slowdown in this capture. They do not isolate its cause. The later cached Hall
crossing took 8.453 seconds in the new capture.

The native metadata has no Cargo features. Its log has no Tracy-active warning.
The earlier `map_volume` hotspot is absent from the new leading symbols;
validation remains visible. The kernel benchmark result does not establish a
desktop speed increase. Do not use overall run averages as a matched comparison:
the runs spend different amounts of time building graphs and visiting rooms.

## Tracy pass

The Tracy build succeeded. The game log confirms that Tracy tracing is active.
The game then exited with signal 11, status 139, after about 78.6 seconds in the
primeval trailhead. The collector log contains only
`Connecting to 127.0.0.1:8086...`. No trace was saved. There is no crash stack
in the bundle. The cause of the crash and the failed connection remains unknown.
This evidence does not establish an out-of-memory failure or a protocol mismatch.

The paired command retained the native result after this failure. Its summary
mistook the diagnostic subfolder for a separate workload mode. The report now
excludes that subfolder from the multi-mode introduction. The raw capture files
remain outside Git.

## Integration

Upstream code and measurement records were merged. The lifetime totals were
regenerated from the combined ledger to resolve the generated-file conflict.
The maintainer's frame-history edits and untracked summaries were preserved.
This review adds no engine behavior change. No Cargo build was started: the
target bind was absent when inspected after the tool runtime restart.
All 22 desktop profiler command tests passed, including the paired report check.
These tests use simulated external profiling tools. They do not validate a
desktop Tracy connection or the assembled app after the upstream merge.
