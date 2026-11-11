# Portal-free simulation cost: current-engine sample

## Scope

Source: `47fa11f164481a497b198453b9c75186a3a89613`. This receipt measures
the current engine. It does not compare the engine before the material sweep
change and does not establish a regression or an optimization gain.

The VM reports 12 CPUs and an Intel Core i9-11900K at 3.50 GHz. The kernel is
Linux 6.8.0-147-generic, x86-64, under KVM. The build uses the development
profile: workspace crates are unoptimized and third-party crates use level 3.
The executable retains symbols. No collision source or dependency settings
were changed for this measurement. No build ran during a match measurement.

The program is the assembled visible host in `NoWindow` mode, with GGRS and
CPU-controlled fighters. It has no adapter, render app, display pacing or
window. This is simulation-host cost, not displayed-frame or mobile cost.
The Smash stage constructor adds no authored portal pairs. The mirror cast
uses four performers; mixed-v1 uses performer, robot, Sanic and projectile
polygon. Each match has 40 stocks per fighter, CPU level 3 and debris off.

## Commands

```bash
cargo rustc -j6 -p ambition_app_tools --bin smash_match_profile -- -C strip=none
target/debug/smash_match_profile --fighters 4 --cast mirror --character performer --ticks 1800 --stocks 40 --debris off
target/debug/smash_match_profile --fighters 4 --cast mixed-v1 --ticks 1800 --stocks 40 --debris off
```

The host settles for 30 updates, then waits for the opening ceremony to release
the cast. Measurement starts at release. Each row reaches exactly 1,800
simulation ticks in 1,800 host updates. All four seats remain at run end.
The live-cast checks sample every 50 updates. Timing includes normal background
asset preparation and live content scanning.

| Run | Simulation ticks | Mean ms/tick | P95 host update ms | Live-cast coverage |
| --- | ---: | ---: | ---: | ---: |
| mirror-1 | 1800 | 15.0056 | 20.2740 | 100% |
| mirror-2 | 1800 | 15.3210 | 20.7565 | 100% |
| mixed-1 | 1800 | 16.5687 | 27.6085 | 100% |

## Warmed native sample

A separate mirror run reaches exactly 3,000 ticks with 100% sampled live-cast
coverage and four seats at run end. It averages 15.8298 ms/tick, with a
23.3569 ms P95 host update. Native sampling is enabled ten seconds after the
cast is live and disabled when the timing window ends. The recorded interval
is 37.490 seconds. The event is `cpu-clock:u`, at 99 Hz, with inherited worker
threads and a 32,768-byte DWARF stack. The capture contains 4,496 samples.

```bash
perf record -e cpu-clock:u -F 99 --call-graph dwarf,32768 --delay=-1 --control=fifo:CONTROL -o steady.perf.data -- target/debug/smash_match_profile --fighters 4 --cast mirror --character performer --ticks 3000 --stocks 40 --debris off
perf report -i steady.perf.data --stdio --no-inline --children --call-graph none --percent-limit 0.1 --sort symbol
```

Write `enable` and then `disable`, each followed by a newline, to the control
FIFO at the stated boundaries. Inline expansion is disabled in the report.
The earlier 8,192-byte-stack sample has truncated callers and includes startup;
do not use it for the cost shares below.

| Function | Inclusive user-CPU sample share |
| --- | ---: |
| Body update with frame clusters | 4.40% |
| Axis sweep | 3.83% |
| Material axis repair | 2.51% |
| Material axis hits | 2.40% |
| MaterialCast::cells | 2.14% |
| MaterialCell::bounds | 1.47% |
| World::material_axis_contacts | 0.98% |
| Background sprite source scanner | 17.68% |

Inclusive shares overlap. They include worker CPU time and are not fractions
of host wall time. Finite sampled stacks can omit callers. This sample does
not justify a second AABB solver. The canonical material authority stays
unified. An optimized historical control and a displayed-host measurement
remain required for R5. These runs do not measure body-to-visual lookup
scaling or compare the old and new lookup implementations.

Local evidence is under `target/portal-r5-current/`: `timings.json`, the three
unprofiled logs, `steady-events.json`, `steady.log`, `steady.perf.data`,
`steady-inclusive.txt` and `steady-attribution.json`. The runner scripts in
that directory record the exact commands and control boundaries.
