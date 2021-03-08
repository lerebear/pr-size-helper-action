# PR Size Helper action

This action adds [size labels](https://github.com/kubernetes/kubernetes/labels?q=size) to pull requests. If a pull requests is above 1000 lines of additions and deletions, the action will prompt the PR author for more context to help explain the size of the PR. If the author chooses to give additional context, the reason will be tracked along with all others in a digest issue.

The end goal is to proactively capture reasons why pull requests are above 1000 lines of additions + deletions and to index all of those reasons in one easy to find place.

## Usage

Create two workflow files:

`.github/workflows/apply-pr-size-label.yml`

```
name: Apply PR size label

on: pull_request

jobs:
  apply_pr_size_label:
    runs-on: ubuntu-latest
    steps:
      - uses: levindixon/pr-size-helper-action@v1
        env:
          GITHUB_TOKEN: "${{ secrets.GITHUB_TOKEN }}"

```

`.github/workflows/track-large-pr-reasons.yml`

```
name: Track large PR reasons

on: issue_comment

jobs:
  track_large_pr_reasons:
    if: ${{ github.event.issue.pull_request && contains(github.event.comment.body, '!reason') }}
    runs-on: ubuntu-latest
    steps:
      - uses: levindixon/pr-size-helper-action@v1
        env:
          GITHUB_TOKEN: "${{ secrets.GITHUB_TOKEN }}"

```

## Configuration

The following environment variables are supported:

- `IGNORED`: A list of [glob expressions](http://man7.org/linux/man-pages/man7/glob.7.html)
  separated by newlines. Files matching these expressions will not count when
  calculating the change size of the pull request. Lines starting with `#` are
  ignored and files matching lines starting with `!` are always included.

You can configure the environment variables in the workflow file like this:

```yaml
env:
  GITHUB_TOKEN: "${{ secrets.GITHUB_TOKEN }}"
  IGNORED: ".*\n!.gitignore\nyarn.lock\ngenerated/**"
```