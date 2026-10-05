# Harbor plugins

Harbor gives your coding agents your team's shared memory: recall on every
prompt, guardrails that block what the team banned, and learnings saved for a
person to review.

Harbor is invite-only for now. Signing in needs a Harbor account: join the
waitlist at https://app.gethrbr.com/waitlist and we will let you in.

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

Cursor keeps the plugin at the commit you added, and `marketplace update` does
not move it. To update, remove the marketplace and add it again:

```
cursor-agent plugin marketplace remove harbor
cursor-agent plugin marketplace add https://github.com/gethrbr/harbor-plugins
```

`/harbor:status` says when the plugin is behind.

## How it works

All three need Node 18 or later: the sign-in runs harborloop 0.5.17 through
`npx`, which puts Harbor's hooks in `~/.harbor/bin`. The plugins' own hooks
only launch those.

Cursor also runs Claude Code's plugins. Inside Cursor, only the Cursor plugin
runs, so nothing runs twice.

Already set up with `npx harborloop init`? You do not need a plugin. If you
install one anyway, its hooks stay quiet while `harbor init`'s are registered.
