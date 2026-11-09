# Archive

The rule table in the workspace root `AGENTS.md` lists each rule and the check that enforces it; this page keeps the full text.

A finished project stays findable and leaves the set a seat reads on a wake. `resources.yaml` keeps every id and url. Do not delete a ref.

- **Three levels** (ADR-0015): a Project (its knowledge page moves, and still-true Facts are harvested to its Area), a Resource, and an Area (a product: every host is touched). An archived page moves to `archives/<bucket>/` with `status: deprecated`. Archived ids are never filing targets: a rule hit on one goes to triage, and the operator's word for it is `restore <id>`.
- **The dream proposes, the operator decides.** The dream adds rows to `archives/PROPOSALS.md` (a finished Project weekly, a Resource or Area monthly). Nothing moves without the operator's yes. Archive, restore and retitle (`kb archive|restore|retitle <id> --plan|--apply`) may come later in your kb tool: until then an approved item waits in `PROPOSALS.md`. Nobody moves, renames or edits a page by hand. An unjournaled change under `kb/` fails the gate and stops that org's commits.
- **An Area archive** needs every mission `done` or `dropped`, no rig for the project, no open desk row, no inbox events for 60 days, no open Jira issue under its `## Live state`, and the operator's yes.
- On the project's `resources.yaml`, set `status: archived`. The refs stay, so a `source_ref` still resolves. `workspace.yaml` keeps the project entry so the path still resolves.
- Leave `missions/` in place. `SPEC.md`, `PROGRESS.md` and `PROOF.md` are the record. The project `AGENTS.md` says it is archived and names the archived Area page. It does not list closed missions as current work.
- Retire the project's conductor desk: drain its open rows, keep its digest, then `rig remove` and drop it from the conductor roster (`openrig-workspace` skill, `references/conductor.md`, Archive a desk).
- On the project's host, remove the task dir and worktrees only after each worktree is clean and fully pushed (no uncommitted or untracked files, no commits ahead of upstream, no stash naming its branch) and the captures its `PROOF.md` files cite are copied to `~/.share/openrig/`. Never force a removal. Keep `~/.share/openrig/<org>/<project>/`. Do not start a rig for an archived project.
- A seat reads an archived item only when its task names it.
