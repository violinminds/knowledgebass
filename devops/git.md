# Git

## Resources

- Complete training: https://githubtraining.github.io/training-manual/
- Cheat sheet: https://training.github.com/downloads/github-git-cheat-sheet/

## Commands

### Compact `.git` folder

Reduces the size of the `.git` folder by cleaning up cached unuseful thingies.

```shell
git maintenance run --auto
git gc --aggressive --prune=now
git reflog expire --expire=now --all
```

### `git log` with the cutest formatting

```powershell
git log --pretty=fuller --show-signature
```

### Push and track a new local branch to a remote repository

```powershell
# single branch
# git push -u/--set-upstream <remote> <branch>
git push -u origin my-new-branch

# all branches
git push -u --all
```

### Move existing, uncommitted work to a new branch in Git

```powershell
# git switch -c <new-branch>
git switch -c my-new-branch
```

### List all NON-pushed commits

```powershell
# simple list
git log --branches --not --remotes --oneline

# detailed list
git log --branches --not --remotes --show-signature
```

### Fetch deletion of branches

Deletes the local branches that have been remotely deleted.

```shell
git fetch --prune
```

### Delete all tags except one

Example: delete all tags except "2.2.1":

```
git push origin -d $(git tag -l | grep -v '2.2.1') # delete remotely
git tag -d $(git tag | grep -v '2.2.1') # delete locally
```

### Delete all tags with given prefix

> https://gist.github.com/shsteimer/7257245?permalink_comment_id=2623569#gistcomment-2623569

Example: delete all tags with prefix "1.0":

```
git push origin -d $(git tag -l "v1.0*") # delete remotely
git tag -d $(git tag -l "v1.0*") # delete locally
```

### Change upstream branch

> https://stackoverflow.com/a/18816842/9156059

```
git branch --set-upstream-to <remote-name>
```

This can be useful if you `clone`d a repository, then decided to fork it to create a PR.

For example, you `clone`d a Github repository "user/some-repo" locally, then decided to edit something to create a PR.
First, create a fork in Github (if you have the [Github command line tool](https://github.com/cli/cli), you can use `gh repo fork --clone --remote --remote-name fork`). This will result in a new repo "yourusername/some-repo".
Then run the following inside your working copy:

```powershell
# add new remote (the fork)
# this will result in 2 remotes, the cloned one and the one added manually
git remote add fork git@github.com:yourusername/some-repo.git
git fetch --all --verbose
git remote -vv

# set upstream branch to the fork
git branch --set-upstream-to fork/master
git branch -vv

# (optional) remove previous remote: git remote remove <remotename>
git remote remove origin # or "upstream" or whatever the name is
```
