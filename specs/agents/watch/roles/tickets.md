# Tickets seat

You own the day's `## Tickets` (policy actor `collector:tickets`). Always `--section Tickets`. Shared rules: `guidance/role.md`. The filter is ADR-0006; Jira is read-only for you: no comment, transition or assignment.

## Sources and scope

The org `resources.yaml` tracker entry names the tracker (its id is the `source_ref`; the examples use `jira`). **What to read is the org's `ticket_scope:` key, and only that**: `source` (the tracker id), `involving` (`operator`: issues where the operator is assignee or reporter, or `any`), `projects` (`any` or a list of project keys), `exclude_done` (true or false), optional `include_moved_to_done` (true or false, default false), `first_run_days_back`. Read issues updated since the cursor that match it. Do not infer the scope from the tracker entry's `board`, `project` or `note` fields.

If `exclude_done` and `include_moved_to_done` are both true, the read also catches an issue that moved to Done since the cursor, so its resolution is recorded: the status clause of the query is `(statusCategory != Done OR status CHANGED TO Done AFTER "<cursor>")` instead of `statusCategory != Done`.

If the tracker entry has `jql_timezone`, convert the cursor to that zone before writing `updated >=` (and the `AFTER` time above): the tracker reads a date-time literal in that zone, not in UTC, and an offset in the literal is ignored. Run a control window that should match something before you report an empty result.

If `ticket_scope:` is missing or has a key you do not understand, **do nothing**: write no entry, move no cursor, and send one queue row to the desk (the shared role's Failure paragraph) saying the key is missing.

## Anchors (ADR-0010 s5)

`jira:<KEY>#created`, `jira:<KEY>#c<comment id>` or `jira:<KEY>#h<changelog id>`.

## What to record (ADR-0006 s3, s4)

| Record | Skip |
|---|---|
| created, status, assignee, priority or due date change, resolution | rank, sprint, label and watcher changes |
| each new comment | |
| a description edit, only on a mission's own ticket (a mission SPEC `ticket:` key) | a description edit elsewhere |

- A comment is a two-sentence summary of what it decides, answers or asks, with the commenter's contacts key in `--who`. Never paste the text.
- A status or field change is one line: the key, what changed, from and to. No knowledge page mirrors a Jira status, and you create no per-ticket page.
- `--assigned-to-operator` when the issue is assigned to the operator (ADR-0006 s7). `kb` applies the `escalate` flag to urgency; you do not decide it. `--urgent` yourself only for Highest or blocker priority assigned to the operator, or someone blocked waiting on the operator.

Example:

```bash
kb append <org> --section Tickets \
  --anchor 'jira:ABC-123#c88123' --time 2026-11-01T10:15:00Z --who alex --assigned-to-operator \
  --summary 'Asks to confirm the rollout order before Friday. Waiting on the operator.'
```
