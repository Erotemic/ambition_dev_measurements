# Modern mold and Tracy initialization

## Linker selection

The maintainer asked to retain mold for native link speed. The earlier fault
was measured with mold 1.0.3. It did not establish that modern mold has the
same fault, or that Rust's default linker is slower.

`fecf409688` selects mold 2.42.1 through a Cargo linker driver. Setup downloads
the pinned release to the user cache and checks its SHA-256 checksum. The
driver passes its absolute path to clang. A `PATH` change alone is insufficient:
clang on the VM selected `/usr/bin/ld.mold` before a newer copy on `PATH`.

The developer setup installs the linker. Profiling preflight checks the same
installation and can install it in apply mode. Cargo itself does not download
the linker. Each machine needs its own installation. The desktop's mold 1.0.3
installation is not removed or used by the driver.

## Constructor validation

A mixed Rust/C++ constructor probe passes with mold 2.42.1. The native
constructor writes the expected value before Rust `main`. Its ELF has one
constructor table.

The production game entry point was then linked against the same cached
desktop application libraries used in the earlier repair test, from
`e8c9b748ab`. The resulting ELF has one 9,856-byte constructor table, and
`DT_INIT_ARRAY` covers the full table. The matching Tracy 0.14.0 collector
connects successfully.

These cached libraries predate the new `action_delivery_rules` schema. For
the sustained diagnostic, a private mount namespace selects their original
content pack manifest from `e8c9b748ab`. The checkout manifest stays unchanged.
The diagnostic uses the production headless host and normal autoplay route.
It does not exercise the desktop GPU path.

Raw evidence is in `profiles/tracy-modern-mold-vm-20261119/`. It includes the
link command, linker version, ELF tables, manifest selection, environment,
capture, and process status. Binary artifacts remain outside Git tracking.
No comparable full game link benchmark was run.

The game ran for 150.12 seconds and ended at its planned timeout with status
124. The collector exited with status 0 and saved 120.3 seconds of tracing,
with 20,098,199 zones and a 140.5 MB trace. The matching CSV exporter decoded
the saved trace successfully. This passes the earlier crash window.

## Verification

The profiling preflight passed all 13 checks. The existing desktop profiler
and Tracy version tests passed all 29 cases. Shell syntax checks passed for
the changed setup scripts. A missing-cache check confirmed that linker use
fails with the install command and does not download tools during a build.

`cargo check -p ambition_app --features profile -j 1` passed with the pinned
linker. The first check took 18 minutes 5 seconds after the linker configuration
change. A second check on the final merged tree passed in 1.38 seconds. This
measures a cached check, not full binary link speed. The full workspace test
suite was not run.

The temporary read-only desktop target view was removed. The VM's local Cargo
target bind remains in place. Existing measurement edits and profile summaries
were left unchanged.
