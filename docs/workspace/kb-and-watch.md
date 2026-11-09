# Knowledge base and watch

The rule table in the workspace root `AGENTS.md` lists each rule and the check that enforces it; this page keeps the full text.

Two layers (ADR-0004, ADR-0017). `resources.yaml` is the address book: ids, urls and refs. `orgs/<org>/kb/` is the brain, an [Open Knowledge Format](https://github.com/GoogleCloudPlatform/open-knowledge-format) bundle filed by PARA: `inbox/` (one file per day, the entry point for every event), `projects/` (a mission with a goal and a due date), `areas/` (a product and its standard), `resources/` (playbooks, topics, decisions) and `archives/`. `kb/resources/` is not `resources.yaml`.

- **Read** only through `"${PROJECTS_ROOT:-$HOME/projects}/bin/kb" context <org>`, then the same command with a `<page-id>`. Do not browse the tree or open `archives/` unless the task names the archived item.
- **Write** only through `kb`. Collectors use `kb append` (one validated event per call). The dream uses `kb page write`, `kb day close` and `kb proposals add`. Each write is checked against the seat's policy actor and journaled. Indexes, `log.md`, `triage.md` and `## Work` are generated. Never edit them.
- **The contract** (layout, page shapes, anchors, citations, lifecycle, who writes what) is the `okf` skill. Classification and reviews are the `para` skill's. This file does not repeat them.
- **The watch rig.** An org with a messaging, meeting or ticket source gets one parked rig, `<org>-watch`, from `specs/rigs/org-watch`: four pi-tui seats on `glm-5.3` through cli-proxy, the collectors `inbox`, `meetings` and `tickets` at medium and the `dream` at high (ADR-0017). The dream is parked at first launch and added by one `rig add` once its commands have passed review (`openrig-workspace` skill). Set-up steps: `openrig-workspace` skill, "Set up an org watch rig". Each seat's watchdog wakes it on its own interval (staggered, `specs/rigs/org-watch/watchdogs/`) to run `kb due <org> <seat>`; on `none` (nothing due, or a collector's last collect is too recent) the seat exits in one line. No seat posts, replies or reacts, and none runs `git`.
- **One committer.** `workspace-sync`, on the org's `kb_host` machine only, runs `kb gate <org>` and then commits `orgs/<org>/kb`.
  - Exit 0: the tree is committed and pushed.
  - Exit 3: nothing in the tree is committed. `fleet route` queues each finding to the seat that owns it, and after 2 failing runs escalates to `desk-<org>@conductor`.
  - Exit 4: a lock is held, so the tree is skipped this round.
  - Never launch a watch rig on any other machine.
- **A seat that cannot continue** after a `kb` failure stops, writes nothing by hand and sends one queue row to `desk-<org>@conductor` with the command and exit code.
- **Sources and access.** Read history or search first. If that fails, use `gog` for Gmail, Drive and Google Chat, then Aside on the account in `resources.yaml` (`aside guide` first; a conversation is opened only to read it). Accounts, channels and DMs are data in `resources.yaml`. Privacy rules are in the org `AGENTS.md`.
- Never put secrets, transcripts, raw chat, client exports or screenshots in the tree. A Drive file a page needs goes under `$HOME/.share/openrig/<org>/raw/` and is cited from the page.
