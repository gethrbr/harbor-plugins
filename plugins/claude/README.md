# Harbor for Claude Code

Harbor gives Claude Code your team's shared memory. It adds what your
team already knows about the code to the agent's context, blocks the shell
commands your team has banned, and saves what a session learned for a person
on your team to review before any other agent sees it.

Site and guides: [gethrbr.com](https://gethrbr.com) ·
[Quickstart](https://gethrbr.com/docs/quickstart)

Signing in needs a Harbor account, free for up to 2 people with no card.
Create one at https://app.gethrbr.com/sign-up and then sign in.

## Install

```
claude plugin marketplace add gethrbr/harbor-plugins
claude plugin install harbor@harbor
```

Run `/harbor:login` to sign in, once, in your browser.
`/harbor:status` checks the install.

## What this plugin runs

The plugin is one shell script, plus the commands that call it. It ships
no hooks, and the script connects to nothing itself.

- `scripts/harbor.sh` runs only when you use `/harbor:login` or
  `/harbor:status`. It runs `npx -y harborloop@0.6.24`: npm downloads
  harborloop, Harbor's command-line tool (MIT license), from registry.npmjs.org,
  with a compiled build of it for your platform
  (`@gethrbr/harborloop-<platform>`). The version is pinned, and each release
  of this plugin pins the next one.

`/harbor:login` registers Harbor's hooks in `~/.claude/settings.json`,
as `npx harborloop init` does. They run the hooks it installed in
`~/.harbor/bin`, on the events below. `npx harborloop uninstall` takes
them out again.

| Event | Hook | What it does |
|---|---|---|
| `UserPromptSubmit` | `session-context` | recalls your team's context for your prompt |
| `SessionEnd` | `session-report` | reports which recalled facts the session cited |
| `Stop` | `session-capture` | at the end of a turn, may ask the agent to record what it learned |
| `PostToolUse` on Write, Edit, Read, Bash, Grep, mcp__.*__harbor_record_learning, exec, edit, write, read, grep | `session-toolcontext` | recalls context for a file the agent wrote with its Write or Edit tool |
| `PreToolUse` on Bash, Write, Edit, MultiEdit, NotebookEdit, mcp__.*, exec, edit, write, read, grep | `session-toolcontext` | checks a shell command, or a write with the Write or Edit tool, against your team's guardrails, on this machine, and blocks it when a rule says to |
| `SessionStart` | `session-compact` | notes that the context was compacted, so the next recall is sent in full; sends nothing |
| `SubagentStart` | `session-context` | recalls your team's context for your prompt |

This plugin ships no hooks of its own, so there is nothing to approve
in `/hooks` and nothing runs before you sign in. Cursor runs the hooks in
`~/.claude/settings.json` too; while they are there, Harbor's Cursor plugin
stays quiet, so no hook runs twice.

## What the sign-in does

`/harbor:login` opens https://app.gethrbr.com/desktop-auth in your browser
and waits for the answer on a local port (127.0.0.1). Then it:

- keeps your Harbor credential in `~/.harbor/auth.json`, readable only by you;
- adds Harbor's MCP server, https://mcp.gethrbr.com/mcp, with a token of its
  own, to `~/.claude.json`;
- installs Harbor's hooks in `~/.harbor/bin`, and registers them in
  `~/.claude/settings.json`;
- connects the repo you ran it in, which sends that repo's git remote URL to
  Harbor;
- schedules nothing: a prompt starts Harbor's daily check in the background,
  at most once a day.

It sends your repo's committed `CLAUDE.md` or `AGENTS.md` to your team's
review queue only if you answer yes when it asks, in a terminal.

## What leaves your machine

Everything goes to Harbor at https://app.gethrbr.com/v1 or
https://mcp.gethrbr.com/mcp, over HTTPS, with your credential. The hooks send
nothing from a repo you have not connected or have paused. From such a repo,
at most every 10 minutes, they ask Harbor for your connected repos, sending
nothing about the repo you are in.

| When | What is sent |
|---|---|
| Your first prompt; a later one that changes the subject | The prompt as you wrote it, your previous message and the agent's reply (up to 400 and 700 characters), the session ID, the repo's git remote URL and folder path. Harbor answers with the context to add. |
| The agent writes a file with its Write or Edit tool | The file's path and the first 800 characters of the new content, once per file per session. |
| Each prompt, turn end and session end, and at most once a minute while the agent runs tools | The agent's name, the session ID, the event, the repo's git remote URL and branch, and counts of tool calls and blocked commands. |
| A session ends | The session ID and the numbers of the recalled facts the agent cited. |
| A command matches a guardrail | The rule, the program's name (such as `git`), the session ID, the time and counts. Never the command's arguments or paths. |
| The agent calls a Harbor tool | What the agent wrote in the call: a search, or a learning, which waits for a person on your team to approve it. |

The hooks do not mask your prompt or the file excerpt before they send them.
The shell commands the agent runs are checked on your machine and are not sent.

While the agent runs tools, the guardrail check downloads your team's
guardrails into `~/.harbor` in the background, at most every 30 seconds,
sending nothing about the repo.

The daily check renews your credential and sends a machine ID, the hostname,
the OS and its version, and the harborloop version. For each connected repo it
sends the names of the tools the repo uses, the package scripts that run a
command Harbor has a guardrail for, and the deny rules in the repo's committed
Claude Code settings, with any `NAME=value` in a command masked, and only
when they changed. It sends no file contents. It also asks registry.npmjs.org
for the latest harborloop version.

## Turning it off

```
npx -y harborloop off             # this repo: no recall, nothing sent
npx -y harborloop off --capture   # keep the recall, record nothing
npx -y harborloop off --global    # every repo on this machine
npx -y harborloop on              # back on
```

The pause is kept on your machine and read before any request.

To remove Harbor, take the plugin out with `claude plugin uninstall harbor@harbor`, and run:

```
npx -y harborloop uninstall   # the hooks, the MCP entry, ~/.harbor
```

- Privacy policy: https://gethrbr.com/legal/privacy
- Questions: https://gethrbr.com/contact
