# agent-project-template

A workspace layout for running teams of coding agents ([OpenRig](https://www.openrig.dev) rigs) across several orgs and projects. Code stays in each product repo; this tree holds the context, the rules, the work records and the places where rigs run.

## The layout and why

```
AGENTS.md                      the rules every agent reads, one row each, with what enforces it
workspace.yaml                 every project by <org>.<project> id, its root and its rigs
fleet/                         machines, model routing and authority defaults
specs/rigs/<template>/         rig templates: solo, workshop, factory, studio, org-watch
specs/agents/<role>/           role guidance, selected by a seat's PI_TUI_ROLE
docs/workspace/                the long form of the rules in AGENTS.md
orgs/<org>/                    org rules, repos, resources and contacts
  projects/<project>/          project rules, repos (with the one host it runs on), resources
    missions/<mission>/        an outcome with an owner and a date
      slices/<nn>-<slug>/      the unit of work: SPEC (intent, requirements, proof contract), PROGRESS, PROOF, proof/
    tasks/<task>/              where a rig runs: its brief (AGENTS.md) and rig.yaml; gitignored scratch
.worktrees/<repo>-<slice-id>/  one git worktree per repo per slice; gitignored
```

- **Rules live in files, not in prompts.** Every harness that starts below this directory reads `AGENTS.md` here, then the org's and the project's. The most specific written rule wins.
- **The slice is the unit of work.** One outcome, one owner seat, one worktree per repo it touches, one PR by default, and a proof contract written before the build. Records sit next to the project, so `rg <slice-id>` finds the record, the worktrees, the branches and the artifacts.
- **One machine per project.** `host:` in the project's `repos.yaml` names it; its rigs, task dirs and worktrees exist only there.
- **Independence.** A review or QA seat never shares the builder's model family (`fleet/routing.yaml` `families`).

`orgs/example/projects/demo` is a synthetic project with one mission and one finished slice: SPEC with a proof contract, PROGRESS with `<seat>:` lines and a `retro:` line, PROOF, and a sample review verdict in `proof/`. Its `tasks/demo/` shows a team rig's brief and `rig.yaml`.

## Use this template

1. On GitHub, **Use this template** (or `gh repo create <owner>/<name> --template <this repo> --private --clone`). Keep it private: it will hold your orgs, people and work records.
2. Copy the fleet examples and fill in your machines and models:
   ```bash
   cp fleet/machines.example.yaml fleet/machines.yaml
   cp fleet/routing.example.yaml fleet/routing.yaml
   ```
3. Install the `openrig-workspace` skill (below), then add your first project. `--host` is a machine id from `fleet/machines.yaml`:
   ```bash
   node ~/skills/skills/openrig-workspace/scripts/new-project.cjs <org> <project> --host <machine id> --root .
   ```
   It writes the project's five files and an empty `missions/`, adds it to `workspace.yaml`, and checks its resources. Copy `orgs/example/` first for a new org.
4. Pick a rig template for the work and start a task dir from it:
   ```bash
   node ~/skills/skills/openrig-workspace/scripts/rig-kind.cjs --list
   node ~/skills/skills/openrig-workspace/scripts/rig-kind.cjs --kind feature --profile medium   # also --kind bugfix, sop or content
   ```
5. Delete `orgs/example/` once your own org is in.

## The openrig-workspace skill

The scripts that create and name things (`new-project.cjs`, `rig-kind.cjs`, `slice-names.cjs`, `check-resources.sh`) and the how-to for projects, slices, worktrees, rigs and seats live in the `openrig-workspace` skill. Clone the skills repo that carries it to `~/skills` (the paths in this tree assume that place):

```bash
git clone <skills repo URL> ~/skills
```

Every role in `specs/agents/` selects that skill, and `AGENTS.md` points agents to it. The scripts need Node 22 and `yq` (mikefarah v4).

## Optional pieces

- **Org watch** (`specs/rigs/org-watch`, the `watch` and `dream` roles, `docs/workspace/kb-and-watch.md`): a parked rig per org that collects messages, meetings and tickets into a knowledge tree. Skip it if you only build.
- **Conductor and desks** (the `conductor` and `desk` roles, `docs/workspace/captain.md`): one rig that routes decisions from every project to the operator. Useful with several orgs; one project can escalate to the operator's pane instead.
- **The bigger rig templates.** `solo` (one writer) is enough to start; `workshop` adds a lead, QA and a reviewer, `factory` adds design, briefs and an advisor, and `studio` is for research and writing.
- **Per-row enforcement.** The `AGENTS.md` table names the checks the original workspace runs. Those tools are not shipped here: the `fleet` and `kb` CLIs, `workspace-sync` (commits records on a schedule), the `fleet/bin` scripts (seat launch, conductor, sending as the operator, PR screenshot checks), the `cpq` quota check, `docs/model-picker.md`, and the ADRs the role and doc pages cite. Until you add your own checks, every row is prose, and those references describe intent rather than a command you can run.
