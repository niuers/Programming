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
1. `git log --abbrev-commit --pretty=oneline`


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

#### Pushing
1. `git push (remote) (branch)`:
`git push origin serverfix`: Git automatically expands the `serverfix` branch name out to `refs/heads/
serverfix:refs/heads/serverfix`, which means, “Take my `serverfix` local branch and push it to update the remote’s
`serverfix` branch.”
Or use `push origin serverfix:remote_branch_name` to do the same thing.

It’s important to note that when you do a fetch that brings down new remote branches, you don’t automatically
have local, editable copies of them. In other words, in this case, you don’t have a new serverfix branch—you only
have an origin/serverfix pointer that you can’t modify.
To merge this work into your current working branch, you can run `git merge origin/serverfix`. If you want
your own serverfix branch that you can work on, you can base it off your remote branch:
```
$ git checkout -b serverfix origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
Switched to a new branch 'serverfix'
```

#### Tracking Branches
1. Checking out a local branch from a remote branch automatically creates what is called a “tracking branch”
(or sometimes an “upstream branch”). Tracking branches are local branches that have a direct relationship to a
remote branch. If you’re on a tracking branch and type git push, Git automatically knows which server and branch
to push to. Also, running git pull while on one of these branches fetches all the remote references and then
automatically merges in the corresponding remote branch.

1. `git checkout --track origin/serverfix` this is the same as `git checkout -b [branch] [remotename]/[branch]`

If you already have a local branch and want to set it to a remote branch you just pulled down, or want to change
the upstream branch you’re tracking, you can use the -u or --set-upstream-to option to git branch to explicitly set
it at any time.

```
$ git branch -u origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
```

`git fetch --all; git branch -vv` list tracking branches

##### Upstream shorthand
When you have a tracking branch set up, you can reference it with the @{upstream} or @{u} shorthand. So if
you’re on the master branch and it’s tracking origin/master, you can say something like git merge @{u} instead
of git merge origin/master if you want.

#### Pulling

git pull which is essentially a git fetch immediately followed by a git merge in most cases.

Generally it’s better to simply use the fetch and merge commands explicitly as the magic of git pull can often
be confusing.

#### Deleting Remote Branches
1. `git push origin --delete serverfix`

Basically all this does is remove the pointer from the server. The Git server will generally keep the data there for a
while until a garbage collection runs, so if it was accidentally deleted, it’s often easy to recover.

#### Rebasing
Two main ways to integrate changes from one branch into another: the merge and the rebase.

The easiest way to integrate the branches, as we’ve already covered, is the merge command. It performs a
three-way merge between the two latest branch snapshots (C3 and C4) and the most recent common ancestor of the
two (C2), creating a new snapshot (and commit).

However, there is another way: you can take the patch of the change that was introduced in C4 and reapply it on
top of C3. In Git, this is called rebasing. With the rebase command, you can take all the changes that were committed
on one branch and replay them on another one.

It works by going to the common ancestor of the two branches (the one you’re on and the one you’re rebasing
onto), getting the diff introduced by each commit of the branch you’re on, saving those diffs to temporary files,
resetting the current branch to the same commit as the branch you are rebasing onto, and finally applying each
change in turn.
```
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```
At this point, you can go back to the master branch and do a fast-forward merge.

here is no difference in the end product of the integration, but rebasing makes for a cleaner history.
If you examine the log of a rebased branch, it looks like a linear history: it appears that all the work happened in series,
even when it originally happened in parallel.
Often, you’ll do this to make sure your commits apply cleanly on a remote branch – perhaps in a project to
which you’re trying to contribute but that you don’t maintain. In this case, you’d do your work in a branch and then
rebase your work onto origin/master when you were ready to submit your patches to the main project. That way, the
maintainer doesn’t have to do any integration work—just a fast-forward or a clean apply.
Note that the snapshot pointed to by the final commit you end up with, whether it’s the last of the rebased
commits for a rebase or the final merge commit after a merge, is the same snapshot—it’s only the history that is
different. Rebasing replays changes from one line of work onto another in the order they were introduced, whereas
merging takes the endpoints and merges them.

