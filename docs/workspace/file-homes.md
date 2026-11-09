# File homes

The rule table in the workspace root `AGENTS.md` lists each rule and the check that enforces it; this page keeps the full text.

Four homes. Pick by whether a later reader still needs the file after the rig is gone.

- Record, in git: `SPEC.md`, `PROGRESS.md`, `PROOF.md`, `NOTES.md`, `resources.yaml`, `contacts.yaml`, and `orgs/<org>/kb/`. This is what `workspace-sync` carries to every machine.
- Cited captures, not git: `$HOME/.share/openrig/<org>/<project>/<mission>/`. Screenshots, long logs, raw JSON, HTML, recordings. Survives a rig hard-delete. No credentials, `.env`, or key files in that tree. Secrets stay in the existing files directly under `$HOME/.share/`, outside `openrig/`.
- A `PROOF.md` that depends on a capture names it in one line, with no personal home prefix: `evidence: ~/.share/openrig/<org>/<project>/<mission>/<file> host:<machine> sha256:<hex>`. Another machine copies it with `scp` from that host and checks the hash. `host` is a machine id from `fleet/machines.yaml`.
- Rig scratch, gitignored, dies with the rig: `tasks/`, `.worktrees/`, `.workbench/`. Copy a conclusion into the record, or the file into `~/.share/openrig/`, before the rig is hard-deleted. Anything that lived only in the task dir is gone when the rig is.
- Exhaust, gitignored: `exhaust/`, and `csv`, `xlsx`, `parquet`, `jsonl` under `orgs/`. A number worth keeping is a sentence in the record.
- Cache, gitignored: any `.cache/` directory, written by the script beside it as `.cache/<tool>/`. Rebuildable from a live source, never cited by a `PROOF.md`, never copied between hosts (rerun the script). If the source can expire, copy what a conclusion rests on to `~/.share/openrig/` as a capture.
- Proof images already tracked in git stay there until a later change copies one mission to `~/.share/openrig/`, points `PROOF.md` at the copy, and then drops the blob. Do not move that history in the same change as this rule. New images and video under `orgs/` are gitignored so they do not become the next copy of that pile.
- Record outcomes and evidence links in the slice's `PROGRESS.md`. No secrets, transcripts, or client data exports in this repo.

## Who commits records

- A seat writes its records (`SPEC.md`, `PROGRESS.md`, `PROOF.md`, `NOTES.md`, `reports/`) in the live checkout, at their place under `orgs/<org>/projects/<project>/`. `workspace-sync` on the project's host commits them; a seat never does.
- A worktree carries code, specs and docs only. Never commit a record from a worktree: the sync commits the same file from the live checkout, and the two commits meet as a conflict on the next pull.
- When it happens anyway, `fleet sync-commit integrate` (the pull step of `workspace-sync`) resolves a conflict on a record without a person:
  - An append log (`PROGRESS.md`, `NOTES.md`, any `desks/*.md`) keeps the union of both sides (`git merge-file --union`).
  - Any other record (a project's `missions/**.md`, `desks/**.md` or `resources.yaml`) keeps the side committed later; a tie keeps the remote's. The sync log names the older side's blob and commit, so `git show <blob>` reads it back.
  - Anything else stops the round and names the paths: code, specs, docs, any `AGENTS.md`, `repos.yaml`, `project.yaml`, task dirs, kb, and non-markdown files under `missions/`.
