# Org watch

Kind: sop

This is the brief the four watch seats inherit from their cwd, the rig's task dir `orgs/<org>/tasks/<org>-watch/` (gitignored). The template and the render steps are in the `openrig-workspace` skill, "Set up an org watch rig" (ADR-0021 Q12). The layout, page shapes, anchors and lifecycle are the `okf` skill's. This file does not restate them.

## Seats

| Seat | Owns | Effort |
|---|---|---|
| `inbox` | the day's `## Messages`; operator ideas from `fleet idea` rows | medium |
| `meetings` | the day's `## Meetings` | medium |
| `tickets` | the day's `## Tickets` | medium |
| `dream` | consolidation: pages, closing a day, proposals; the weekly and monthly passes. **Parked at go-live:** it is not in `rig.yaml`; `dream.member.yaml` adds it with one command once the dream passes review | high |

There is no `sync` seat. `workspace-sync` is the only committer, on the org's `kb_host`, behind `kb gate` (ADR-0017 s2). A seat never runs `git`.

## Reading and writing

- Read only through `kb context <org>` (and `<page-id>`, or `--day <D>` for a closed day).
- Collectors write only through `kb append` and `kb idea`. The dream writes only through `kb page write`, `kb day close` and `kb dream begin|renew|batch|end` (and `kb proposals add`, when that command exists after go-live). Every write is checked against the seat's policy actor and journaled (ADR-0020 Q2).
- Seats never run `kb gate`, `kb migrate`, `kb archive`, `kb restore`, `kb retitle`, `kb triage apply` or any `--apply`. Those are the operator's.
- Your seat's identity is your session. Never pass `--as`.

## Do not

- Post, reply or react anywhere. Read history first; fall back to `gog`, then Aside read-only (the shared role has the order).
- Sleep, loop or wait for the next tick. The watchdog is the loop.
- Edit a file by hand: an index, `log.md`, `triage.md`, `## Work`, `## Ideas` and every generated section belong to the CLI.
- Put a secret, a transcript, raw chat or a screenshot in the tree. Name no other org.
- Work around a refused command. Stop, write nothing by hand, and report (the shared role says how).

## Seat directory

A host whose tmux server has a dead working directory (after a volume drop) cannot start a new seat pane. There, set `PI_TUI_CWD` to the absolute task dir in the member `env` of that task dir's copy of `rig.yaml`; the launcher changes to it first when it is an existing absolute directory, and ignores it otherwise. The template does not set it.

## Watchdog

One `periodic-reminder` per seat, target `watch-<member>@<org>-watch`, with the one-line message from `watchdogs/<member>.yaml`. The intervals differ per member (inbox 1860 s, meetings 2580 s, tickets 3180 s, dream 3660 s: 31, 43, 53 and 61 minutes) so the seats do not wake in the same minute and hit the plan limit together. The seat does not register its own loop. Each seat sets `PI_TUI_RETRY_MAX: "1"`: Pi has no way to skip a usage-limit error, so the seat gives up after one retry and the next tick tries again.

## Change of model

Check the live model list and `cpq`, edit the `watch` profile in `fleet/routing.yaml`, rerun `fleet/bin/rig-to-pi convert <rig.yaml> watch`, then `rig seat set-model` and `rig seat launch --fresh --stop` per seat (`docs/model-picker.md`).
