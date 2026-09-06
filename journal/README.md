# Journals

A journal entry is the narrative a ledger row cannot hold: what was being chased,
what was tried, what turned out to be wrong, and what the next person should not
repeat. The `.jsonl` ledgers hold numbers; `summaries/` holds one run;
**a journal entry holds a campaign.**

## The rules

1. **One file per campaign**, named `YYYY-MM-DD-<what-was-chased>.md`. A campaign
   is a question, not a day — it may span several sessions and several hosts.
2. **Name the host in the first line.** Every number in this repo belongs to a
   machine (`hardware.md`). An entry whose host is unrecoverable is a story, not
   a measurement.
3. **Record what was WRONG, not just what was found.** The retracted conclusions
   are the expensive part; a lesson that made it into `profiling-lessons.md`
   should say so, and the entry keeps the incident that produced it.
4. **Link the runs.** Bundle summaries live in `summaries/<run-id>.md` and the
   raw bundles in `profiles/<run-id>/` (untracked). Quote the run id.
5. **End with what is OPEN**, including what is blocked on a decision and whose
   decision it is. An entry that ends with only successes is a status report and
   will not help the person who picks the work up.

## Why this exists

The desktop-hitch campaign of 2026-08-29 produced five retracted conclusions, six
lying instruments and one noise floor that moved by 5x within an hour. None of
that fits in a ledger row, all of it was expensive, and the next campaign on
different hardware will meet the same traps in a different order.
