# agent-skills

My personal agent skills, installable into any agent (claude code, cursor,
codex, pi) via [skills.sh](https://www.skills.sh/).

```bash
npx --yes skills add ajkdrag/agent-skills -g --skill '*' --agent '*'
npx --yes skills update    # pull latest after a push here
```

## Skills

| skill | what it does |
| --- | --- |
| `marp-deck` | Marp + MarpX slide decks in my style, overflow-checked, with a starter theme |
| `excalidraw-diagram` | Excalidraw diagrams that argue visually, my palette, local PNG render loop |
| `pkm-wiki` | maintains my personal PKM wiki notes |

## Notes

- `excalidraw-diagram/references` needs a one-time `uv sync` and
  `uv run playwright install chromium` before the render loop works.
- Edit skills here, push, then `skills update` on each machine.
