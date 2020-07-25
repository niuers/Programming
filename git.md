### Table of Contents
**[Git Overview](#git-overview)**<br>
**[Git Basics](#git-basics)**<br>
**[Git Usage Examples](#git-usage-examples)**<br>
**[Git Internals](#git-internals)**<br>


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

1. Tracked files: Files that were in the last snapshot.
1. Untracked files: Everything else. Any files in your working directory that were not in your last snapshot and are not in your staging area.

`git add` is a multipurpose command—you use it to begin tracking new files, to stage files, and
to do other things like marking merge-conflicted files as resolved. It may be helpful to think of it more as “add this
content to the next commit” rather than “add this file to the project”

A file can be in both staged and 'not staged but modified' status
It turns out that
Git stages a file exactly as it is when you run the git add command. If you commit now, the version of benchmarks.rb
as it was when you last ran the git add command is how it will go into the commit, not the version of the file as it
looks in your working directory when you run git commit. If you modify a file after you run git add, you have to
run git add again to stage the latest version of the file

Remember that the commit records the snapshot you set up in your staging area. Anything you didn’t stage is still
sitting there modified; you can do another commit to add it to your history. Every time you perform a commit, you’re
recording a snapshot of your project that you can revert to or compare to later.

1. `git log -p -2`
1. `git log --stat`
1. `git log --pretty=oneline --graph`
1. `git log --pretty=format: "%h - %an, %ar : %s" --graph`
1. `git log --since=2.weeks --committer= --grep= --all-match`
1. `git log --until=2.weeks`
1. `git log --before=2.weeks`
1. `git log -Sfunction_name`


### Undoing Things
1. `git commit --amend`

This command takes your staging area and uses it for the commit. If you’ve made no changes since your last
commit (for instance, you run this command immediately after your previous commit), then your snapshot will look
exactly the same, and all you’ll change is your commit message.
As an example, if you commit and then realize you forgot to stage the changes in a file you wanted to add to this
commit, you can do `git add`, then `git commit --amend`. 
You end up with a single commit—the second commit replaces the results of the first.

Remember, anything that is committed in Git can almost always be recovered. Even commits that were on
branches that were deleted or commits that were overwritten with an --amend commit can be recovered (see the
section on data recovery). However, anything you lose that was never committed is likely never to be seen again.

### Git Remotes
1. `git remote -v`:
1. Add remote repository
`git remote add pb https://github.com/paulboone/ticgit`
1. `git fetch [remote-name]`: 
The command goes out to that remote project and pulls down all the data from that remote project that you don’t
have yet. After you do this, you should have references to all the branches from that remote, which you can merge in
or inspect at any time.

It’s important to note that the git fetch command pulls the data to your local repository—it doesn’t automatically
merge it with any of your work or modify what you’re currently working on. You have to merge it manually into your
work when you’re ready.

1. If you have a branch set up to track a remote branch (see the next section and Chapter 3 for more information),
you can use the git pull command to automatically fetch and then merge a remote branch into your current branch.

Running git pull generally fetches data from the server you originally cloned from and
automatically tries to merge it into the code you’re currently working on.

#### Pushing to Your Remotes
1. `git push [remote-name] [branch-name]`

1. `git remote show origin`: inspect a remote

#### Tagging
1. Search for tags with a particular pattern
`git tag -l 'v1.8.5*'`

#### Create Tags
1. Annotated Tags
  * Annotated tags, however, are stored as full objects in the Git database. They’re checksummed; contain the tagger
name, e-mail, and date; have a tagging message; and can be signed and verified with GNU Privacy Guard (GPG).
It’s generally recommended that you create annotated tags so you can have all this information; but if you want a
temporary tag or for some reason don’t want to keep the other information, lightweight tags are available too.

`git tag -a v1.4 -m 'my version 1.4'`

Tag commit
`git tag -a v1.2 9fceb02`


  
1. Lightweight Tag:  lightweight tag is very much like a branch that doesn’t change—it’s just a pointer to a specific commit
`git tag v1.4-lw`

This is basically the commit checksum stored in a file—no other
information is kept.

Sharing Tags: By default, the git push command doesn’t transfer tags to remote servers.
1. `git push origin [tagname]`
1. `git push origin --tags`

#### Git Aliases
1. 
```
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status
$ git config --global alias.last 'log -1 HEAD'
$ git config --global alias.unstage 'reset HEAD --'
```

This makes the following two commands equivalent:
```
$ git unstage fileA
$ git reset HEAD fileA
```

However, maybe you want to run
an external command, rather than a Git subcommand. In that case, you start the command with a ! character. This is
useful if you write your own tools that work with a Git repository. We can demonstrate by aliasing `git visual` to run `gitk`:
```
$ git config --global alias.visual "!gitk"
```




## Git Branching
1. That’s basically what a branch in Git is: a simple pointer or reference to the head of a line of work.
1. The HEAD file is a symbolic reference to the branch you’re currently on. By symbolic reference, we mean that
unlike a normal reference, it doesn’t generally contain a SHA-1 value but rather a pointer to another reference.
  1. When you run git commit, it creates the commit object, specifying the parent of that commit object to be
whatever SHA-1 value the reference in HEAD points to.

When you make a commit, Git stores a commit object that contains a pointer to the snapshot of the content
you staged. This object also contains the author’s name and email, the message that you typed, and pointers to
the commit or commits that directly came before this commit (its parent or parents): zero parents for the initial
commit, one parent for a normal commit, and multiple parents for a commit that results from a merge of two or
more branches.

To visualize this, let’s assume that you have a directory containing three files, and you stage them all and commit.
Staging the files checksums each one (the SHA-1 hash we mentioned in Chapter 1), stores that version of the file in the
Git repository (Git refers to them as blobs), and adds that checksum to the staging area:
$ git add README test.rb LICENSE
$ git commit -m 'initial commit of my project'
When you create the commit by running git commit, Git checksums each subdirectory (in this case, just the
root project directory) and stores those tree objects in the Git repository. Git then creates a commit object that has the
metadata and a pointer to the root project tree so it can re-create that snapshot when needed.
Your Git repository now contains five objects: one blob for the contents of each of your three files, one tree that
lists the contents of the directory and specifies which file names are stored as which blobs, and one commit with the
pointer to that root tree and all the commit metadata.

If you make some changes and commit again, the next commit stores a pointer to the commit that came
immediately before it.

A branch in Git is simply a lightweight movable pointer to one of these commits.


#### HEAD
1. `git branch testing`: This creates a new pointer at the same commit you’re currently on.
How does Git know what branch you’re currently on? It keeps a special pointer called HEAD. In Git, this is a
pointer to the local branch you’re currently on. In this case, you’re still on master. The git branch command only
created a new branch—it didn’t switch to that branch.

1. `git log --oneline --decorate`

You can easily see this by running a simple git log command that shows you where the branch pointers are
pointing. This option is called `--decorate`.

1.  Switch Branches
`git checkout testing`: This moves HEAD to point to the testing branch.

1. Create a branch and switch to it: `git checkout -b testing`

##### Switching branches changes files in your working directory
It’s important to note that when you switch branches in Git, files in your working directory will change. If you
switch to an older branch, your working directory will be reverted to look like it did the last time you committed on
that branch. If Git cannot do it cleanly, it will not let you switch at all.

`git log --oneline --decorate --graph --all`

1. Because a branch in Git is in actuality a simple file that contains the 40 character SHA-1 checksum of the commit
it points to, branches are cheap to create and destroy. Creating a new branch is as quick and simple as writing 41 bytes to a file (40 characters and a newline).
`cat .git/refs/heads/testing`

#### Basic Branching and Merging

However, before you can switch branch, note that if your working directory or staging area has uncommitted changes that
conflict with the branch you’re checking out, Git won’t let you switch branches. It’s best to have a clean working state
when you switch branches. There are ways to get around this (namely, stashing and commit amending) that we’ll cover
later on.

This is an important point to remember: when you switch branches, Git resets
your working directory to look like it did the last time you committed on that branch. It adds, removes, and modifies
files automatically to make sure your working copy is what the branch looked like on your last commit to it.

1. Merge
Suppose you are on 'master' branch: `git merge hotfix`

point. Because the commit on the branch you’re on isn’t a direct ancestor of the branch
you’re merging in, Git has to do some work. In this case, Git does a simple three-way merge, using the two snapshots
pointed to by the branch tips and the common ancestor of the two. Instead of just moving the branch pointer forward, Git creates a new snapshot that results from this three-way
merge and automatically creates a new commit that points to it. This is referred to as a merge commit, and is special
in that it has more than one parent

1. Merge Conflicts:

`git mergetool`

##### Branch Management
1. `git branch -v`: see the last commit on each branch
1. `git branch --merged`: Filter the list to branches that you have or have not yet merged (--no-merged) into the branch you're currently on.

#### Branch Workflow
##### Long-Running Branches

1. The idea
is that your branches are at various levels of stability; when they reach a more stable level, they’re merged into the
branch above them. Again, having multiple long-running branches isn’t necessary, but it’s often helpful, especially
when you’re dealing with very large or complex projects.

##### Topic Branches
##### Remote Branches
Remote branches are references (pointers) to the state of branches in your remote repositories. They’re local branches
that you can’t move; they’re moved automatically for you whenever you do any network communication. Remote
branches act as bookmarks to remind you where the branches on your remote repositories were the last time you
connected to them.

#### "ORIGIN" is NOT special
Just like the branch name “master” does not have any special meaning in Git, neither does “origin”. While
“master” is the default name for a starting branch when you run git init which is the only reason it’s widely
used, “origin” is the default name for a remote when you run git clone. If you run git clone -o booyah
instead, then you will have booyah/master as your default remote branch.

To synchronize your work, you run a git fetch origin command. This command looks up which server “origin”
is (in this case, it’s git.ourcompany.com), fetches any data from it that you don’t yet have, and updates your local
database, moving your origin/master pointer to its new, more up-to-date position.

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

### Transfer Protocols
#### The Dumb Protocol
#### The Smart Protocol

## Git Usage Examples

  




