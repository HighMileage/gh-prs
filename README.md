# gh prs

Open pull requests for you, your team, a given author, or awaiting your review, as an aligned
table in the terminal. Sort by the `complexity:*` labels so cheap reviews float to the top.

Needs only `gh`, `bash`, and POSIX `sort`/`awk`. No jq or other installs.

## Install

```sh
gh extension install HighMileage/gh-prs
```

To hack on it, clone the repo and run `gh extension install .` inside the clone. gh symlinks
to the checkout, so edits take effect immediately.

## Configure

From inside a clone of one of your organization's repositories:

```sh
gh prs init --team engineering
```

This writes `~/.config/gh-prs/config` (override the path with `$GH_PRS_CONFIG`) using the
current repo's owner as the organization:

```
organization=my-org
team=engineering        # GitHub team slug; its members are "the team"
teammates=alice bob     # optional extra logins, space separated
```

`init` also accepts `--org` and `--teammates "alice bob"`, and refuses to overwrite an existing
file. Without any config, `gh prs` and `gh prs --requested` still work inside a clone by using
the repo's owner as the organization. Only `--team` needs the config.

## Use

```sh
gh prs                           # my open PRs
gh prs --requested               # open PRs requesting my review
gh prs --team                    # open PRs by my team
gh prs --author alice            # open PRs by alice (repeatable, ORed)
gh prs -r -a alice               # PRs by alice awaiting my review
gh prs -r --sort complexity      # cheapest reviews first
gh prs -t -l complexity:low -l area:go-api
gh prs -t --drafts               # include draft PRs, with a Draft column
```

| Flag                   | Meaning                                                           |
| ---------------------- | ----------------------------------------------------------------- |
| `-r`, `--requested`    | PRs where my review is requested                                  |
| `-t`, `--team`         | PRs authored by the configured team or teammates                  |
| `-a`, `--author LOGIN` | PRs by this author; repeatable; narrows `-r`; not valid with `-t` |
| `-d`, `--drafts`       | Include draft PRs (excluded by default) and add a Draft column    |
| `-s`, `--sort KEY`     | `author` (default), `created`, or `complexity`                    |
| `-l`, `--label NAME`   | Only PRs carrying this label; repeatable, all must match          |
| `--org ORG`            | Override the organization for this run                            |

Columns: Title, Author, Complexity, Review, Repo, Created, Link, Labels. Review lists the
distinct states of your own reviews on that PR (`APPROVED`, `CHANGES_REQUESTED`, `COMMENTED`),
blank if you have not reviewed it. Complexity is taken from a `complexity:trivial|low|medium|high`
label; other labels appear in the Labels column. With `--sort complexity`, unlabeled PRs sort
last.

## Notes

GitHub search drops `author:` and similar qualifiers for accounts with a private profile unless
the query is scoped with `repo:`. The extension expands the organization into an explicit
`repo:` list on every run, which costs one extra API call.
