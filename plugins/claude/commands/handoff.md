---
description: Save where this work stands so any agent, or a teammate, carries on from here
argument-hint: "[@teammate or email]"
---
Save a Harbor Handoff: a note so the person's next session, in any agent, or a teammate's, carries on from exactly here.

If a <handoff_request> block from Harbor is in this turn, follow it exactly and call no tool: Harbor saves the note from your reply when the turn ends.

If there is none (Harbor's hooks are not installed here), call the harbor_handoff tool with action "save": a one-line goal, what is done, the exact next step first, open questions, decisions with their reasons, and the files that matter, plus the git origin, branch, commit and uncommitted files from git. To hand it to a teammate, pass the name or email the person gave as "to".

To pick one up instead, the person says "handoff pick".

The person may have named a teammate: $ARGUMENTS
