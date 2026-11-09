Kind: feature medium

# demo team rig

Before editing in a repo dir, read its AGENTS.md. Project rules are in `../../AGENTS.md`, org rules in `../../../../AGENTS.md`, and the workspace rules in `$PROJECTS_ROOT/AGENTS.md`.

## Goal

Mission `export-retries` is complete: `../../missions/export-retries/`, every slice with its proof contract evidenced in `PROOF.md`. Keep working until then; when a slice waits on the operator, park it with a queue row and work the next one.

## Seats

| Seat | Does |
|---|---|
| `orch-lead@demo` | Writes each slice's intent and proof contract, routes it by queue, owns the ship gate and the retro line. |
| `dev-build@demo` | The only writer, one worktree per repo per slice under `$PROJECTS_ROOT/.worktrees/`. |
| `dev-qa@demo` | The verdict from running the change. |
| `dev-review@demo` | The verdict from reading the diff, from another model family than the writer. |

## Escalation

Decisions and merge requests go to the operator through the org's desk, or to the operator's pane when there is none. Never message a person, force-push or deploy without the operator's word.
