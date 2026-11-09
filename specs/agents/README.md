# Role specs

Roles are named by member, as OpenRig names them: a seat `pod.member` runs the role `member`. The role dir is what `PI_TUI_ROLE` selects; the model per role is in `fleet/routing.yaml` `pi.profiles`. Every pi-tui seat also reads `pi-seat`.

| Role | Seats | Does |
|---|---|---|
| `lead` | `orch.lead` | Intent, proof contract, routing, ship gate, retro line |
| `build` | `dev.build`, `data.build` | The change: the only writer in its worktree |
| `qa` | `dev.qa`, `data.qa` | The verdict from running the change; scores design fidelity |
| `review` | `dev.review`, `review.rN`, `research.review` | The verdict from reading the diff, bound to the patch-id |
| `design` | `dev.design` | Mockups; never builds or verifies |
| `pm` | `orch.pm` | Briefs, requirements, decks, drafts |
| `advisor` | `orch.advisor` | A second opinion on a plan; edits nothing |
| `writer` | `research.writer` | Research and writing for one named reader; drafts only for publishing and messages |
| `watch` | `watch.inbox`, `watch.meetings`, `watch.tickets` | Org watch collectors |
| `dream` | `watch.dream` | Org watch dream: files the collected events into the knowledge pages |
| `desk` | `desk.<org>` in the conductor rig | One project's desk: watches its rigs, routes their decisions to the operator, keeps a short digest |
| `conductor` | the conductor rig's lead | The operator's front door: reads desk digests and fans questions out to the desks |
| `infra` | an infra rig's writer | IaC, cloud operations, access and CI/CD; counts as a builder for model independence |
| `pi-seat` | every pi-tui seat | Rules for the Pi harness, whatever the role |

A review or QA seat never shares the builder's model family.
