### Table of Contents
**[Git Overview](#git-overview)**<br>
**[Git Basics](#git-basics)**<br>
**[Git Usage Examples](#git-usage-examples)**<br>

## Git Overview

### Git is a Distributed Version Control System (DVCS)
1. Each client not only checks out the latest snapshot of the files, but a full mirror of the repository.
1. Every checkout is a full backup of all the data.


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
  
### Getting a Git Repository
1. 'git init'
1. 'git clone'

## Git Branching
1. That’s basically what a branch in Git is: a simple pointer or reference to the head of a line of work.
1. The HEAD file is a symbolic reference to the branch you’re currently on. By symbolic reference, we mean that
unlike a normal reference, it doesn’t generally contain a SHA-1 value but rather a pointer to another reference.
  1. When you run git commit, it creates the commit object, specifying the parent of that commit object to be
whatever SHA-1 value the reference in HEAD points to.

## Git Internals

### Git is a Content-Addressable Filesystem
1. Content-addressable filesystem: means that it's key-value data store. You can insert any kind of content into it, and it will give you back a key that you can use to retrieve the content again at any time. 

1. Git thinks about its data more like a **"stream of snapshots"**. Other VCS (Subversion etc.) think of the information as a set of files and the changes (deltas) made to each file over time.

1. All the content is stored as tree and blob objects, with trees corresponding to UNIX directory entries and blobs corresponding more
or less to inodes or file contents. A single tree object contains one or more tree entries, each of which contains
a SHA-1 pointer to a blob or subtree with its associated mode, type, and filename.

### Plumbing and Porcelain
### Git Objects
#### Blob Objects
1. `objects`: stores all the content for your database
1. `git hash-object`
1. `git cat-file`

#### Tree Objects
1. `git cat-file -p master^{tree}`

#### Commit Object

#### Tags
1. The tag object is very much like a
commit object – it contains a tagger, a date, a message, and a pointer. The main difference is that a tag object generally
points to a commit rather than a tree. It’s like a branch reference, but it never moves—it always points to the same
commit but gives it a friendlier name.
1. Also notice that it doesn’t need to point to a commit; you can tag any Git object.
##### Annotated 
##### Lightweight

#### Pack Files
1. The initial format in which Git saves objects on disk is called a “loose” object format.
However, occasionally Git packs up several of these objects into a single binary file called a “packfile” in order to save
space and be more efficient. Git does this if you have too many loose objects around, if you run the git gc command
manually, or if you push to a remote server. To see what happens, you can manually ask Git to pack up the objects by
calling the git gc command
1. The packfile is a single file containing the contents of
all the objects that were removed from your filesystem. The index is a file that contains offsets into that packfile
so you can quickly seek to a specific object.
1. What is also interesting is that
the second version of the file is the one that is stored intact, whereas the original version is stored as a delta—this is
because you’re most likely to need faster access to the most recent version of the file.

####
1. `refs`: stores pointers into commit objects in that data (branches)
1. `HEAD`: points to the branch you currently have checked out
1. `index`: where Git stores your staging area information

## Git Usage Examples

  




