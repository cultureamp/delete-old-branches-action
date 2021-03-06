# Delete old branches action

This repo is public for technical reasons, it's unlikely to be helpful to anyone.

This repo contains a github action that will delete branches that haven't had a commit in over `DAYS_TO_KEEP_BRANCHES`, unless there is or will be less than `MIN_NUM_BRANCHES` of branches in the repo. This is useful for one uncommon quirk in one particular deployment, but is probably a bad idea to add this anywhere else.


## Using this action

Create a workflow file `.github/workflows/cleanup-old-branches.yml`, `repo-token` is required, the other two inputs are optional and will default to the below values. Make sure you use the latest version of this action.

```yaml
name: cleanup-old-branches
on: [push]
jobs:
  cleanup-old-branches:
    runs-on: ubuntu-latest
    steps:
      - uses: cultureamp/delete-old-branches-action@v0.3
        with:
          repo-token: ${{ secrets.GITHUB_TOKEN }}
          min-num-branches: 20
          days-to-keep-branches: 14
```

It could also be run on a schedule using POSIX cron syntax. According to the GitHub docs scheduled workflows don't actually run on a schedule, they need to be triggered by a commit to the default or base branch. So they're more like rate limited workflows.

```yaml
name: cleanup-old-branches
on:
  schedule:
    - cron "30 2 * * *"
    # will run every day at 2:30am UTC (12:30pm AEST/1:30pm AEDT)
jobs:
  ...
```

### More info
- https://docs.github.com/en/free-pro-team@latest/actions/reference/workflow-syntax-for-github-actions
- https://docs.github.com/en/free-pro-team@latest/actions/reference/events-that-trigger-workflows#schedule

## Developing

`yarn install` to get started. On every commit `yarn build` will run and build a no dependancy version of the distributable that github can read (the other option is checking all of node modules in the repo - we can't run `yarn install` in someone else's pipeline).

When you want to make new version available run `git tag -a -m "tag message" v0.1` with an appropriate tag message and version number. This version number is what is used in the workflow file, like the example above.
