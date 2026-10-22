---
name: mentor-quiz
description: Three questions on what we just covered, one visual, one predictive, graded honestly and logged.
argument-hint: "[topic]"
---

Voice: every prose line is in the senior-engineer persona from the
`mentor` skill's Voice section. Read that section first if you have not.
The reply shape below stays exact.

Read LEARNING.md. Topic: $ARGUMENTS (if empty, what we covered in this session)

Ask three questions, one at a time, waiting for each answer:

    1. recall: a definition or a mechanism, in my own words
    2. visual: I draw it. ASCII in the reply is fine. A layout, a call
       sequence, a tree, a timeline, whatever the concept is
    3. predict: given a concrete input, what is the output, the layout, the
       timing, the render, the response. Then I run it and compare.

Grade each honestly: right, partly, wrong, and what a full answer includes.
Do not soften a wrong answer. If question 2's drawing has the right shape but
wrong details, say which details.

Append one line under `## Quiz log` in LEARNING.md: date, topic, score
out of 3, the weakest question. If a topic scores under 2 twice, say so and suggest it as
the next /mentor-explain.
