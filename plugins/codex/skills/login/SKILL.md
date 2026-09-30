---
name: login
description: Connect Harbor to your team's workspace with one browser sign-in. Use when the user asks to sign in to, log in to, connect or set up Harbor.
---
# Sign in to Harbor

Run this command in the user's project folder. The script is `../../scripts/harbor.sh`, relative to the folder this SKILL.md is in, so pass its absolute path. It needs the network and writes outside this repo (to `~/.harbor` and to Codex's `config.toml`), so request to run it outside the sandbox. It opens a browser tab for the sign-in and waits for it, so give it a timeout of 600000 ms:

sh <absolute path to scripts/harbor.sh> init --plugin codex

Then show the user the command's output as it is, and tell them to start a new Codex session: Harbor's hooks and tools load when a session starts.

If it says several workspaces are available, ask the user which one to use and run the same command again with `--workspace <id>` added at the end. If it fails for any other reason, show the error and stop.
