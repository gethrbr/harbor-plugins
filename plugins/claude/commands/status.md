---
description: Check that Harbor is connected and working in this repo
---
Run this command with the Bash tool, with a timeout of 300000 ms:

cd / && npx -y "harborloop@0.6.35" doctor --cwd "$OLDPWD"

Show the user the output as it is. If a line has a fix, say which command runs it.
