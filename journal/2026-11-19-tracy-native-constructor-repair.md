# Tracy native constructor repair

## Evidence

The maintainer supplied `1_bash_calculex_2026-11-18T19-09-01.log` and the
incomplete capture `desktop-autoplay-tracy-20261118T234842Z`. The game exits
with signal 11 after about 93 seconds. No crash stack is present.

The exact desktop executable has build ID
`0e30b0c05cd3b853a2e85cecacc2d7459df39b67` and was linked with mold 1.0.3.
A temporary read-only bind view exposed the desktop target while the VM target
bind stayed in place. Inspection found two constructor sections:

| Section address | Size | Contents |
| --- | --- | --- |
| `0x14c601c0` | 56 bytes | Native constructor table, including Tracy |
| `0x14c601f8` | 9,800 bytes | Rust constructor table |

The loader's `DT_INIT_ARRAY` starts at `0x14c601f8` and covers 9,800 bytes.
It omits the first table. The debugger stops at `main` with
`tracy::s_instance == nullptr` and no Tracy worker threads. A direct run opens
no Tracy socket. The profiling warning and linked symbols did not establish
that native initialization had run.

This is a concrete initialization fault. Tracing then accesses native objects
whose constructors did not run. A desktop core stack is not required to prove
that fault. The sustained repaired run below passes the earlier crash window.

## Fix

`8d66bea506` removes the forced Linux clang/mold linker configuration. Native
builds now use Rust's default linker. On the tested toolchain, that is bundled
LLD 22.1.8. No manual Tracy startup path or patched binary is shipped.

A small Rust/native constructor probe passes with the default linker. The
actual game libraries from the desktop target were also relinked with it.
The entry point is the repository's normal game entry point; the application
libraries retain their desktop build code. The diagnostic executable has one
complete constructor table of 9,856 bytes. At `main`, its native profiler
instance is non-null and its three Tracy worker threads exist.

The old VM collector immediately reports a protocol mismatch against this
executable. That establishes a working connection. The matching 0.14.0 tools
were then built with one compiler job. The dependency version lookup now uses
`Cargo.lock`, so an older cached client cannot choose the tool version.

Changing the linker configuration invalidates cached artifacts. The first
ordinary build after this change can rebuild dependencies.

## Sustained validation

The diagnostic uses the production shared host without a render backend,
starts in `primeval_trailhead`, and enables the normal autoplay route. It
travels through the primeval rooms, hub, and Hall. It runs for 150.05 seconds
and ends at its planned timeout with status 124, rather than signal 11.

The collector records 120.3 seconds and exits successfully. Its report contains
22,413,765 zones. The saved trace is about 154 MiB. It was decoded by the
matching CSV exporter. This validates native initialization and sustained CPU
tracing with the same application libraries. It does not validate the desktop
GPU path. The VM run uses `TRACY_NO_INVARIANT_CHECK=1` and
`TRACY_NO_SYS_TRACE=1`; its collector port is explicitly 8094.

Raw logs, constructor tables, debugger output, source probes, trace, exports,
and command metadata are in
`profiles/tracy-constructor-repair-vm-20261119T0030Z/`. Binaries and raw capture
data remain outside Git tracking.

## Report memory and storage

The previous report path wrote every zone instance to a CSV, then read every
row into a Python list. With millions of events, memory and storage use grew
with the event count before reduction.

The report now streams the exporter into window reduction. The parser retains
only window buckets. Aggregate exports and manually supplied instance files
use the same parser. It also avoids allocating a new bucket for each repeated
event.

The full repaired trace reduced 22,413,760 exported events into 383,278 window
buckets in 72.97 seconds. The aggregate and window event counts agree. The
collector's five additional zones are not in the aggregate export.
The retained window CSV is about 51 MiB; no instance CSV was written.
`time -v` reports 891,688 KiB maximum child RSS. That is the largest process
peak, not the simultaneous sum across the pipeline.

## Further optimization evidence

The headless trace's recorded self-zone totals include body integration at
12.86 seconds across 4,916 calls, rigged presentation at 3.10 seconds across
5,244 calls, and target selection at 1.97 seconds across 4,916 calls. These are
zone wall durations, not sampled CPU totals. They identify candidates; they do
not establish desktop frame-time gains. Parent schedule self time can include
waiting for worker threads.

## Verification

All 906 core tests passed, with one existing test ignored. The Smash demo run
passed 18 unit tests and 56 integration tests, with six existing integration
tests ignored. These checks cover the ledge optimization before the linker
change and upstream merge; its source remains unchanged.

All 27 selected profiler, tool-version, and streaming tests passed. The real
120-second export also completed with matching event counts. The native
constructor probe and the relinked application establish that the default
linker runs the omitted constructors.
