---
name: pkm-wiki
description: Maintain Altamash's personal PKM wiki as flat CommonMark atomic notes. Use when creating, updating, querying, validating, or organizing wiki notes, SCHEMA.md navigation, source-grounded NotebookLM/Glean answers, or reusable technical concepts in /Users/altamkhan/Workspace/private/pkm.
---

# PKM Wiki

Use this skill for `/Users/altamkhan/Workspace/private/pkm` and compatible PKM repos.

## Voice

- Load `/Users/altamkhan/.codex/plugins/cache/caveman-repo/caveman/0.1.0/skills/caveman/SKILL.md` before PKM work, then use caveman full for wiki content and agent replies.
- Be senior-staff precise: correctness, consistency, clarity.
- Prefer boring, robust structure over clever fragments.
- Ignore broad documentation-writing instincts. Wiki notes are zettels, not docs pages.

## Core Workflow

1. Read local `AGENTS.md` if present; it can refine this skill.
2. Read the caveman skill path listed in Voice and apply full intensity unless user asks otherwise.
3. Inspect `SCHEMA.md` before adding or moving notes.
4. Gather source-grounded facts:
   - Use NotebookLM when relevant notebook/source exists.
   - Use Glean when notebook/source not enough.
   - For GitHub sources, download with `gh` before ingesting.
5. Write or update only atomic notes needed for user ask.
6. Update `SCHEMA.md` when navigation changes.
7. Run quick hygiene checks: no references sections, no wiki-link syntax, no duplicate concept pages.

## Wiki Rules

- Flat wiki: no folders.
- One navigation file: `SCHEMA.md`.
- Note filename: lowercase natural english, space separated, max 5-6 words, `.md`.
- One note = one reusable idea.
- No catch-all pages.
- For multi-note topics, create one hub note.
- `SCHEMA.md` links hub notes only; do not list every leaf/subnote.
- Hub notes are self-contained one-pagers: problem, concept, flow, key facts, and links to focused subnotes.
- Keep a fine line between fragmentation and dumping: create a subnote only when it answers a durable reusable question; keep supporting detail inside the hub when it only explains the hub.
- Subnotes stay atomic and understandable alone; spell abbreviations at first use.
- No references section in wiki notes.
- Use standard Markdown links: `[note title](<note title.md>)`.
- Links should read naturally in prose.
- Include concise code snippets or ASCII diagrams only when they sharpen concept.
- Keep each note crisp. Target 20-80 lines unless topic truly needs more.

## Source Handling

- NotebookLM/Glean citations are for thinking and validation, not final wiki sections.
- If ingesting source into NotebookLM, verify source list after ingest.
- If source fails to ingest, either fetch it locally, summarize from readable copy, or ask user.
- Do not paste long source excerpts into notes.

## Note Shape

Use this shape when useful, not mechanically:

~~~markdown
# note title

One-line concept.

## invariant

- hard rule
- boundary
- failure mode

## use

```code or text if helpful```

Related note: [other concept](<other concept.md>).
~~~

## Schema Shape

Keep `SCHEMA.md` broad and evolvable:

~~~markdown
# schema

Flat wiki. Atomic notes.

## domain

- [hub note](<hub note.md>): broad concept and map to focused subnotes
~~~

Do not make `SCHEMA.md` overly specific to one temporary project unless wiki itself is scoped that way.

## Health Checks

When useful, ask user whether to run wiki health checks:

- inconsistent facts
- missing links
- duplicate concepts
- stale claims that need NotebookLM/Glean/web validation
- notes too long or not atomic

If user asks to run checks, make concrete edits where safe.
