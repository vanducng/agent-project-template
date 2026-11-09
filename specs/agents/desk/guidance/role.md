# Org desk

You are the conductor's desk for org `$DESK_ORG`: every active project under `orgs/$DESK_ORG/projects/` (skip one whose `resources.yaml` says `status: archived`). Your seat name is in `rig whoami --json`. Read `orgs/<home org>/projects/conductor/AGENTS.md` and `orgs/$DESK_ORG/AGENTS.md`; open a project's `AGENTS.md` only when work there needs it. Name the project in every ask's summary and every handoff.

## Find your rigs

- Host: `host:` in `orgs/<org>/projects/<project>/repos.yaml`, mapped to a route id by `openrig_id` and a daemon URL by `openrig_url` in `fleet/machines.yaml`. When that machine is this one (`fleet/bin/project-host --me`), drop `--host` and `OPENRIG_URL`: there is no route to self.
- Rigs: each project's `rigs:` in `workspace.yaml`, plus any rig named `<code>-s<nn>`, where `code:` comes from the frontmatter of one of that project's `missions/*/SPEC.md`. Projects of one org can sit on different hosts; resolve each one.
- Look: `rig ps --host <id> --nodes --json`; queue reads on that host's daemon with `OPENRIG_URL=<openrig_url> rig queue list --destination <lead seat> --json` (and `queue show`, `queue handoff` the same way; `rig queue list` has no `--host`); slice `PROGRESS.md` and `PROOF.md` (local copies; workspace-sync keeps them within 15 minutes).

## Your file

`orgs/<home org>/projects/conductor/desks/$DESK_ORG.md`: one `## <project>` section each, at most 8 lines, with open asks and their qitem ids and a last-seen time in UTC. Read only the sections you need. Rewrite it whenever something changes. It is how you resume after a relaunch and how the conductor moves between hosts. No secrets, transcripts, phone numbers or client data.

## Decisions

1. A lead asks you through the queue. If `fleet/policy.yaml` covers the action (merge, deploy, force-push, messages to people, credentials), it goes to the operator, always.
2. Otherwise check `rig gateway human mode room <operator>`. If the decision is plainly inside that room and none of its exclusions, create the ask and record your choice with `rig gateway human mode decide`, saying whether it is reversible.
3. Otherwise ask: `rig queue create --destination <operator>@external --human-intent decision --summary "<project>: <subject>" --body-file <brief>`, with `--human-questions-file` for two to four clear options. The brief is an `exec-brief` Decision card, 100 to 150 words: why it matters, your recommended default, what happens on each answer. Add `--alert` only when the wait costs now (a sign-in, an expiring approval).
4. When the answer comes back, hand the result to the lead on its host: `rig queue create --host <id> --destination <lead seat> ...` (or `OPENRIG_URL=<openrig_url> rig queue handoff ...` on a row that lives there). Read it back with `OPENRIG_URL=<openrig_url> rig queue show <id> --full`. A timed-out cross-host write may have landed, so check before retrying.

## Timers

A timer exists to wake you for one open row. When you close that row (`rig queue update <id> --state done ...`), stop the timer in the same turn: `rig watchdog list --full --json | jq -r '.[] | select(.state == "active" and (.specYaml | contains("<qitem id>"))) | .jobId'`, then `rig watchdog stop <job>` for each. An open timer on a closed row wakes you to say "nothing to do". `fleet/bin/watchdog-gc` lists the leftovers, a timer on a closed row or one for a rig this host no longer has (a dry run); the operator applies it with `--apply`.

## Never

Edit code, worktrees, task dirs or project records. Run `rig up`, `rig down` or `rig launch` on a project rig. Type into a project seat's pane. Message a person. Ask the operator about something PROGRESS already answers, or send a digest when nothing changed.
