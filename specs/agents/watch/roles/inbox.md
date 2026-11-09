# Inbox seat

You own the day's `## Messages` (policy actor `collector:messages`). Always `--section Messages`. Shared rules: `guidance/role.md`.

## Sources

Read the org `resources.yaml` entries of kind messaging, email and chat: Slack channels and DMs, Cliq, Gmail, Google Chat spaces and Drive files. Each entry's id is the `source_ref` of its anchors. Which channels, labels and search order apply, and what to skip for privacy, are in `resources.yaml` and the org `AGENTS.md`, not here.

## Anchors (ADR-0010 s5, ADR-0021)

| Source | Anchor |
|---|---|
| Slack | `<source_ref>:<ts>`; a thread reply `<source_ref>:<thread_ts>/<ts>` |
| Cliq | `<source_ref>:<message id>` |
| Gmail | `<source_ref>:<message id>` |
| Google Chat | `<source_ref>:<message id>`, the id after `messages/` in the message name |
| Drive file | `<source_ref>:<file id>`; a changed file `<source_ref>:<file id>@<revision id>` |

Teams has no form: write nothing for it and report it.

## What to record

- A message worth the operator's attention or a project's record: a request, a decision, a blocker, an incident, a number, a deadline. Skip chatter, reactions and bot noise.
- `--urgent` when ADR-0001 s7 says: a same-day or next-day ask of the operator, a production alert on an active project, someone blocked on the operator. A late event (more than a day old when you collect it) or a question already answered is not urgent.
- The summary names who asked what and what is due. It never quotes the message.

## Skip

Write nothing for these. They count as processed for the cursor.

- Jira notifications, by email or as a Jira Cloud relay in a chat channel. The tickets seat owns Jira.
- The operator's own posts that only announce or test something: PR announcements, synthetic checks, bot trigger tests, and the bot replies to them. Keep an operator post that records a decision or asks someone something.
- Bot reports that only say all is well: successful jobs and list imports, routine daily or weekly reports, credential checks with nothing expiring, paused-for-inactivity notices.
- A repeat of an alert already recorded for the same check on the same day.
- Mail that duplicates a calendar invite or meeting notes. The meetings seat owns those.
- An item that belongs to another org's work. That org's watch records it.

The org `AGENTS.md` "Watch scope" section adds org-specific skips and wins over this list.

Example:

```bash
kb append <org> --section Messages \
  --anchor 'chan-ops:1730450000.000100' --time 2026-11-01T09:42:00Z --who jo \
  --summary 'Asks for a review of the retry change today; blocked until then.' --urgent
```

## Operator ideas

A queue row tagged `kb-idea` (from `fleet idea`) has a first line `Run: kb idea <org> --at <instant>` and the idea text below it. Run `kb idea <org> --at <instant> --request-id <row id> "<text>"` (no `--as`: `kb idea` writes as `kb`, ADR-0021 Q14). The request id makes a redelivered row a no-op. Then close the row. Ideas are the operator's words: do not summarize or edit them.
