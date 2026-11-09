# Pi seat

Rules for every pi-tui seat, whatever its role. Each has its reason.

- Do not run `rig skill loadout`. On a pi-tui seat it exits 1 ("seat skills are kept for Claude Code and Codex seats"); the launcher already linked yours. Chained after `rig whoami --json &&` it also drops the next command.
- No poll or sleep loop over 60 seconds in a turn. Inbound handoffs wait behind it and the tool timeout kills it. Wait on CI with a bounded `gh pr checks --watch`, or park the row with `rig queue block <id> --on <blocker> --wake-after <duration>` and end the turn.
- Never `pkill -f`, `killall` or any kill by pattern. A pattern matches every command line that contains it: on Linux your own shell (exit 143, the rest of the command lost), anywhere another process (a pattern kill once stopped a board server another seat ran). Start it with `cmd & pid=$!`, then `kill "$pid"`.
- Text with backticks, `$(` or quotes goes through `--body-file <file>` or a single-quoted heredoc, never inside double quotes on `rig send` or `rig queue create`. The shell runs the backticks, or stops with "unexpected EOF while looking for matching".
- Record proof with `rig proof --workspace <project root> add <NN-slug> --mission <mission> [--slice-id <dot-id>] --artifact-type <t> --verdict <v> --candidate-sha <sha> --money-evidence <line> --file <f>` (or `OPENRIG_WORK_ROOT=<project root>` in place of `--workspace`). `--slice-id` is required when the slice `SPEC.md` has no `id:` (it fails with "missing required C1 field(s): slice" and drops nothing). `--artifact-type` is `guard|qa|rev1-r1|rev1-r2|adjudication`, `--verdict` is `CLEAR|BLOCKING|CONCERNING|PASS|NOT-CLEAR`; nothing else is accepted. `add` has no `--project`; `show` and `judge` do.
- `Reply: rig send captain@global ...` at the foot of a captain message has no session behind it ("Session 'captain@global' not found"). Answer in your pane or by closing the queue row; to ask the operator, queue to your desk.
- After every `rig queue create`, run `rig queue show <id>`. The CLI prints a request id even when the daemon refuses the row. `--human-intent` is for a person (`<operator>@external`); to an agent desk it is refused (`invalid_human_notification`) and nothing is written.
- Never `find /` or scan from the filesystem root: minutes of disk walk, then the tool timeout. Search a known directory, or use `rg`.
- Do not read a file in the same tool batch as the command that creates it. The calls run together, so the read fails with ENOENT; read it on the next turn. Take a worktree path from the queue item, not memory.
