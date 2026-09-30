---
name: status
description: Check that Harbor is connected and working in this repo. Use when the user asks whether Harbor works, or why it did not recall or block something.
---
# Check Harbor

Run this command in the user's project folder. The script is `../../scripts/harbor.sh`, relative to the folder this SKILL.md is in, so pass its absolute path. It needs the network and writes outside this repo (to `~/.harbor` and to Codex's `config.toml`), so request to run it outside the sandbox. Give it a timeout of 300000 ms:

sh <absolute path to scripts/harbor.sh> doctor

Show the user the output as it is. If a line has a fix, say which command runs it.
