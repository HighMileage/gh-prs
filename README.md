# gh prs

Open pull requests for you, your team, or awaiting your review, as a table in the terminal.
Needs only `gh` and `awk`.

## Install

```sh
gh extension install <org>/gh-prs
```

For local development, run `gh extension install .` inside a clone; gh symlinks to the
checkout so edits are live.

## Configure

From inside a clone of one of your org's repos:

```sh
gh prs init --team engineering
```

This writes `~/.config/gh-prs/config` (or `$GH_PRS_CONFIG`) with the current repo's owner as
the organization:

```
organization=my-org
team=engineering        # GitHub team slug; its members are the team
teammates=alice bob     # optional extra logins
```

Without a config file, `gh prs` and `gh prs --requested` still work inside a clone, using the
repo's owner as the organization. Only `--team` needs the config.

## Use

```sh
gh prs                           # my open PRs
gh prs --requested               # open PRs requesting my review
gh prs --team                    # open PRs by my team
gh prs --author alice            # open PRs by alice (repeatable)
gh prs -r -a alice               # PRs by alice awaiting my review
gh prs -r --sort complexity      # cheapest reviews first
gh prs -t -l complexity:low -l area:go-api
gh prs -t --drafts               # include draft PRs, with a Draft column
```

Draft PRs are excluded unless `--drafts` is given.

`--sort` accepts `author` (default), `created`, or `complexity`. Complexity comes from
`complexity:trivial|low|medium|high` labels; unlabeled PRs sort last.
