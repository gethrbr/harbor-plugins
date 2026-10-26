# Harbor for Cursor

Harbor gives Cursor your team's shared memory. It adds what your
team already knows about the code to the agent's context, blocks the shell
commands your team has banned, and saves what a session learned for a person
on your team to review before any other agent sees it.

Harbor is invite-only for now, so signing in needs a Harbor account. Join the
waitlist at https://app.gethrbr.com/waitlist and we will let you in.

## Install

```
cursor-agent plugin marketplace add https://github.com/gethrbr/harbor-plugins
```

Then install Harbor from Cursor's plugins page (`/plugins` in the Cursor CLI).

Run `/harbor:login` to sign in, once, in your browser.
`/harbor:status` checks the install.

## What this plugin runs

The plugin is two shell scripts, plus the skills that call them. Neither
script connects to anything itself.

- `scripts/harbor.sh` runs only when you use `/harbor:login` or
  `/harbor:status`. It runs `npx -y harborloop@0.6.11`: npm downloads
  harborloop, Harbor's command-line tool (MIT license), from registry.npmjs.org,
  with a compiled build of it for your platform
  (`@gethrbr/harborloop-<platform>`). The version is pinned, and each release
  of this plugin pins the next one.
- `hooks/run.sh` runs on the events below. It runs the matching hook that
  `/harbor:login` installed in `~/.harbor/bin`, with the event's input.
  Before you sign in there is no hook there, so it only says, when a session
  starts, how to sign in. It also stands down where `harbor init` registered
  Harbor's hooks itself, in ~/.cursor/hooks.json or ~/.claude/settings.json, so no hook runs
  twice.

| Event | Hook | What it does |
|---|---|---|
| `sessionStart` | `session-context` | recalls your team's context for the repo when a chat opens |
| `beforeSubmitPrompt` | `session-context` | recalls your team's context for your prompt |
| `sessionEnd` | `session-report` | reports which recalled facts the session cited |
| `stop` | `session-capture` | at the end of a turn, may ask the agent to record what it learned |
| `postToolUse` on Write | `session-toolcontext` | recalls context for a file the agent wrote with its Write or Edit tool |
| `preToolUse` on Shell, Write, ^MCP: | `session-toolcontext` | checks a shell command, or a write with the Write or Edit tool, against your team's guardrails, on this machine, and blocks it when a rule says to |
| `preCompact` | `session-compact` | notes that the context was compacted, so the next recall is sent in full; sends nothing |

The plugin also adds Harbor's MCP server, https://mcp.gethrbr.com/mcp, which Cursor
shows as `plugin-harbor-harbor`. It holds no credential: Cursor asks you to
sign in with your Harbor account the first time, and keeps that sign-in itself.
Grok Bot uses Cursor's plugins, so this is how a Bot connects to Harbor. On
your own machine `/harbor:login` adds a Harbor server of its own beside the
hooks, so you can leave `plugin-harbor-harbor` signed out there. Signed in to
both, the agent sees each Harbor tool twice.

Cursor keeps the plugin at the commit you added it from. To update, remove
the marketplace and add it again; `/harbor:status` says when the plugin is
behind.

## What the sign-in does

`/harbor:login` opens https://app.gethrbr.com/desktop-auth in your browser
and waits for the answer on a local port (127.0.0.1). Then it:

- keeps your Harbor credential in `~/.harbor/auth.json`, readable only by you;
- adds Harbor's MCP server, https://mcp.gethrbr.com/mcp, with a token of its
  own, to `~/.cursor/mcp.json`;
- installs Harbor's hooks in `~/.harbor/bin`;
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
| A chat opens, with no prompt yet; your first prompt; a later one that changes the subject | The prompt as you wrote it, your previous message and the agent's reply (up to 400 and 700 characters), the session ID, the repo's git remote URL and folder path. Harbor answers with the context to add. |
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

To remove Harbor, take the plugin out on Cursor's plugins page, and run:

```
npx -y harborloop uninstall   # the hooks, the MCP entry, ~/.harbor
```

- Privacy policy: https://gethrbr.com/legal/privacy
- Questions: https://gethrbr.com/contact
