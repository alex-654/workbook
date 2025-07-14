
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
