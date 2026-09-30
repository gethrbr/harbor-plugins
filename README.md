# Harbor plugins

Harbor gives your coding agents your team's shared memory: recall on every
prompt, guardrails that block what the team banned, and learnings saved for a
person to review.

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

## How it works

Both need Node 18 or later: the sign-in runs harborloop 0.5.9 through
`npx`, which puts Harbor's hooks in `~/.harbor/bin`. The plugins' own hooks
only launch those.

Already set up with `npx harborloop init`? You do not need a plugin. If you
install one anyway, its hooks stay quiet while `harbor init`'s are registered.
