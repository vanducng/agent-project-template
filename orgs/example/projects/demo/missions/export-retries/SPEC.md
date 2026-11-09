---
id: EX.1
mission: export-retries
stage: wip
created: 2026-01-05
intent: "A failed export delivery is retried with a budget and a backoff, so a short outage at the receiver no longer drops a day's file."
depends_on: []
code: ex1
ticket: PRJ-120
milestone: 1
---

# Mission: export retries

## Slices

| # | Slice | Outcome |
|---|---|---|
| 01 | retry-budget | three attempts with a 30 s backoff, then a page |
