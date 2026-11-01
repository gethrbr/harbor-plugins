# Harbor plugins

Harbor gives your coding agents your team's shared memory: recall on every
prompt, guardrails that block what the team banned, and learnings saved for a
person to review.

Site and guides: [gethrbr.com](https://gethrbr.com) ·
[Quickstart](https://gethrbr.com/docs/quickstart)

Signing in needs a Harbor account, free for up to 2 people with no card.
Create one at https://app.gethrbr.com/sign-up and then sign in.

## Claude Code

```
claude plugin marketplace add gethrbr/harbor-plugins
claude plugin install harbor@harbor
```

Then run `/harbor:login` in Claude Code. It signs you in once in the browser.
`/harbor:status` checks the install.

## Codex

```
codex plugin marketplace add gethrbr/harbor-plugins
codex plugin add harbor@harbor
```

Then type `$harbor:login` in Codex. It signs you in once in the browser, and
trusts the plugin's hooks for you, so there is nothing to approve in `/hooks`.
`$harbor:status` checks the install.

## Cursor

```
cursor-agent plugin marketplace add https://github.com/gethrbr/harbor-plugins
```

Then install Harbor from Cursor's plugins page (`/plugins` in the Cursor CLI),
and run `/harbor:login` in a Cursor chat. It signs you in once in the browser.
`/harbor:status` checks the install.

Grok Bot uses Cursor's plugins. Add Harbor there and sign in to the Harbor
server it adds.

Cursor keeps the plugin at the commit you added, and `marketplace update` does
not move it. To update, remove the marketplace and add it again:

```
cursor-agent plugin marketplace remove harbor
cursor-agent plugin marketplace add https://github.com/gethrbr/harbor-plugins
```

`/harbor:status` says when the plugin is behind.

## How it works

All three need Node 18 or later: the sign-in runs harborloop 0.6.19 through
`npx`, which puts Harbor's hooks in `~/.harbor/bin`. The plugins' own hooks
only launch those.

Cursor also runs Claude Code's plugins. Inside Cursor, only the Cursor plugin
runs, so nothing runs twice.

Already set up with `npx harborloop init`? You do not need a plugin. If you
install one anyway, its hooks stay quiet while `harbor init`'s are registered.

## Guides

- [Why does Claude ignore my CLAUDE.md?](https://gethrbr.com/blog/why-claude-ignores-claude-md)
- [AGENTS.md vs CLAUDE.md: which tools read which](https://gethrbr.com/blog/agents-md-vs-claude-md)
- [Claude Code memory vs Codex, Cursor and Copilot memory](https://gethrbr.com/blog/claude-code-memory-vs-codex-cursor-copilot)
- [Codex skills, and sharing them with Claude Code and Cursor](https://gethrbr.com/blog/codex-skills)
- [We checked 3,905 CLAUDE.md and AGENTS.md files on GitHub](https://gethrbr.com/blog/claude-md-agents-md-github-study)
