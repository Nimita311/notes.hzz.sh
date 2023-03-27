# Git By Scenarios

## Develop on a feature branch
Setup: public base branch `develop`, feature branch `feature`, and remote `origin`.

### Create a branch
``` bash
git branch feature develop
git checkout feature
# or
git switch -c feature develop
# or
git checkout -b feature develop
```
If `HEAD` is on `develop`, the base branch argument (`develop`) can be omitted.

### Make a commit
``` bash
# after making some code changes
git checkout feature
git add -A # stage all changes including new files
git commit -m "Write a commit message"
```

### Push a local branch for the first time
Option `-u` (or `--set-upstream`) should be used to set up the remote. Configure `push.autoSetupRemote` for it to happen automatically.
``` bash
git push -u origin feature
```
If checked out to `feature`, `feature` can be replaced by `HEAD` (or `@`) to save typing as a real feature branch may have a very long name.

The upstream can be unset.
``` bash
git branch --unset-upstream feature
```

You can check upstream of a branch as follows.
``` bash
git branch -vv
```

### Update the base
``` bash
git pull origin develop
git rebase feature develop
```
Conflicts may appear. Try to rebase frequently and keep the change in `feature` small.

### Change the base
We would like to change the base of `feature` to `another-develop`. This case also applies when `develop` is rolled back with its history altered.
``` bash
git checkout feature
git rebase --onto another-develop develop
```

Cherry-pick can also work if there are few commits, for example only one.
``` bash
git checkout feature
git reset --hard another-develop
git cherry-pick @{1}
```

### Force push a branch
When `feature` has been altered locally (e.g. with rebase or amend), force push to overwrite remote.
``` bash
git push --force-with-lease origin feature
```
Option `--force-with-lease` should always be used instead of `--force` to prevent overwriting unexpected changes on remote. Though `feature` is not meant to be pushed by more than one developer.

### Integrate a branch
Most likely in a team, a feature branch needs to be merged with a Pull Request or by an automated system after review and approval.

In a smaller project with fewer restrictions, merge for an explicit "merge commit" on `develop`.
``` bash
git merge develop feature
```

Rebase for a clean and linear history on `develop`.
``` bash
git rebase feature develop
git merge develop feature # fast forward
```

### Delete a branch
Delete a local branch. Use option `-D` (equivalent to adding `--force`) to remove a unmerged branch.
``` bash
git branch -d feature
```

Delete a remote branch.
``` bash
git push -d origin feature
# or
git push origin :feature
```

## Use a remote feature branch
Setup: another feature branch `another-feature` actively worked on by a colleague and pushed to remote `origin`.

### Fetch a remote branch
``` bash
git fetch origin another-feature
# or simply fetch all refs from origin
git fetch
# or from all remotes
git fetch --all
```

### Track a remote branch with a local branch
``` bash
git switch another-feature
# or
git checkout --track origin/another-feature
# or
git checkout -b another-feature origin/another-feature
```

### Update a force pushed branch
If our colleague force pushed, we want to reset the local branch to match the remote.
``` bash
git branch -f another-feature origin/another-feature # when not checked out
# or
git checkout another-branch
git reset --hard @{u} # be careful, risk of losing work
```
In this case, `@{push}` and `origin/another-branch` would also work as the target for reset.

### Use commits from another branch
We may need to include a hot fix or borrow some changes from a colleague. For example, if we want to apply the last three commits in `another-feature` to `feature`.

``` bash
git checkout feature
git cherry-pick another-feature~3..another-feature
# or
git checkout -b temp another-feature
git rebase --onto feature @~3
git merge feature temp # fast forward
```
There might be implications when merging `feature` with commits from `another-feature` if the latter has already been merged.

Directly rebasing `feature` on `another-feature` is unlikely to work. If `feature` branches off `develop` later than `another-feature`, rebasing will include unwanted commits between two branching points.

## Fix and rename

### Rename a branch

## Housekeeping

```
git clean -fdx
```

```
git rev-parse --symbolic-full-name
```