You can also have your rebase replay on something other than the rebase target branch.
Suppose you are on 'client' branch, you have 'master', 'server', 'client' branches, 
Suppose you decide that you want to merge your client-side changes into your mainline for a release, but you
want to hold off on the server-side changes until it’s tested further. You can take the changes on client that aren’t on
server (C8 and C9) and replay them on your master branch by using the --onto option of git rebase:

$ git rebase --onto master server client
This basically says, “Check out the client branch, figure out the patches from the common ancestor of the client
and server branches, and then replay them onto master.”

Now you can fast-forward your master branch:
$ git checkout master
$ git merge client

checkout topicbranch and replays it onto the base branch. 
`git rebase [basebranch] [topicbranch]`

Now fast-forward the base branch
`git checkout master`
`git merge server`

#### The Perils of Rebasing

Do not rebase commits that exist outside your repository.

When you rebase stuff, you’re abandoning existing commits and creating new ones that are similar but different.
If you push commits somewhere and others pull them down and base work on them, and then you rewrite those
commits with git rebase and push them up again, your collaborators will have to re-merge their work and things will
get messy when you try to pull their work back into yours.

It turns out that in addition to the commit SHA checksum, Git also calculate a checksum that is based just on the
patch introduced with the commit. This is called a “patch-id.”
If you pull down work that was rewritten and rebase it on top of the new commits from your partner, Git can often
successfully figure out what is uniquely yours and apply them back on top of the new branch.

If you treat rebasing as a way to clean up and work with commits before you push them, and if you only rebase
commits that have never been available publicly, then you’ll be fine. If you rebase commits that have already been
pushed publicly, and people may have based work on those commits, then you may be in for some frustrating trouble,
and the scorn of your teammates.

In general the way to get the best of both worlds is to rebase local changes you’ve made but haven’t shared yet
before you push them in order to clean up your story, but never rebase anything you’ve pushed somewhere.



A remote repository is generally a bare repository—a Git repository that has no working directory. Because the
repository is only used as a collaboration point, there is no reason to have a snapshot checked out on disk; it’s just the
Git data. In the simplest terms, a bare repository is the contents of your project’s .git directory and nothing else.


## Distributed Git
In Git, however, every developer is potentially both a node and a hub—that is, every developer can both
contribute code to other repositories and maintain a public repository on which others can base their work and which
they can contribute to


### Centralized Workflow
1. One central hub,
or repository, can accept code, and everyone synchronizes their work to it. A number of developers are nodes—
consumers of that hub—and synchronize to that one place.

### Integration-Manager Workflow

Because Git allows you to have multiple remote repositories, it’s possible to have a workflow where each developer
has write access to their own public repository and read access to everyone else’s. This scenario often includes a
canonical repository that represents the “official” project. To contribute to that project, you create your own public
clone of the project and push your changes to it. Then, you can send a request to the maintainer of the main project to
pull in your changes. The maintainer can then add your repository as a remote, test your changes locally, merge them
into their branch, and push back to their repository.

This is a very common workflow with hub-based tools such as GitHub or GitLab, where it’s easy to fork a project
and push your changes into your fork for everyone to see. One of the main advantages of this approach is that you can
continue to work, and the maintainer of the main repository can pull in your changes at any time. Contributors don’t
have to wait for the project to incorporate their changes—each party can work at their own pace.

### Dictator and Lieutenants Workflow
This is a variant of a multiple-repository workflow. It’s generally used by huge projects with hundreds of collaborators;
one famous example is the Linux kernel. Various integration managers are in charge of certain parts of the repository;
they’re called lieutenants. All the lieutenants have one integration manager known as the benevolent dictator.
The benevolent dictator’s repository serves as the reference repository from which all the collaborators need to pull.

## Contributing to a Project
Some of the variables involved are active contributor
count, chosen workflow, your commit access, and possibly the external contribution method.

### Commit Guidelines

1. First, you don’t want to submit any whitespace errors. Git provides an easy way to check for this—before you
commit, run git diff --check, which identifies possible whitespace errors and lists them for you.

1. Next, try to make each commit a logically separate changeset.

If some of the changes modify the same file, try to use git
add --patch to partially stage files.

1. The last thing to keep in mind is the commit message. Getting in the habit of creating quality commit messages
makes using and collaborating with Git a lot easier. As a general rule, your messages should start with a single line
that’s no more than about 50 characters and that describes the changeset concisely, followed by a blank line, followed
by a more detailed explanation.

