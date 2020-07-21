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
  rebase:
    name: Rebase
    if: github.event.issue.pull_request != '' && (contains(github.event.comment.body, '/rebase') || contains(github.event.comment.body, '/merge'))
    runs-on: ubuntu-latest
    steps:
    - name: Checkout the latest code
      uses: actions/checkout@v2
      with:
        fetch-depth: 0
    - name: Automatic Rebase
      uses: zacsweers/rebase@v2
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        GITHUB_COMMENT: ${{ github.event.comment.body }}
        # Optional, specify if you want it to hide or delete the merge comment
        GITHUB_COMMENT_ID: ${{ github.event.comment.node_id }}
        # Optional, requires GITHUB_COMMENT_ID. Can be either "hide" or "delete". Default is hide
        GITHUB_COMMENT_ACTION: hide
```

The label name must have either "rebase" or "merge" in the name to trigger the appropriate behavior.

## Restricting who can call the action

It's possible to use `author_association` field of a comment to restrict who can call the action and skip the rebase for others. Simply add the following expression to the `if` statement in your workflow file: `github.event.comment.author_association == 'MEMBER'`. See [documentation](https://developer.github.com/v4/enum/commentauthorassociation/) for a list of all available values of `author_association`.
