---
name: login
description: Connect Harbor to your team's workspace with one browser sign-in. Use when the user asks to sign in to, log in to, connect or set up Harbor.
---
# Sign in to Harbor

Run this command in the user's project folder. It needs the network and writes outside this project (to `~/.harbor` and `~/.cursor/mcp.json`), so if your terminal runs in a sandbox, ask to run it outside. It opens a browser tab for the sign-in and waits for it, so give it a timeout of 600000 ms:

sh "${CURSOR_PLUGIN_ROOT}/scripts/harbor.sh" init --plugin cursor

If that path did not resolve, the script is `../../scripts/harbor.sh` relative to the folder this SKILL.md is in; pass its absolute path.

Then show the user the command's output as it is, and tell them to start a new chat: Harbor's hooks and tools load when a chat starts.

If it says several workspaces are available, ask the user which one to use and run the same command again with `--workspace <id>` added at the end. If the sign-in timed out or the user has no Harbor account, tell them Harbor is invite-only for now and they can join the waitlist at https://app.gethrbr.com/waitlist. If it fails for any other reason, show the error and stop.
