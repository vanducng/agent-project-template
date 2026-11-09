# Meetings seat

You own the day's `## Meetings` (policy actor `collector:meetings`). Always `--section Meetings`. Shared rules: `guidance/role.md`.

## Sources

The org `resources.yaml` entries of kind calendar and meeting notes (a Docs file per meeting). Each entry's id is the `source_ref`.

## Anchors (ADR-0010 s5, ADR-0021)

| Source | Anchor |
|---|---|
| Calendar | `<source_ref>:<event instance id>` (a recurring event: the instance id with its date) |
| Meeting notes | `<source_ref>:<file id of the notes document>` |

## What to record

- A meeting that was added, moved or cancelled, and a meeting held with its notes. A meeting in the next 24 hours that changed or was added is `--urgent` (ADR-0001 s7).
- The summary: who, topic, outcome, in 1 to 3 lines. Never paste notes or a transcript.
- If notes exist, read them read-only. An action item that names the operator goes in the summary.
- Skip a calendar update that changes no time, attendee or status. Skip a held meeting whose notes do not exist yet or are empty; record it when the notes arrive. A meeting that has already happened is never urgent.

## Decisions

A meeting with decisions is several entries, not one with sub-bullets (`kb append` takes 1 to 3 lines, none starting with `- `):

1. One `kb append` for the meeting itself, with the meeting's anchor.
2. One `kb append` per decision with the same anchor, the same `--time`, and `--decision "<text>"`. `kb` appends `#d-<id>` to the anchor, an id computed from the text, so it does not change when decisions are reordered and you never compute a hash. If the source carries its own decision or action-item id, add `--decision-id <id>` and `kb` uses `#d-<id>`.
3. An edited decision is new text and so a new id; the old entry stays as history. Do not try to replace it.

```bash
kb append <org> --section Meetings \
  --anchor 'cal:evt1_20261101T140000Z' --time 2026-11-01T14:00:00Z --who jo \
  --summary 'Weekly sync held. Two decisions follow as separate entries.'
kb append <org> --section Meetings \
  --anchor 'cal:evt1_20261101T140000Z' --time 2026-11-01T14:00:00Z --who jo \
  --decision 'Cap retries at 3 per job. Owner: alex.' \
  --summary 'Decision: cap retries at 3 per job. Owner: alex.'
```

Rerunning the calls returns `exists` for each.
