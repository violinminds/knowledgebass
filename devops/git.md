# Git

## Resources

- Complete training: https://githubtraining.github.io/training-manual/
- Cheat sheet: https://training.github.com/downloads/github-git-cheat-sheet/

## Commands

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
