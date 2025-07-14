### What is Git?

is distribute file system database
Git basically takes a picture of what all your files look like at that moment and stores a
reference to that snapshot. To be efficient, if files have not changed, Git doesn’t store the file again,
just a link to the previous identical file it has already stored. Git thinks about its data more like a
stream of snapshots.

CVS, Subversion, Perforce, and so on is delta-based version control

Git perks

- Snapshots, Not Differences. Git thinks about its data more like a stream of snapshots
-

### Git Has Integrity

- Everything in Git is checksummed (SHA-1 hash) before it is stored and is then referred to by that checksum

### Git Generally Only Adds Data

### The Three States

- Modified means that you have changed the file but have not committed it to your database yet.
- Staged means that you have marked a modified file in its current version to go into your next
  commit snapshot.
- Committed means that the data is safely stored in your local database.

The basic Git workflow goes something like this:
1. You modify files in your working tree.
2. You selectively stage just those changes you want to be part of your next commit, which adds
   only those changes to the staging area.
3. You do a commit, which takes the files as they are in the staging area and stores that snapshot
   permanently to your Git directory.

### Checking Your Settings
```bash 
git config --list
```

## Git Basics
Recording Changes to the Repository

Tracked files are files that Git knows about
To stage it, you run the git add command (“add precisely this content to the next commit”). git add is a multipurpose command — you use it to begin
tracking new files, to stage files, and to do other things like marking merge-conflicted files as
resolved.
If you modify a file after you run git add, you have to run git add again to stage the
latest version of the file.

GitHub maintains a fairly comprehensive list of good .gitignore file examples for
dozens of projects and languages at https://github.com/github/gitignore if you want
a starting point for your project.

