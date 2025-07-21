## Git Branching

```
git branch testing
```

This creates a new pointer to the same commit you’re currently on.

How does Git know what branch you’re currently on? It keeps a special pointer called HEAD.  
In Git, this is a pointer to the local branch you’re currently on.

```
git checkout testing
```

This moves HEAD to point to the testing branch.

```
git commit -a -m 'Make a change'
```

```
git checkout master
```

That command did two things. It moved the HEAD pointer back to point to the master branch, and it
reverted the files in your working directory back to the snapshot that master points to.

Because a branch in Git is actually a simple file that contains the 40 character SHA-1 checksum of
the commit it points to, branches are cheap to create and destroy.

### Basic Branching and Merging

```
git branch --merged
```

Branches on this list without the \* in front of them are generally fine to delete with
```git branch -d```; you’ve already incorporated their work into another branch, so you’re not going to lose anything.

### Tracking Branches

Checking out a local branch from a remote-tracking branch automatically creates what is called a
92“tracking branch” (and the branch it tracks is called an “upstream branch”). Tracking branches are
local branches that have a direct relationship to a remote branch. If you’re on a tracking branch
and type git pull, Git automatically knows which server to fetch from and which branch to merge
in.

### Rebasing

A cherry-pick in Git is like a rebase for a single commit. It takes the patch that was introduced in a commit
and tries to reapply it on the branch you’re currently on.
It's create a new commit hash with new date. And is's difference from git rebase

```
git cherry-pick a2957a00777ffac7e0d5cb16edcf9b7606f89f8f
```

In phpStorm::

- option **undo** is delete the commit and return changes to working tree.

```
git reset --soft 42e734809195cdd0698c570e0e451f966c357afd
```

- option revert - revert commit

```
git revert 1a5d1b806d952baa4d50be4b8282eac9f0ab77a7
```

- option drop - drop commit by rebasing onto previous commit
  And phoStorm do same interactive work in background.

```
git rebase --interactive --no-autosquash 1a5d1b806d952baa4d50be4b8282eac9f0ab77a7
```

Before dangerous operation like merge or rebase make a backup

```
git tag BACKUP
```

You can return to it if something goes wrong:

```
git reset --hard BACKUP
```

### Rebase example

rebase a current branch onto other branch

```
git rebase refs/heads/DG-386
```

Undo rebase/merge

```
git reset --hard ORIG_HEAD
```

Git keep in ORIG_HEAD variable last commit hash before dangerous operation like merge/rebase/pull

```
git log -1 ORIG_HEAD
```

Or find the last commit hash in ```git reflog``` and reset to it via commit hash HEAD@{1}.
