# GitHub action to automatically rebase or merge PRs

## This is a fork

I made some changes here to support merging instead of just rebasing. Feature request is currently open on the original repo here: https://github.com/cirrus-actions/rebase/issues/56

After installation simply add a label containing `rebase` or `merge` to trigger the action. This will perform the action and then remove the label.

# Installation

To configure the action simply add the following lines to your `.github/workflows/rebase.yml` workflow file:

```yml
on:
  pull_request:
    types: [labeled]
name: Automatic Merge
jobs:
  merge:
    name: Merge
    if: contains(github.event.pull_request.labels.*.name, 'merge-pls') || contains(github.event.pull_request.labels.*.name, 'rebase-pls')
    runs-on: ubuntu-latest
    steps:
      - name: Dump GitHub context
        env:
          GITHUB_CONTEXT: ${{ toJson(github) }}
        run: echo "$GITHUB_CONTEXT"
      - name: Checkout the latest code
        uses: actions/checkout@v2
        with:
          fetch-depth: 0
      - name: Automatic Rebase
        uses: zacsweers/rebase@9b5b45f10f297d69269ad4734a7cdd87b71cd7ce
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUBTOKEN }}
          GITHUB_LABEL: ${{ github.event.label.name }}
          GITHUB_LABEL_ID: ${{ github.event.label.node_id }}
          GITHUB_PR_ID: ${{ github.event.pull_request.node_id }}
```

The label name must have either "rebase" or "merge" in the name to trigger the appropriate behavior.

## Restricting who can call the action

It's possible to use `author_association` field of a comment to restrict who can call the action and skip the rebase for others. Simply add the following expression to the `if` statement in your workflow file: `github.event.comment.author_association == 'MEMBER'`. See [documentation](https://developer.github.com/v4/enum/commentauthorassociation/) for a list of all available values of `author_association`.