The Git project requires that the more detailed explanation include your motivation
for the change and contrast its implementation with previous behavior—this is a good guideline to follow. It’s also a
good idea to use the imperative present tense in these messages. In other words, use commands. Instead of “I added
tests for” or “Adding tests for,” use “Add tests for.”

```
Short (50 chars or less) summary of changes
More detailed explanatory text, if necessary. Wrap it to
about 72 characters or so. In some contexts, the first
line is treated as the subject of an email and the rest of
the text as the body. The blank line separating the
summary from the body is critical (unless you omit the body
entirely); tools like rebase can get confused if you run
the two together.
Further paragraphs come after blank lines.
- Bullet points are okay, too
- Typically a hyphen or asterisk is used for the bullet,
preceded by a single space, with blank lines in
between, but conventions vary here
```

The Git project has well-formatted commit messages—try running git log --no-merges there to see what a nicely
formatted project-commit history looks like.




## Tricks
### Branch References
The most straightforward way to specify a commit requires that it have a branch reference pointed at it.
If you want to see which specific SHA a branch points to, or if you want to see what any of these examples
boils down to in terms of SHAs, you can use a Git plumbing tool called rev-parse
`git rev-parse topic1`


### RefLog Shortnames
1. One of the things Git does in the background while you’re working away is keep a “reflog”—a log of where your HEAD
and branch references have been for the last few months.

`git reflog`

Every time your branch tip is updated for any reason, Git stores that information for you in this temporary
history. And you can specify older commits with this data, as well. If you want to see the fifth prior value of the HEAD
of your repository, you can use the @{n} reference that you see in the reflog output:
$ git show HEAD@{5}

For instance, to see
where your master branch was yesterday, you can type
$ git show master@{yesterday}

This technique only works for data that’s still in your reflog,
so you can’t use it to look for commits older than a few months

To see reflog information formatted like the git log output, you can run git log -g:
`got log -g master`

It’s important to note that the ref log information is strictly local—it’s a log of what you’ve done in your repository.
The references won’t be the same on someone else’s copy of the repository; and right after you initially clone a
repository, you’ll have an empty reflog, as no activity has occurred yet in your repository.

### Ancestry References
The other main way to specify a commit is via its ancestry. If you place a ^ at the end of a reference, Git resolves it to
mean the parent of that commit.

you can see the previous commit by specifying HEAD^, which means “the parent of HEAD.”
`git show HEAD^`

You can also specify a number after the ^—for example, d921970^2 means “the second parent of d921970.” This
syntax is only useful for merge commits, which have more than one parent. The first parent is the branch you were on
when you merged, and the second is the commit on the branch that you merged in

The other main ancestry specification is the ~. This also refers to the first parent, so HEAD~ and HEAD^ are
equivalent. The difference becomes apparent when you specify a number. HEAD~2 means “the first parent of the first
parent,” or “the grandparent”—it traverses the first parents the number of times you specify.

### Commit Ranges
#### Double Dot
This basically asks Git to resolve a range of commits
that are reachable from one commit but aren’t reachable from another.

You want to see what is in your experiment branch that hasn’t yet been merged into your master branch. You
can ask Git to show you a log of just those commits with master..experiment—that means “all commits reachable by
experiment that aren’t reachable by master.”

`git log master..experiment`

If, on the other hand, you want to see the opposite—all commits in master that aren’t in experiment—you can
reverse the branch names. experiment..master shows you everything in master not reachable from experiment

Another very frequent use of this syntax is to see what you’re about to push to a remote
`git log origin/master..HEAD`

This command shows you any commits in your current branch that aren’t in the master branch on your origin
remote.

You can also leave off one side of the
syntax to have Git assume HEAD. For example, you can get the same results as in the previous example by typing
git log origin/master.. – Git substitutes HEAD if one side is missing.


### Multiple Points

perhaps you want to specify more than two branches to indicate
your revision, such as seeing what commits are in any of several branches that aren’t in the branch you’re currently
on. Git allows you to do this by using either the ^ character or --not before any reference from which you don’t want
to see reachable commits. Thus these three commands are equivalent

