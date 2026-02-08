# Vouched for

GitHub Action that wraps [vouch](https://github.com/mitchellh/vouch): it checks whether a GitHub user is in your vouch list and fails the job if the user is not vouched or is denounced.

## Usage in other repos

1. In your repo, add a **VOUCHED.td** (or `.github/VOUCHED.td`) with one GitHub username per line (see [vouch file format](https://github.com/mitchellh/vouch#vouched-file-format)).

2. In your workflow, call this action with `user` and `ref`. The action checks out the repo at `ref` so it uses the target branch’s VOUCHED file (for PRs, pass the base branch).

```yaml
on:
  push:
    branches: [main, master]
  pull_request_target:
    types: [opened, reopened]

jobs:
  vouched-for:
    runs-on: ubuntu-latest
    steps:
      - name: Check user is vouched
        uses: YOUR_ORG/vouch-action@main
        with:
          user: ${{ github.event.pull_request.user.login || github.actor }}
          ref: ${{ github.event.pull_request.base.ref || github.ref }}
```

Replace `YOUR_ORG/vouch-action` with your fork or the repo that hosts this action.

## Inputs

| Input   | Required | Description |
|--------|----------|-------------|
| `user` | Yes      | GitHub username to check (e.g. PR author or pusher). |
| `ref`  | No       | Ref to checkout (e.g. `github.event.pull_request.base.ref \|\| github.ref`). When set, the action checks against this ref’s VOUCHED file. |

## Behaviour

- Checks out the repository at the given `ref` (if provided), then installs Nushell and clones [mitchellh/vouch](https://github.com/mitchellh/vouch) and runs vouch’s `check` command.
- Looks for **VOUCHED.td** or **.github/VOUCHED.td** in the repository root (the checkout).
- Exit codes: **0** = vouched (pass), **1** = denounced (fail), **2** = unknown (fail).

## This repo

The **vouched-for** workflow ([.github/workflows/vouched-for.yml](.github/workflows/vouched-for.yml)) in this repo calls the local action (`uses: ./`) with `ref` so the check runs against the target branch’s VOUCHED file on push and pull requests.
