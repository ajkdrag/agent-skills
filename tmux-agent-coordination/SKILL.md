---
name: tmux-agent-coordination
description: Drive interactive coding agents through tmux panes and windows. Use when asked to launch Claude Code, Pi or another CLI agent, delegate work, relay messages, have agents review or debate each other, or steer an agent through implementation and verification. Keeps task ownership, model settings, evidence and completion visible without building an agent framework.
---

# Coordinate agents through tmux

Use one agent as coordinator. It gives each participant a focused task, carries useful findings between them and checks the final work. tmux is the terminal transport, not a source of truth about whether work succeeded.

Launch agents from the user's request, not from preconfigured subagent definitions. The user chooses the CLI, model, effort and task naturally; the coordinator fills in missing details when needed. No `scout` registry or agent-role configuration files are required. This is the single workflow for Pi to Pi, Pi to Claude Code or another pair of installed CLIs. It does not depend on a managed subagent tool.

## Choose the layout

- Two parties, including the coordinator: use two panes in the existing window. Split horizontally only when both CLIs remain readable; otherwise split vertically.
- Three or more parties: prefer a named window per worker or reviewer in the same session. Three parties can still use panes on a wide screen, but do not keep subdividing an unreadable terminal.
- Keep the coordinator's terminal intact. Never replace it with the child command.
- Capture the new pane's `%ID`. Target that ID, not a pane index that changes when the layout changes.

One writer and one reviewer is usually enough. Add an agent for an independent task, not to increase the number of opinions.

## 1. Check the environment and assign ownership

```bash
command -v tmux
command -v claude
claude --help
printf 'parent=%s\n' "$TMUX_PANE"
tmux display-message -p '#S:#I.#P'
tmux list-panes -a -F '#{session_name}:#{window_index} #{pane_id} #{pane_current_command} #{pane_current_path}'
git status --short --branch
```

These recipes assume the coordinator is inside tmux. If `TMUX_PANE` is empty, ask the user to attach the coordinator to tmux first; do not guess a target pane.

The examples use Claude Code. For another agent, check that CLI and its help instead. Do not assume a user-requested launcher alias exists in the tool's non-interactive shell. Inspect it in the user's shell first. For a zsh alias named `my-agent`, for example:

```bash
zsh -lic 'whence -va my-agent; whence -va claude'
```

Use the requested launcher and permissions. Do not add permission-bypass flags on your own. Check that the agent supports the requested model and effort, then verify them in its startup banner. If the model is unavailable, ask rather than silently substituting another.

Give each editing task one owner. Two agents must not edit the same files concurrently. Use a read-only reviewer on the same checkout, or separate branches/worktrees for independent implementation. Inspect existing work before creating a worktree; never stash, reset or discard it to make delegation easier.

## 2. Put the task in a file

Examples below use a Bash-compatible coordinator shell. Use a separate scratch directory and environment file for each participant. Tool calls may start a fresh shell each time, so save the tmux targets and paths:

```bash
umask 077
run_dir=$(mktemp -d "${TMPDIR:-/tmp}/agent-dialogue.XXXXXX")
repo=$(pwd -P)
parent=$TMUX_PANE
session=$(tmux display-message -p -t "$parent" '#S')
signal="agent-ready-${run_dir##*/}-1"
printf 'run_dir=%q\nrepo=%q\nparent=%q\nsession=%q\nsignal=%q\n' \
  "$run_dir" "$repo" "$parent" "$session" "$signal" > "$run_dir/tmux.env"
printf 'state_file=%s\n' "$run_dir/tmux.env"
```

Begin later Bash tool calls by sourcing that printed absolute path. Do not expect shell variables from a previous tool call to survive. The environment file contains paths and pane IDs, not credentials.

Write `$run_dir/task.md` with:

- Goal and concrete completion criteria.
- Working directory and files the agent may change. State whether it is read-only.
- Applicable `AGENTS.md` and `CLAUDE.md`, including inherited instructions.
- Actual findings, relevant source/diff paths, commands already run and failures still open.
- Constraints and non-goals. State whether commits, pushes, live queries or deployments are allowed.
- Requested model and effort, when specified.
- A progress/report path, such as `$run_dir/report.md`.
- The parent pane ID and completion signal. Require a completion message after the report is saved, and cleanup of task-owned background processes before completion.

Ask the agent to inspect source before editing, surface unresolved product decisions, run the repository's checks and reread changed files. Keep the task narrow. Do not prescribe a framework before reading the code.

Local terminal agents can read the files you name. A remote model without filesystem access needs the actual source and diff, not just their paths. Never put credentials, PINs, OTPs or tokens in a task file.

## 3. Launch a participant

For two parties, start a sibling shell pane without moving focus. A shell stays open if the agent command fails, so the error remains readable.

```bash
child=$(tmux split-window -h -d -t "$parent" -c "$repo" -P -F '#{pane_id}' \
  "${SHELL:-/bin/sh}")
```

For a larger group, use this window command instead of the split. Choose a window label that describes the requested task:

```bash
child=$(tmux new-window -d -t "$session:" -n agent -c "$repo" -P -F '#{pane_id}' \
  "${SHELL:-/bin/sh}")
```

Both alternatives give you the current participant's pane ID. Save it and inspect the shell:

```bash
printf 'child=%q\n' "$child" >> "$run_dir/tmux.env"
tmux capture-pane -p -t "$child" -S -60
```

Once the shell is ready, send the verified launch command. This example requests Sonnet 5.5 with medium effort; use the agent, model and effort the user requested. Use their launcher alias instead of `claude` when requested and verified.

```bash
tmux send-keys -t "$child" -l 'claude --model claude-sonnet-5-5 --effort medium'
tmux send-keys -t "$child" Enter
```

