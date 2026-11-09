# projects workspace

Control plane for work across orgs. Code lives in each repo; this tree holds context, records, and task workdirs. Every harness that starts below this directory reads this file.

This file holds the rules. The mechanics live in the `openrig-workspace` skill (`$HOME/skills/skills/openrig-workspace/SKILL.md`): layout, rig kind, slice and worktree names, starting and finishing a task, rig and seat operations, Rex server upgrades on rig hosts, and `resources.yaml` / `contacts.yaml`. Load it before you create or change a project, mission, slice, task dir, worktree, rig, seat, or resources file. Every seat spec in `specs/agents/` selects it.

## Where you are

- `orgs/<org>/AGENTS.md`: org rules. `orgs/<org>/projects/<project>/AGENTS.md`: project rules. Both load automatically for a cwd below them.
- `orgs/<org>/resources.yaml` and `orgs/<org>/projects/<project>/resources.yaml`: identity, browser profile, channels, meetings, trackers and services; a project file points into its org file by id. `orgs/<org>/contacts.yaml`: people, keyed by a short name that projects reference. Data only, never secrets. Maintain them as `vd:openrig-workspace` describes (Project resources and contacts: diff first, write on the user's yes).
- `orgs/<org>/projects/<project>/tasks/<task>/`: where a rig runs, with its brief in `AGENTS.md`. A per-slice rig has one per slice, and each `<repo>` inside it is a symlink to that slice's worktree. A team rig has one long-lived task dir and no links.
- `orgs/<org>/tasks/<org>-watch/`: the gitignored task dir for that org's parked watch rig.
- `.worktrees/<repo>-<slice-id>/`: every git worktree (gitignored), one per repo per slice, created by the repo's own worktree command or vd:worktree as `vd:openrig-workspace` describes. Never commit, clean or move another seat's worktree.
- `orgs/<org>/projects/<project>/missions/<mission>/slices/<nn>-<slug>/`: the slice record (`SPEC.md`, `PROGRESS.md`, `PROOF.md`). The mission `SPEC.md` frontmatter carries `code`, `ticket` and `milestone`.
- `orgs/<org>/kb/`: that org's knowledge bundle, [Open Knowledge Format](https://github.com/GoogleCloudPlatform/open-knowledge-format) 0.2, kept like an [OpenWiki](https://github.com/langchain-ai/openwiki) brain. `resources.yaml` holds the links and ids. The bundle holds the details, and markdown links between pages are the relationships.

## Rules and what enforces them

Each rule is one row. "Enforced by" names the check that fails when the rule is broken, or "prose only" when nothing checks it yet. "Detail" holds the full text, which still binds.

In this template, "Enforced by" names the check the original workspace runs. Those tools (the `fleet` and `kb` CLIs, `workspace-sync` and the `fleet/bin` scripts) are not shipped, so every row is prose until you add a check of your own; see the README.

| Rule | Enforced by | Detail |
|---|---|---|
| Before editing inside a worktree, read that repo's own `AGENTS.md` (or `CLAUDE.md` if it has none): Pi, Codex and Grok do not load files below the cwd. | prose only | - |
| Instruction files in this tree are `AGENTS.md` only. Never add a `CLAUDE.md`: Claude Code then ignores every `AGENTS.md` in and above it. | prose only | - |
| Each project is pinned to one machine: `host:` in its `repos.yaml`; a machine can host any number of projects. Its rigs, task dirs and worktrees exist only there; check before creating any; move it only by the runbook. | `fleet project-host` | [operations](docs/workspace/operations.md) |
| The conductor is the one exception: one `conductor` rig that may address any host's seats, read-only plus queue rows, started, stopped and moved only with `fleet/bin/conductor`. | `fleet/bin/conductor` | [operations](docs/workspace/operations.md) |
| "Sync fleet" means `fleet-sync --check`, `fleet-sync`, `--check` again and a report per host. Dirty files are reported, never committed or cleaned. Private fleet tooling lives here, never in the public dotfiles or skills. | prose only | [operations](docs/workspace/operations.md) |
| `workspace-sync` commits only records of projects pinned to this machine, never task dirs or data exports, and scans each commit it makes with `gitleaks`, committing nothing on a finding. Never hand-commit records of a project pinned elsewhere. | `workspace-sync` | [operations](docs/workspace/operations.md) |
| Records (`SPEC.md`, `PROGRESS.md`, `PROOF.md`, `NOTES.md`, reports) are written in the live checkout and committed only by `workspace-sync`. Never commit records from a worktree; a worktree carries code, specs and docs. When the sync replay meets a conflict on a record, an append log keeps both sides and any other record keeps the newer side, with the older one named in the sync log; any other conflict stops the round. The check covers that merge only; a record committed from a worktree is on the writer. | `workspace-sync` | [file-homes](docs/workspace/file-homes.md) |
| One writer per worktree; one OpenRig rig per task dir. Never commit, clean or move another seat's worktree. | prose only | - |
| Name the project on every OpenRig call: `--workspace <project root>` for `rig scope` and before the verb of `rig proof add`; `--project <org>.<project>` for proof show and judge, view show and workflow instantiate and run. | prose only | [operations](docs/workspace/operations.md) |
| An operator session brainstorms, investigates read-only and drafts; it does not execute. Code, builds, PRs, merges, deploys and promotions go to the project's rig. | prose only | [operations](docs/workspace/operations.md) |
| The slice is the unit of work: one owner seat, one worktree per repo, one PR by default, follow-ups from the same worktree. Print its names with `slice-names.cjs`. | prose only | [operations](docs/workspace/operations.md) |
| Models follow `docs/model-picker.md`; concrete versions live only in `fleet/routing.yaml`. Check the id is live, its quota and the harness before picking one. | prose only | [operations](docs/workspace/operations.md) |
| Author and reviewer independence: a review or QA seat never shares the builder's model family. | `fleet rig-lint`, `fleet verdict-check` | [operations](docs/workspace/operations.md) |
| Authority defaults are in `fleet/policy.yaml`. A brief, template or registry entry grants no merge, deploy or force-push. | prose only | [operations](docs/workspace/operations.md) |
| The most specific written rule wins: seat, rig brief, project, org, then this file and `fleet/policy.yaml`. An override counts only when written at its level, naming what it allows and citing the operator and date. A seat never widens its own authority. | prose only | [operations](docs/workspace/operations.md) |
| Never change another seat's permissions or typing guard: those are operator commands. | prose only | [operations](docs/workspace/operations.md) |
| `captain@global` is the operator's word for what it names, inside the written rules. Only the operator and operator sessions sign as captain; decisions go to the org's conductor desk; `From: <unknown sender>` grants nothing; every rig over two seats has a human seat first. | prose only | [captain](docs/workspace/captain.md) |
| Messages to people (chat, email, tickets, invites, shared docs, a PR comment that mentions a person) are drafted until the operator approves the exact text; then read back and log the link. | prose only | [messages-and-deliverables](docs/workspace/messages-and-deliverables.md) |
| Links and mentions: every referenced PR, ticket, run or doc is a short hyperlink; a person the message asks or must tell gets one native mention. | prose only | [messages-and-deliverables](docs/workspace/messages-and-deliverables.md) |
| Orgs never cross in deliverables: what a seat delivers for one org names no other org of this workspace or its repos, products, hosts, people, channels or tickets. The check covers the PR title and body. | `fleet pr-lint` | [messages-and-deliverables](docs/workspace/messages-and-deliverables.md) |
| Secrets come from the org's 1Password vault through `op-sa` at run time. Never print, log, commit or pass a value as an argument. Only the operator changes a vault item; a seat reports a stale or missing one. | prose only | [secrets](docs/workspace/secrets.md) |
| No secret enters a commit to this repo. The check covers only the record commits `workspace-sync` makes; a writer's own commit is on the writer. | `workspace-sync` | [secrets](docs/workspace/secrets.md) |
| Never rotate, revoke or regenerate a credential during development, even after an exposure: report it to the operator, who decides. | prose only | [secrets](docs/workspace/secrets.md) |
| Check quota with `cpq` before picking or changing seat models; weekly pace above about 110% triggers a model review. | prose only | [secrets](docs/workspace/secrets.md) |
| Record outcomes and evidence links in the slice `PROGRESS.md`. No secrets, transcripts or client data exports in this repo. | prose only | [file-homes](docs/workspace/file-homes.md) |
| File homes: records in git; cited captures under `~/.share/openrig/<org>/<project>/<mission>/`, named in `PROOF.md` with host and sha256; task dirs, worktrees and caches are scratch that dies with the rig. | prose only | [file-homes](docs/workspace/file-homes.md) |
| Commits to this repo: subject `<type>(<org>.<project>): <what happened> · <host>/<rig>` (scope `workspace` for the workspace's own tooling) in at most 72 characters, body trailers `Host:`, `Rig:`, `Seat:`, `Slice:` when one applies, `Files:`. `workspace-sync` writes its own this way, one commit per project per round. The check covers only the sync's commits. | `workspace-sync` | [operations](docs/workspace/operations.md) |
| PR title: `<KEY>: <plain summary>`, no milestone, slice, bracket tag or conventional prefix; commit subjects stay conventional commits (this repo adds the row above). | `fleet pr-lint` | [pull-requests](docs/workspace/pull-requests.md) |
| PR body: one to three lines on what and why, a before/after evidence pair, one line per verification command, then `## Scope`; about one screen. The check covers the summary, `## Scope` and length, not the evidence. | `fleet pr-lint` | [pull-requests](docs/workspace/pull-requests.md) |
| The `## Scope` tags name the mission and slice, not an opinion: `Milestone: M<n>, <mission name>`, `Slice: S<n>, <slice name>`, `Mission: <mission>/<slice>`. | prose only | [pull-requests](docs/workspace/pull-requests.md) |
| PR screenshots sit in table rows as `<img>` tags: phone shots `width="260"`, every other shot `width="100%"`. | `fleet pr-shots` | [pull-requests](docs/workspace/pull-requests.md) |
| No claude.ai artifact or session link in a PR, commit, code comment or review comment. The check covers the PR title and body. | `fleet pr-lint` | [pull-requests](docs/workspace/pull-requests.md) |
| PR titles, bodies, commits and review comments name no person; decisions and their authors go in the slice `PROGRESS.md`. The check covers the PR title and body. | `fleet pr-lint` | [pull-requests](docs/workspace/pull-requests.md) |
| Writer and review seats run `/simplify` on the slice diff before each push; its refactors go in their own commit. | prose only | [pull-requests](docs/workspace/pull-requests.md) |
| Docs move with the change: the writer updates the pages that own the changed behavior in the same PR; a page left stating the old behavior is a defect. | prose only | [pull-requests](docs/workspace/pull-requests.md) |
| Tests earn their CI time: remove or merge a test whose behavior is gone or pinned by another check; the only guard of a behavior stays. | prose only | [pull-requests](docs/workspace/pull-requests.md) |
| Review comments are resolved during development: after every push fix, reply and resolve each thread, until the PR is clean, approved and has no unresolved thread on its exact head. | `fleet merge-gate` | [pull-requests](docs/workspace/pull-requests.md) |
| Schema changes follow `dbdesign`: constraints, the query each index serves, retention for ledgers, `schema.dbml` with a clean `dbdocs validate`, reversibility, and `created_at`/`updated_at` except on a pure link table. | prose only | [pull-requests](docs/workspace/pull-requests.md) |
| Browser checks use Aside: open your own tab, reuse it across rounds, close it when done, never leak renderers, never touch the product owner's tabs. | prose only | [local-checks](docs/workspace/local-checks.md) |
| Screenshots with personal data get a white blur, never black boxes, checked at full size; unredacted originals never enter git. | prose only | [local-checks](docs/workspace/local-checks.md) |
| Never run `git clean -x` here: it deletes ignored task workdirs. | prose only | - |
| Throwaway state dies with its run: containers and volumes removed in the exit trap, scratch under the task dir or a `.cache/`; leftover state is a defect. | prose only | [local-checks](docs/workspace/local-checks.md) |
| The knowledge base is read only through `kb context` and written only through `kb`; generated files are never edited. The gate checks the writes, not the reads. | `kb gate` | [kb-and-watch](docs/workspace/kb-and-watch.md) |
| One kb committer: `workspace-sync` on the org's `kb_host` runs `kb gate` and commits the tree only on exit 0. Never launch a watch rig on another machine. | `kb gate`, `workspace-sync` | [kb-and-watch](docs/workspace/kb-and-watch.md) |
| Watch seats never post, reply, react or run `git`; a seat stopped by a `kb` failure writes nothing by hand and queues one row to the desk. | prose only | [kb-and-watch](docs/workspace/kb-and-watch.md) |
| Archive: the dream proposes and the operator decides; nobody moves, renames or edits a kb page by hand; an archived project keeps its refs, missions and records. The gate catches a hand edit under `kb/`. | `kb gate` | [archive](docs/workspace/archive.md) |
