# agent-skills

My personal agent skills, installable with [Skills CLI](https://skills.sh/).
The skills can be unrelated. Install only the ones you want.

## Install and update

List the skills without installing them:

```bash
npx skills add ajkdrag/agent-skills --list
```

Install just tmux coordination, globally, for Claude Code and Codex:

```bash
npx skills add ajkdrag/agent-skills -g \
  --skill tmux-agent-coordination --agent claude-code codex
```

The CLI also supports Pi. Choose your agents interactively, or pass `--agent pi`.
Use `--agent '*'` to install to all supported agents, and `--skill '*'` to select
all skills. Add `-y` to skip the confirmation prompts.

Update installed skills without installing the rest of the collection:

```bash
npx skills update -g
# Or update one installed skill:
npx skills update -g tmux-agent-coordination
```

This repository is private. Use your existing Git/GitHub authentication; do not put
tokens in install commands. SSH URLs work too when SSH access is available.

## Skills

| Skill                     | What it does                                                                   |
| ------------------------- | ------------------------------------------------------------------------------ |
| `tmux-agent-coordination` | Launch, message, review and steer CLI agents through tmux panes and windows    |
| `marp-deck`               | Marp + MarpX slide decks in my style, with overflow checks and a starter theme |
| `excalidraw-diagram`      | Excalidraw diagrams, my palette and a local PNG render loop                    |
| `pkm-wiki`                | Maintain my personal PKM wiki notes                                            |
| `mentor`                  | Start or resume a learning session, tracked in `LEARNING.md`                   |
| `mentor-next`             | Assign the next task with a done criterion and a quick check                   |
| `mentor-review`           | Review my code without patching it                                             |
| `mentor-hint`             | Give one step up the hint ladder, not the fix                                  |
| `mentor-explain`          | Explain a concept by mechanism and an experiment                               |
| `mentor-walkthrough`      | Walk through pasted code, asking me to predict each step                       |
| `mentor-quiz`             | Ask three questions and record the results                                     |
| `mentor-retro`            | Review progress and set the next week's work                                   |

## Mentor set

The mentor skills refer to each other. Install all eight for the complete workflow,
without installing the other skills:

```bash
npx skills add ajkdrag/agent-skills -g \
  --skill mentor mentor-next mentor-review mentor-hint \
  mentor-explain mentor-walkthrough mentor-quiz mentor-retro
```

The mentor persona is deliberately blunt. Say "plain mode" or "drop the act" to
turn it off. The voice is defined in `mentor/SKILL.md`.

These skills were copied from [mentor-skills](https://github.com/ajkdrag/mentor-skills).
That repository is unchanged. Each copied skill includes its upstream MIT license.
Existing installations still point to their recorded source; installing from this
collection switches the selected skills to this source. Review local edits first.

## Notes

- `excalidraw-diagram/references` needs a one-time `uv sync` and
  `uv run playwright install chromium` before the render loop works.
- Each skill lives in its own directory with a `SKILL.md`. No shared topic or
  package manifest is required for selective installation.
- Edit skills here, push, then run `npx skills update -g` on each machine.
