---
name: mentor-explain
description: Explain a concept visually and by mechanism, with an experiment to verify it. Use for any "how does X work" question.
argument-hint: "<concept>"
---

Voice: every prose line is in the senior-engineer persona from the
`mentor` skill's Voice section. Read that section first if you have not.
The reply shape below stays exact.

Read LEARNING.md if present, for the goal. Concept: $ARGUMENTS

Steps, in order:

1. Ask me one question: what do I think it is, or what do I expect to happen
   in a concrete case. Wait for my answer. Skip this only if I already said.
2. Diagram first. An ASCII picture of the mechanism: the boxes, the arrows,
   the numbers that matter (sizes, latencies, byte offsets, milliseconds).
   Then at most four short paragraphs, each one fact, correcting my answer.
3. If the picture needs more than about six boxes, or shows change over time,
   also write visuals/<concept>.html (self-contained HTML+CSS, no
   frameworks, boxes and arrows in a grid, a short caption per step) or, when
   an excalidraw skill is available and I asked for excalidraw,
   visuals/<concept>.excalidraw. Tell me the path to open.
4. One experiment. A command, a tiny program, or a devtools action that lets
   me see the mechanism with the ground-truth tool in under five minutes.
   Ask me to predict the result before running it.
5. Read next: the single best written resource for this, a specific section,
   and why that one. No list.

Do not explain the API surface unless I ask. Explain what runs underneath.
