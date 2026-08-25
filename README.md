# ambition_dev_measurements
This is a repo that helps keep measurement bloat out of the main repo

## goal_check_cost.jsonl

The goal guard used to run shell checks — in this repo, cargo builds and test
suites — at the end of every turn, and released the run when they all passed.
This ledger timed them from 2026-08-08.

Read back on 2026-08-25 it retired the feature: **972 runs, 66.0 hours of cargo,
median 192s rising to 395s over the last 40 runs, and not one run ever passed.**
In 972 of 972 the verdict was already sealed by a sub-second `docs/planning`
grep before the build started. Concurrency roughly doubled the cost (186s with
no foreign build in flight, ~320-370s with one or more), while machine load
alone explained little — contention for the target directory, not for CPU.

`scripts/goal_guard.py` runs no commands now, so nothing appends to this file.
Schema 2 rows: `total_seconds`, `load_before`/`load_after`, `build_processes`,
`foreign_builds` (null = the repo never declared what a build looks like), and
per-check `name`/`ok`/`seconds`. Incidents: `docs/tools/goal-guard.md`.
