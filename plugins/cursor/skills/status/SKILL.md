---
name: status
description: Check that Harbor is connected and working in this repo. Use when the user asks whether Harbor works, or why it did not recall or block something.
---
# Check Harbor

Run this command in the user's project folder. It needs the network and writes outside this project (to `~/.harbor` and `~/.cursor/mcp.json`), so if your terminal runs in a sandbox, ask to run it outside. Give it a timeout of 300000 ms:

sh "${CURSOR_PLUGIN_ROOT}/scripts/harbor.sh" doctor

If that path did not resolve, the script is `../../scripts/harbor.sh` relative to the folder this SKILL.md is in; pass its absolute path.

Show the user the output as it is. If a line has a fix, say which command runs it.