```
$ git log refA..refB
$ git log ^refA refB
$ git log refB --not refA
```

For instance, if you want to see all commits that are reachable from refA or refB but
not from refC, you can type one of these:
```
$ git log refA refB ^refC
$ git log refA refB --not refC
```

### Triple Dots
specifies all the commits that are reachable by either of two references but not by both of them.

If you want to see what is in master or experiment but not any common references, you can run
$ git log master...experiment

#### Interactive Staging
1. a few interactive
commands that can help you easily craft your commits to include only certain combinations and parts of files.

These tools are very helpful if you modify a bunch of files and then decide that you want those changes to be in
several focused commits rather than one big messy commit. This way, you can make sure your commits are logically
separate changesets and can be easily reviewed by the developers working with you. If you run git add with the -i or
--interactive option, Git goes into an interactive shell mode, displaying something like this:

`git add -i`

1. Staging and Unstaging Files
Select '2' or 'u' (update): 

1. Staging Patches

It’s also possible for Git to stage certain parts of files and not the rest. For example, if you make two changes to your
simplegit.rb file and want to stage one of them and not the other, doing so is very easy in Git. From the interactive
prompt, type 5 or p (for patch). Git will ask you which files you would like to partially stage; then, for each section of
the selected files, it will display hunks of the file diff and ask if you would like to stage them, one by one

You also don’t need to be in interactive add mode to do the partial-file staging—you can start the same script by
using git add -p or git add --patch on the command line.

Furthermore, you can use patch mode for partially resetting files with the reset --patch command, for checking
out parts of files with the checkout --patch command and for stashing parts of files with the stash save --patch
command. 

#### Stashing and Cleaning
1. Often, when you’ve been working on part of your project, things are in a messy state and you want to switch branches
for a bit to work on something else. The problem is, you don’t want to do a commit of half-done work just so you can
get back to this point later. The answer to this issue is the git stash command.

`git stash apply stash@{1}`
You can see that Git remodifies the files you reverted when you saved the stash.

The changes to your files were reapplied, but the file you staged before wasn’t restaged. To do that, you must run
the git stash apply command with a --index option to tell the command to try to reapply the staged changes. If you
had run that instead, you’d have gotten back to your original position

#### Creative Stashing
1. 
The first option that is quite popular is the --keep-index
option to the stash save command. This tells Git to not stash anything that you’ve already staged with the git add
command.
This can be really helpful if you’ve made a number of changes but want to only commit some of them and then
come back to the rest of the changes at a later time.

1. 
Another common thing you may want to do with stash is to stash the untracked files as well as the tracked ones.
By default, git stash will only store files that are already in the index. If you specify --include-untracked or -u, Git
will also stash any untracked files you have created.

1. Finally, if you specify the --patch flag, Git will not stash everything that is modified but will instead prompt you
interactively which of the changes you would like to stash and which you would like to keep in your working directory.
$ git stash --patch

#### Unapplying a Stash

In some use case scenarios you might want to apply stashed changes, do some work, but then unapply those changes
that originally came from the stash. Git does not provide such a stash unapply command, but it is possible to achieve
the effect by simply retrieving the patch associated with a stash and applying it in reverse:
$ git stash show -p stash@{0} | git apply -R
Again, if you don’t specify a stash, Git assumes the most recent stash:
$ git stash show -p | git apply -R
You may want to create an alias and effectively add a stash-unapply command to your git. For example:
$ git config --global alias.stash-unapply '!git stash show -p | git apply -R'
$ git stash
$ #... work work work
$ git stash-unapply


#### Creating a Branch from a Stash

`git stash branch testchanges`

which creates a new branch for you, checks out the commit you were on when you stashed
your work, reapplies your work there, and then drops the stash if it applies successfully


#### Cleaning Your Working Directory
Finally, you may not want to stash some work or files in your working directory, but simply get rid of them.
The git clean command will do this for you.
Some common reasons for this might be to remove cruft that has been generated by merges or external tools or
to remove build artifacts in order to run a clean buil

You’ll want to be pretty careful with this command, because it’s designed to remove files from your working
directory that are not tracked. If you change your mind, there is often no retrieving the content of those files. A safer
option is to run git stash --all to remove everything but save it in a stash.

