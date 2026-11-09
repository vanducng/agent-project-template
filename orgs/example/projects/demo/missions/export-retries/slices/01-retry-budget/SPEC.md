---
id: EX.1.1
slice: 01-retry-budget
mission: export-retries
owner: dev-build@demo
status: done
depends_on: []
---

# Slice 01: retry budget

## Intent

A failed export delivery is retried three times with a 30 s backoff before anyone is paged.

## Requirements

1. A delivery that fails with a timeout or a 5xx is retried, at most three times.
2. The wait between attempts is 30 s, then 60 s, then 120 s.
3. After the third failure the job pages the on-call channel once, with the file name and the last error.
4. A 4xx is not retried.

## Proof contract

Door: two-way (a config value and one code path). Blast radius: the nightly export job only. Undo: set `retry.max_attempts: 0` in the job config, or revert the PR.

- [x] Unit tests for 1, 2 and 4, red on the base and green on the head.
- [x] A local run against a receiver stub that fails twice, then accepts: one file delivered, two retries logged.
- [x] A local run against a stub that always fails: one page, after the third attempt.
- [x] Review verdict PASS from a seat of another model family, bound to the PR's patch-id.

Local stack: tests only.
