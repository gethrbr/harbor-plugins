---
name: harbor
description: Your team's shared memory for coding work. Use before starting a task in a repo, before guessing at a team convention, file, decision or config value, and when the user corrects you or states a rule the next agent should know.
---
# Harbor

Harbor holds what your team's agents learned and a person approved: conventions, past decisions, gotchas.

- Before starting a task, call `harbor_get_knowledge` with a one-line description of it. Call it again whenever you are about to guess.
- When the user corrects you, states a rule, or a surprise costs you time, call `harbor_record_learning` with one self-contained sentence and the reason. A person reviews it before the team sees it.
- If the Harbor tools are missing, ask the user to run `/harbor:login`.

The Harbor server's own instructions describe each tool in full. Follow them.
