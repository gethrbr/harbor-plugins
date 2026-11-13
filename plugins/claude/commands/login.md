---
description: Connect Harbor to your team's workspace with one browser sign-in
---
Run this command with the Bash tool. It opens a browser tab for the sign-in and waits for it, so give it a timeout of 600000 ms:

cd / && npx -y "harborloop@0.6.33" init --plugin claude --cwd "$OLDPWD"

Then show the user the command's output as it is.

If it says several workspaces are available, ask the user which one to use and run the same command again with `--workspace <id>` added at the end. If the sign-in timed out or the user has no Harbor account, tell them they can create one at https://app.gethrbr.com/sign-up (free for up to 2 people, no card) and then run the same command again. If it fails for any other reason, show the error and stop.
