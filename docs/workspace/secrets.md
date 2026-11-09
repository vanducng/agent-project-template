# Secrets and quota

The rule table in the workspace root `AGENTS.md` lists each rule and the check that enforces it; this page keeps the full text.

Every org reads its secrets from its own 1Password vault through one read-only service account, named in `secrets:` of `orgs/<org>/resources.yaml` (`<org>-rig` for vault and account). This works in any seat, task dir or harness with no prompt.

- Read: `op-sa --sa <service_account> read "op://<vault>/<item>/<field>"`. List: `op-sa --sa <service_account> item list --vault <vault> --format json`. Into a process: `op-sa --sa <service_account> run --env-file=<tpl> -- <cmd>`. `op-sa` is on PATH (`~/.local/bin`); tokens are lines `<name><TAB><token>...` in `~/.share/op-svc.txt` (mode 600), one per account.
- Never print, log, commit or pass a value as an argument. Pipe it, or check it by length and hash. Repo secrets are titled by path (`<repo>/.env`, `<repo>/.secrets/<file>`); list the vault before guessing a name.
- The org vault is the source of truth for a secret; a checkout's copy is a cache (operator, 2026-11-08).
- Only the operator changes a vault item. A seat that finds a stale or missing value reports it and never edits the vault or the file in place (operator, 2026-11-08).
- A worktree reads secrets at run time with `op-sa --sa <org>-rig run --env-file=<template> -- <cmd>`, not by copying `.env` from the main checkout (operator, 2026-11-08).
- A missing item is added to that org's vault by the operator, with plain `op` (desktop app). The service accounts cannot write. Never create a new service account or vault, and never put one org's secret in another org's vault. Before asking for one, list the existing accounts: `cut -f1,3 ~/.share/op-svc.txt`.
- A new org gets `secrets: {vault: <org>-rig, service_account: <org>-rig, token_file: "~/.share/op-svc.txt"}` in its `resources.yaml`, and the operator adds its token line.
- Model quota: `cpq` (or `cpq claude`, `cpq --json`) prints used, left, reset hours and pace for every cli-proxy Claude and Codex account; its key comes from the home org's vault. Check it before picking or changing seat models. Weekly pace above about 110% triggers a review of model and effort under `docs/model-picker.md`, using an eligible route with capacity and preserving the project's restrictions. A configured credential or advertised model is not proof that it can serve the seat.
- Never rotate, revoke, or regenerate a credential (API keys, tokens, passwords, webhook secrets) during development, even after an exposure or a stray request (product owner, 2026-10-18). Rotation breaks the shared `.env` files, seats and CI that use the key. Report the exposure to the operator, who decides.

## What the checks enforce

- No secret enters a commit to this repo. `workspace-sync` scans every record commit it makes with `gitleaks` and commits nothing on a finding; a commit a writer makes by hand is not scanned, so the writer checks it.