Capture the agent's startup screen before sending work:

```bash
tmux capture-pane -p -t "$child" -S -60
```

Check the actual model, effort and permission mode. Handle trust or authentication prompts before submitting a task. Let the user enter secrets privately; do not capture or relay secret-entry screens.

## 4. Send messages as terminal input

For a short message, confirm the agent is at its normal input prompt, then send literal text and submit it separately. If a trust or permission dialog is visible, do not send Enter as though it were the task prompt:

```bash
tmux send-keys -t "$child" -l "Read $run_dir/task.md and follow it. Write progress and your final report to $run_dir/report.md."
tmux send-keys -t "$child" Enter
```

Do not paste an untrusted message into a shell prompt. Confirm that the target is the intended agent first. `-l` prevents tmux from treating words as key names; it does not make terminal input safe to execute in a shell.

Prefer sending a file path over pasting a long task. When the text itself must be pasted, use a named buffer and bracketed paste:

```bash
buffer="agent-message-${child#%}"
tmux load-buffer -b "$buffer" "$run_dir/message.md"
tmux paste-buffer -p -t "$child" -b "$buffer"
tmux delete-buffer -b "$buffer"
```

Capture the pane to confirm the message is staged, then send `Enter` in a separate call. Multi-line paste may be staged or submitted differently by different CLIs. Check the screen rather than repeatedly pressing Enter.

While the agent is busy, a message may be queued. Queued does not mean read. Use only that CLI's verified controls to send it immediately, background a tool or interrupt a turn. Never send control keys borrowed from another CLI. For a normal follow-up, waiting for the next turn is usually fine.

## 5. Coordinate review, debate and steering

Use useful parent-side work while the child works: inspect surrounding code, run a baseline, prepare a reproduction or review a completed diff. Check progress at meaningful points, not in a tight polling loop.

```bash
tmux capture-pane -p -t "$child" -S -80
```

Read the report file when available. Do not dump an entire terminal transcript into another agent's context.

A review loop can be this small:

1. Ask the author for its short design, assumptions and relevant diff.
1. Give a reviewer the requirements, actual source/diff and known test results. Make it read-only while the author owns the files. Use a stable snapshot or have the author pause edits during the review.
1. Ask for concrete failures, unnecessary complexity and evidence that would settle a disagreement. Request paths, lines and a reproduction, not preferences alone.
1. Relay only the useful findings. Let the author explain constraints or provide a counterexample.
1. The coordinator decides from the evidence. If a product choice remains unresolved, ask the user.
1. Give the writer a precise change request, then rerun the affected check and surrounding suite.

One review and one response are often enough. Stop debating when the requirement is met and the evidence settles the issue. Disagreement does not require splitting the difference.

Steering messages should state what changed, why it matters and the next action. For example:

```text
The baseline suite passed. The reviewer reproduced a timeout that leaves the child alive.
Fix that cleanup path and add one regression. Keep the current API and do not add retries.
Do not edit the unrelated files. Report the exact check you reran.
```

Do not add speculative guardrails just to satisfy a reviewer. Preserve the user's intended scope and the repository's coding style. Prefer a handful of useful tests over a large set of trivial ones.

## 6. Notify the parent when work is ready

Make notification part of the task, not something the coordinator has to guess. Give the child the actual parent pane ID, report path and signal name from the saved state file.

Before marking `READY_FOR_REVIEW`, the child must save its findings, changed files, checks, results and anything unverified. It must stop any temporary servers or workers it started, or explicitly hand them over with their PIDs and purpose. Write the final report before sending the notification.

The child can signal completion without touching the parent's input:

```bash
tmux wait-for -S "$signal"
tmux display-message -d 10000 -t "$parent" "Agent $child ready: $run_dir/report.md"
```

Do not type a completion message into the parent's input automatically. It can overwrite or submit a user's draft. Use a CLI's dedicated message queue only if it actually provides one and its behavior has been verified. A tmux status message alone does not wake an LLM. These are cooperative notifications, not universal harness-level callbacks.

The parent can work on something else while the child runs. When genuinely blocked on that child, wait on its completion signal rather than repeatedly polling:

```bash
tmux wait-for "$signal"
```

A signal sent before the wait is retained until consumed. Use a new signal name for every new delegation or revision round, and pass it to the child before that round starts. Reusing an old signal can make a later wait return before the new work finishes.

Keep waits bounded through the calling tool's timeout. A timed-out wait does not mean the task failed or that the child stopped. Check its report before cancelling it. A blocked child should send a concise question and mark its report `BLOCKED`, not pretend completion.

## 7. Verify completion and clean up

A prompt, spinner stopping or a successful agent exit is not proof that the feature works. On the completion notification, the coordinator should:

- Inspect the actual diff and reread the changed files.
- Run the relevant formatter, linter, type checker and tests.
- Rerun the reproduction or a small end-to-end check when applicable.
- Check git status for unrelated changes and unexpected generated files.
- Report only outcomes actually observed. Keep test failures separate from setup or network failures.

After verification, close the spawned pane by default. Do not accumulate finished agent terminals. If the pane is the last one in a task-owned window, tmux removes that window too:

```bash
tmux kill-pane -t "$child"
```

Keep the pane open only for a pending revision, a failed or blocked task, or an explicit user request. If a child disappears before writing a report, inspect its last progress and actual diff; do not treat the missing pane as success. `C-c` may cancel a turn without exiting the CLI. Killing a terminal does not guarantee a remote job or deployment stopped.

Stop only processes and panes created for this task. Do not kill a shared tmux session or server, close the coordinator or touch unrelated windows and shells. Preserve useful reports, remove temporary private logs when no longer needed, and tell the user what remains running. Commit or publish only when requested.
