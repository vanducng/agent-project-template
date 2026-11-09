# demo

A synthetic project: an export job in `demo-app` that retries failed deliveries. It shows one mission with one slice and its records.

## Repos

| Repo | Role here |
|---|---|
| `demo-app` | the export job and its tests |

## Rules

- Project resources (channels, meetings, dashboards, docs): `resources.yaml` in this folder, pointing into the org's `../../resources.yaml` by id. Data only, never secrets.
- Before changing a project, mission, slice, task dir, worktree, rig or seat, load the `openrig-workspace` skill.
