### Table of Contents
**[Git Overview](#git-overview)**<br>
**[Git Basics](#git-basics)**<br>

## Git Overview

### Git is a Distributed Version Control System (DVCS)
1. Each client not only checks out the latest snapshot of the files, but a full mirror of the repository.
1. Every checkout is a full backup of all the data.

### Git is more like a mini filesystem
1. Git thinks about its data more like a **"stream of snapshots"**. Other VCS (Subversion etc.) think of the information as a set of files and the changes (deltas) made to each file over time.

### Nearly Every Operation in Git is Local

1. Git has Integrity: Everything in Git is check-summed before it is stored and is then referred to by that checksum. The mechanism that Git uses for this checksumming is called a SHA-1 hash. This is a 40-character string
composed of hexadecimal characters (0–9 and a–f) and calculated based on the contents of a file or directory
structure in Git. Git stores everything in its database not by filename but by the hash value of its contents.

1. Git Generally Only Adds Data: 

### The Three States
1. Committed: data is safely stored in your local database
1. Modified: You changed the file but have not committed it to your database yet.
1. Staged: You have marked a modified file in its current version to go into your next commit snapshot.
  
### The Three main sections of a Git project
1. The Git directory: The Git directory is where Git stores the metadata and object database for your project.
1. The working directory: The working directory is a single checkout of one version of the project. These files are pulled out of the
compressed database in the Git directory and placed on disk for you to use or modify.
1. The staging area: The staging area is a file, generally contained in your Git directory, that stores information about what will go into
your next commit. It’s sometimes referred to as the “index”, but it’s also common to refer to it as the staging area.

## Git Basics
### Git configs
1. System config: `/etc/gitconfig`
1. Global config: `~/.gitconfig`
1. Local config: `.git/config` under each repository
  
  
  