To
remove all the untracked files in your working directory, you can run git clean -f -d, which removes any files and
also any subdirectories that become empty as a result. The -f means force or “really do this.”
If you ever want to see what it would do, you can run the command with the -n option, which means “do a dry
run and tell me what you would have removed.”

By default, the git clean command will only remove untracked files that are not ignored. Any file that matches a
pattern in your .gitignore or other ignore files will not be removed. If you want to remove those files too, such as to
remove all .o files generated from a build so you can do a fully clean build, you can add a -x to the clean command.

If you don’t know what the git clean command is going to do, always run it with a -n first to double check before
changing the -n to a -f and doing it for real. The other way you can be careful about the process is to run it with the -i
or “interactive” flag.

#### Searching
##### Git Grep
1. `git grep -n gmtime_r`
By default, grep looks through the files in your working directory. You can pass -n to print out the line numbers
where Git has found matches.

1. you can have Git summarize the output by just showing you which files
matched and how many matches there were in each file with the --count option

1. If you want to see what method or function it thinks it has found a match in, you can pass -p:
`git grep -p gmtime_r *.c`

1. You can also look for complex combinations of strings with the --and flag, which makes sure that multiple
matches are in the same line.

```
$ git grep --break --heading \
-n -e '#define' --and \( -e LINK -e BUF_MAX \) v1.8.0
```

The git grep command has a few advantages over normal searching commands such as grep and ack. The first
is that it’s really fast, and the second is that you can search through any tree in Git, not just the working directory. As
we saw in the previous example, we looked for terms in an older version of the Git source code, not the version that
was currently checked out.

#### Git Log Search
1. we can tell Git to
only show us the commits that either added or removed that string with the -S option.
`git log -Sstring_pattern --oneline`
If you need to be more specific, you can provide a regular expression to search for with the -G option.

#### Line Log Search

It is called with the -L option to git log and shows you the history of
a function or line of code in your codebase.
For example, if we wanted to see every change made to the function git_deflate_bound in the zlib.c file, we
could run git log -L :git_deflate_bound:zlib.c. This tries to figure out what the bounds of that function are and
then looks through the history and shows every change that was made to the function as a series of patches back to
when the function was first created.

If Git can’t figure out how to match a function or method in your programming language, you can also provide
it a regex. For example, this would have done the same thing: git log -L '/unsigned long git_deflate_
bound/',/^}/:zlib.c. You could also give it a range of lines or a single line number and you’ll get the same sort of output.

#### Rewriting History








## Git GUI
### gitk
A powerful GUI shell over `git log` and `git grep`. This is the tool
to use when you’re trying to find something that happened in the past, or visualize your project’s history.

Probably one of the most useful is the --all flag, which tells gitk to show commits reachable from any ref, not just
HEAD.

### git-gui
`git gui`: is primarily a tool for crafting commits


## Git in Bash
If you’re a Bash user, you can tap into some of your shell’s features to make your experience with Git a lot friendlier.
Git actually ships with plugins for several shells, but it’s not turned on by default.
First, you need to get a copy of the contrib/completion/git-completion.bash file out of the Git source code.
Copy it somewhere handy, like your home directory, and add this to your .bashrc:
. ~/git-completion.bash
Once that’s done, change your directory to a git repository, and type:
$ git chec<tab>
…and Bash will auto-complete to git checkout. This works with all of Git’s subcommands, command-line
parameters, and remotes and ref names where appropriate.
  
It’s also useful to customize your prompt to show information about the current directory’s Git repository.
This can be as simple or complex as you want, but there are generally a few key pieces of information that most
people want, like the current branch and the status of the working directory. To add these to your prompt, just copy
the contrib/completion/git-prompt.sh file from Git’s source repository to your home directory, add something like
this to your .bashrc:
```
. ~/git-prompt.sh
export GIT_PS1_SHOWDIRTYSTATE=1
export PS1='\w$(__git_ps1 " (%s)")\$ '
```

The `\w` means print the current working directory, the `\$` prints the `$` part of the prompt, and `__git_ps1 " (%s)"`
calls the function provided by `git-prompt.sh` with a formatting argument. Now your bash prompt will look like this
when you’re anywhere inside a Git-controlled project

  
  






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

  




