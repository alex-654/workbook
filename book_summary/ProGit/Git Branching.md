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
“tracking branch” (and the branch it tracks is called an “upstream branch”). Tracking branches are
local branches that have a direct relationship to a remote branch. If you’re on a tracking branch
and type git pull, Git automatically knows which server to fetch from and which branch to merge
in.

### Rebasing

A cherry-pick in Git is like a rebase for a single commit. It takes the patch that was introduced in a commit
and tries to reapply it on the branch you’re currently on.
It's create a new commit hash with new date.
Git cherry-pick usually brings a commit from somewhere else and applies it on top of your current branch, recording a
new commit, while git rebase takes your current branch and rewrites a series of its own tip commits in one way or
another.

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

- option drop - drop commit via --interactive option

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

#### Undo rebase/merge

```git reset --hard ORIG_HEAD``` or ```git reset --hard HEAD~```

Git keep in ORIG_HEAD variable last commit hash before dangerous operation like merge/rebase/pull

```
git log -1 ORIG_HEAD
```

Or find the last commit hash in ```git reflog``` and reset to it via commit hash HEAD@{1}.

Be aware

- if branch already pushed to remote that this command rewrite history.

Other way with history untouched - Reverse the commit

```
git revert -m 1 HEAD
[master b1d8379] Revert "Merge branch 'topic'"
```

The -m 1 flag indicates which parent is the “mainline” and should be kept.

Git will get confused if you try to merge topic into master again

```
git merge topic
Already up-to-date.
```

The best way around this is to un-revert the original merge, since now you want to bring in the
changes that were reverted out, then create a new merge commit:

```
$ git revert ^M
[master 09f0126] Revert "Revert "Merge branch 'topic'""
$ git merge topic
```

### RefLog Shortnames

“reflog” — a log of where your HEAD and branch references have been for the last few months.  
Reflog information is strictly local — it’s a log only of what you’ve done in your repository.

```git show HEAD@{5}```

Then, you can see the previous commit by specifying HEAD^, which means “the parent of HEAD”:
```git show HEAD^```

### Commit Ranges

#### Double Dot

This basically asks Git to resolve a range of commits that are reachable from one commit but aren’t reachable from
another

```
git log main..DG-379
```

#### Triple Dot

all the commits that are reachable by either of two references but not by both of them

```
git log main...DG-379
```

To compare any two branches (filename):

```
git diff --name-status firstbranch..yourBranchName
```

### rerere

So, if you do a lot of re-merges, or want to keep a topic branch up to date with your master branch
without a ton of merges, or you rebase often, you can turn on rerere to help your life out a bit.
