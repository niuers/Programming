## Table of Contents
**[Git Overview](#git-overview)**<br>
**[Git Configs](#git-configs)**<br>
**[Git Process](#git-process)**<br>
**[Git Log](#git-log)**<br>
**[Git Remotes](#git-remotes)**<br>
**[Git Tag](#git-tag)**<br>
**[Git Branching](#git-branching)**<br>
**[Git Rebase](#git-rebase)**<br>
**[Distributed Git](#distributed-git)**<br>
**[Git Tricks](#git-tricks)**<br>
**[Interactive Staging](#interactive-staging)**<br>
**[Stashing and Cleaning](#stashing-and-cleaning)**<br>
**[Searching](#searching)**<br>
**[Rewriting History](#rewriting-history)**<br>
**[Reset Demystified](#reset-demystified)**<br>
**[Advanced Merging](#advanced-merging)**<br>
**[Debugging with Git](#debugging-with-git)**<br>
**[Git GUI](#git-gui)**<br>
**[Git Usage Examples](#git-usage-examples)**<br>
**[Git Internals](#git-internals)**<br>
**[References](#references)**<br>


# Git Overview

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

# Git Configs

There are three levels of git config: system, global, local, each of these "levels" overwrites the values in the previous level.

1. System config: `/etc/gitconfig`, applies to every user on the system and all their repositories
1. Global config: `~/.gitconfig`, applies to the user.
1. Local config: `.git/config` under each repository, per repository

### Excludes file globally
If you want to ignore certain files for all repositories that you work with, set them in the file: `~/lgitignore_global`: 

```
*~
.DS_Store
```

..and you run git config `--global core.excludesfile ~/.gitignore_global`, Git will never again bother you about those files.

### Git Aliases
1. Examples of Git Aliases 
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

1. However, maybe you want to run an external command, rather than a Git subcommand. In that case, you start the command with a ! character. This is useful if you write your own tools that work with a Git repository. We can demonstrate by aliasing `git visual` to run `gitk`:

```
$ git config --global alias.visual "!gitk"
```


### Formatting and Whitespace
It’s very easy for patches or other collaborated work to introduce subtle whitespace changes because editors silently introduce them, and if your files ever touch a Windows
system, their line endings might be replaced. Git has a few configuration options to help with these issues.

##### core.autocrlf
1. If you’re programming on Windows and working with people who are not (or vice versa), you’ll probably run into
line-ending issues at some point. 
  * This is because Windows uses both a carriage-return character and a linefeed character for newlines in its files, whereas Mac and Linux systems use only the linefeed character. This is a subtle but incredibly annoying fact of cross-platform work; many editors on Windows silently replace existing LF-style line endings with CRLF, or insert both line-ending characters when the user hits the enter key.
  * Git can handle this by auto-converting CRLF line endings into LF when you add a file to the index, and vice versa when it checks out code onto your filesystem. 
    * If you’re on a Windows machine, set `core.autocrlf` to true—this converts LF endings into CRLF when you check out code:
    `$ git config --global core.autocrlf true`
    * If you’re on a Linux or Mac system that uses LF line endings, then you don’t want Git to automatically convert them when you check out files; 
  * However, if a file with CRLF endings accidentally gets introduced, then you may want Git to fix it. You can tell Git to convert CRLF to LF on commit but not the other way around by setting `core.autocrlf` to input:
  `$ git config --global core.autocrlf input`

This setup should leave you with CRLF endings in Windows checkouts, but LF endings on Mac and Linux systems and in the repository.
  * If you’re a Windows programmer doing a Windows-only project, then you can turn off this functionality, recording the carriage returns in the repository by setting the config value to false:
  `$ git config --global core.autocrlf false`


##### core.whitespace
1. Git comes preset to detect and fix some whitespace issues. It can look for six primary whitespace issues—three are
enabled by default and can be turned off, and three are disabled by default but can be activated.
1. The ones that are turned on by default are 
  * blank-at-eol, which looks for spaces at the end of a line; 
  * blank-at-eof, which notices blank lines at the end of a file; 
  * space-before-tab, which looks for spaces before tabs at the beginning of a line.
1. The three that are disabled by default but can be turned on are 
  * indent-with-non-tab, which looks for lines that begin with spaces instead of tabs (and is controlled by the tabwidth option); 
  * tab-in-indent, which watches for tabs in the indentation portion of a line; 
  * cr-at-eol, which tells Git that carriage returns at the end of lines are OK.
  
1. You can tell Git which of these you want enabled by setting `core.whitespace` to the values you want on or off, separated by commas. You can disable settings by either leaving them out of the setting string or prepending a - in front of the value. For example, if you want all but `cr-at-eol` to be set, you can do this:
```
$ git config --global core.whitespace \
trailing-space,space-before-tab,indent-with-non-tab
```

1. Git will detect these issues when you run a `git diff` command and try to color them so you can possibly fix them
before you commit. 
1. It will also use these values to help you when you apply patches with `git apply`. When you’re
applying patches, you can ask Git to warn you if it’s applying patches with the specified whitespace issues:
```
$ git apply --whitespace=warn <patch>
```
Or you can have Git try to automatically fix the issue before applying the patch:
```
$ git apply --whitespace=fix <patch>
```
1. These options apply to the `git rebase` command as well. If you’ve committed whitespace issues but haven’t yet
pushed upstream, you can run `git rebase --whitespace=fix` to have Git automatically fix whitespace issues as it’s
rewriting the patches.
  
### Git Attributes
1. Some of these settings can also be specified for a path, so that Git applies those settings only for a subdirectory or
subset of files. These path-specific settings are called Git attributes and are set either in a `.gitattributes` file in
one of your directories (normally the root of your project) or in the `.git/info/attributes` file if you don’t want the
attributes file committed with your project.

1. Using attributes, you can do things like specify separate merge strategies for individual files or directories in your
project, tell Git how to diff non-text files, or have Git filter content before you check it into or out of Git.



# Git Process
### Initializing a Repository
1. 'git init'
1. 'git clone'

### Traced and Untracked Files
1. Tracked files: Files that were in the last snapshot.
1. Untracked files: Everything else. Any files in your working directory that were not in your last snapshot and are not in your staging area.

1. `git add` is a multipurpose command—you use it to begin tracking new files, to stage files, and
to do other things like marking merge-conflicted files as resolved. It may be helpful to think of it more as “add this
content to the next commit” rather than “add this file to the project”

1. A file can be in both staged and 'not staged but modified' status
It turns out that Git stages a file exactly as it is when you run the `git add` command, not the version of the file as it
looks in your working directory when you run `git commit`. If you modify a file after you run `git add`, you have to run `git add` again to stage the latest version of the file
Remember that the commit records the snapshot you set up in your staging area. Anything you didn’t stage is still sitting there modified; you can do another commit to add it to your history. Every time you perform a commit, you’re recording a snapshot of your project that you can revert to or compare to later.

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


# Git Log

1. `git log -p -2`
1. `git log --stat`
1. `git log --pretty=oneline --graph`
1. `git log --pretty=format: "%h - %an, %ar : %s" --graph`
1. `git log --since=2.weeks --committer= --grep= --all-match`
1. `git log --until=2.weeks`
1. `git log --before=2.weeks`
1. `git log -Sfunction_name`
1. `git log --abbrev-commit --pretty=oneline`
1. `git log --oneline --decorate`:  shows where the branch pointers are pointing.
1. `git log --oneline --decorate --graph --all`: 


# Git Remotes

1. `git remote -v`: list remote repositories
1. Add a remote repository 
`git remote add pb https://github.com/paulboone/ticgit`

1. `git fetch [remote-name]`: 
  * The command goes out to that remote project and pulls down all the data from that remote project that you don’t have yet. After you do this, you should have references to all the branches from that remote, which you can merge in or inspect at any time.
  * It’s important to note that the `git fetch` command pulls the data to your local repository—it doesn’t automatically merge it with any of your work or modify what you’re currently working on. You have to merge it manually into your work when you’re ready.

1. If you have a branch set up to track a remote branch , you can use the `git pull` command to automatically fetch and then merge a remote branch into your current branch.
Running `git pull` generally fetches data from the server you originally cloned from and automatically tries to merge it into the code you’re currently working on.

### Pushing to Your Remotes
1. `git push [remote-name] [branch-name]`
1. `git remote show origin`: inspect a remote

# Git Tag

1. Search for tags with a particular pattern: `git tag -l 'v1.8.5*'`
1. Tag a commit: `git tag -a v1.2 9fceb02`
1. Sharing Tags: By default, the git push command doesn’t transfer tags to remote servers. You need push them explicitly to remote servers.
  * `git push origin [tagname]`
  * `git push origin --tags`

### Create Tags
1. Annotated Tags
  * Annotated tags, are stored as full objects in the Git database. They’re checksummed; contain the tagger name, e-mail, and date; have a tagging message; and can be signed and verified with GNU Privacy Guard (GPG). It’s generally recommended that you create annotated tags so you can have all this information; but if you want a temporary tag or for some reason don’t want to keep the other information, lightweight tags are available too. `-a` is for annoted.

`git tag -a v1.4 -m 'my version 1.4'`

1. Lightweight Tag:  It is very much like a branch that doesn’t change—it’s just a pointer to a specific commit. This is basically the commit checksum stored in a file—no other
information is kept.

`git tag v1.4-lw`



# Git Branching
### Git Branch Overview
1. A branch in Git is a simple movable pointer or reference to the head of a line of work (or to one of the commits).
1. The **HEAD** file is a symbolic reference to the branch you’re currently on. By symbolic reference, we mean that
unlike a normal reference, it doesn’t generally contain a SHA-1 value but rather a pointer to another reference.
1. When you run `git commit`, it creates the commit object, specifying the parent of that commit object to be
whatever SHA-1 value the reference in HEAD points to.
1. When you make a commit, Git stores a commit object that contains a pointer to the snapshot of the content you staged. This object also contains the author’s name and email, the message that you typed, and pointers to the commit or commits that directly came before this commit (its parent or parents): zero parents for the initial
commit, one parent for a normal commit, and multiple parents for a commit that results from a merge of two or more branches.
1. To visualize this, let’s assume that you have a directory containing three files, and you stage them all and commit.
  * Staging the files checksums each one, stores that version of the file in the Git repository (Git refers to them as blobs), and adds that checksum to the staging area:
  ```$ git add README test.rb LICENSE```
  * When you create the commit by running `git commit`, Git checksums each subdirectory and stores those tree objects in the Git repository. Git then creates a commit object that has the metadata and a pointer to the root project tree so it can re-create that snapshot when needed.
  ```$ git commit -m 'initial commit of my project'```

1. Your Git repository now contains five objects: 
  * One blob for the contents of each of your three files
  * One tree that lists the contents of the directory and specifies which file names are stored as which blobs
  * One commit with the pointer to that root tree and all the commit metadata.

1. If you make some changes and commit again, the next commit stores a pointer to the commit that came immediately before it.



### HEAD
1. `git branch testing`: This creates a new pointer at the same commit you’re currently on.

How does Git know what branch you’re currently on? It keeps a special pointer called HEAD. In Git, this is a pointer to the local branch you’re currently on. In this case, you’re still on master. The git branch command only created a new branch—it didn’t switch to that branch.

1. `git checkout testing`: This moves HEAD to point to the `testing` branch, basically switch the branch.
1. Create a branch and switch to it in one command: `git checkout -b testing`

### Switching branches changes files in your working directory
1. It’s important to note that when you switch branches in Git, files in your working directory will change. If you switch to an older branch, your working directory will be reverted to look like it did the last time you committed on that branch. If Git cannot do it cleanly, it will not let you switch at all.

1. Branches are cheap to create and destroy: Because a branch in Git is in actuality a simple file that contains the 40 character SHA-1 checksum of the commit it points to. Creating a new branch is as quick and simple as writing 41 bytes to a file (40 characters and a newline).

`cat .git/refs/heads/testing`


1. However, before you can switch branch, note that if your working directory or staging area has uncommitted changes that conflict with the branch you’re checking out, Git won’t let you switch branches. It’s best to have a clean working state when you switch branches. There are ways to get around this (namely, `stashing` and `commit amending`)

1. **This is an important point to remember**: when you switch branches, Git resets your working directory to look like it did the last time you committed on that branch. It adds, removes, and modifies files automatically to make sure your working copy is what the branch looked like on your last commit to it.


### Basic Branching and Merging

1. Merge with a feature branch. Suppose you are on 'master' branch: `git merge hotfix`
  * Because the commit on the branch you’re on isn’t a direct ancestor of the branch you’re merging in, Git has to do some work. In this case, Git does a simple **three-way merge**, using the two snapshots pointed to by the branch tips and the common ancestor of the two. 
  * Instead of just moving the branch pointer forward, Git creates a new snapshot that results from this three-way merge and automatically creates a new commit that points to it. This is referred to as a **merge commit**, and is special in that it has more than one parent. 

1. Merge Conflicts: `git mergetool`

### Branch Management
1. `git branch -v`: see the last commit on each branch
1. `git branch --merged`: Filter the list to branches that you have or have not yet merged (`--no-merged`) into the branch you're currently on.

### Branch Workflow

##### Long-Running Branches

The idea is that your branches are at various levels of stability; when they reach a more stable level, they’re merged into the branch above them. Again, having multiple long-running branches isn’t necessary, but it’s often helpful, especially when you’re dealing with very large or complex projects.

##### Topic Branches
##### Remote Branches
Remote branches are references (pointers) to the state of branches in your remote repositories. They’re local branches that you can’t move; they’re moved automatically for you whenever you do any network communication. Remote branches act as bookmarks to remind you where the branches on your remote repositories were the last time you connected to them.

##### "ORIGIN" is NOT special

To synchronize your work, you run a `git fetch origin` command. This command looks up which server “origin”
is (in this case, it’s git.ourcompany.com), fetches any data from it that you don’t yet have, and updates your local
database, moving your origin/master pointer to its new, more up-to-date position.

##### Pushing to a Branch
1. `git push (remote) (branch)`:
  * Example: `git push origin serverfix`
  * Git automatically expands the `serverfix` branch name out to `refs/heads/serverfix:refs/heads/serverfix`, which means, “Take the `serverfix` local branch and push it to update the remote’s `serverfix` branch.” 
  * Use `push origin serverfix:remote_branch_name` to do the same thing but with different remote branch name

1. It’s important to note that when you do a fetch that brings down new remote branches, you don’t automatically have local, editable copies of them. In other words, in this case, you don’t have a new serverfix branch—you only have an `origin/serverfix` pointer that you can’t modify.
  * To merge this work into your current working branch, you can run `git merge origin/serverfix`. 

##### Tracking Branches

1. Checking out a local branch from a remote branch automatically creates what is called a “tracking branch”
(or sometimes an “upstream branch”). 
  * `git checkout -b [branch] [remotename]/[branch]`
  * Tracking branches are local branches that have a direct relationship to a remote branch. If you’re on a tracking branch and type `git push`, Git automatically knows which server and branch to push to. Also, running `git pull` while on one of these branches fetches all the remote references and then automatically merges in the corresponding remote branch.
  * `git checkout --track origin/serverfix` this is the same as `git checkout -b [branch] [remotename]/[branch]`

1. If you already have a local branch and want to set it to a remote branch you just pulled down, or want to change the upstream branch you’re tracking, you can use the `-u` or `--set-upstream-to` option to git branch to explicitly set it at any time.

```
$ git branch -u origin/serverfix
Branch serverfix set up to track remote branch serverfix from origin.
```
1. `git fetch --all; git branch -vv` lists tracking branches

1. Upstream Shorthand: you can reference a tracking upstream branch with the `@{upstream}` or `@{u}` shorthand. So if
you’re on the master branch and it’s tracking `origin/master`, you can say something like `git merge @{u}` instead
of `git merge origin/master` if you want.

##### Pulling
1. `git pull` is essentially a `git fetch` immediately followed by a `git merge` in most cases.
1. Generally it’s better to simply use the `fetch` and `merge` commands explicitly as the magic of `git pull` can often be confusing.

##### Deleting Remote Branches
1. `git push origin --delete serverfix`: Basically all this does is remove the pointer from the server. The Git server will generally keep the data there for a
while until a garbage collection runs, so if it was accidentally deleted, it’s often easy to recover.

# Git Rebase
##### Git Rebase Overview
1. There are two main ways to integrate changes from one branch into another: **the merge and the rebase**.

1. The easiest way to integrate the branches is the merge command. It performs a three-way merge between the two latest branch snapshots and the most recent common ancestor of the two, creating a new snapshot (and commit).
1. There is another way: With the rebase command, you can take all the changes that were committed on one branch and replay them on another one.
1. It works by going to the common ancestor of the two branches (the one you’re on and the one you’re rebasing onto), getting the diff introduced by each commit of the branch you’re on, saving those diffs to temporary files, resetting the current branch to the same commit as the branch you are rebasing onto, and finally applying each change in turn.

```
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

1. At this point, you can go back to the master branch and do a fast-forward merge. Here is no difference in the end product of the integration, but rebasing makes for a cleaner history.
  * If you examine the log of a rebased branch, it looks like a linear history: it appears that all the work happened in series, even when it originally happened in parallel.
  * Often, you’ll do this to make sure your commits apply cleanly on a remote branch – perhaps in a project to which you’re trying to contribute but that you don’t maintain. In this case, you’d do your work in a branch and then rebase your work onto `origin/master` when you were ready to submit your patches to the main project. That way, the maintainer doesn’t have to do any integration work—just a fast-forward or a clean apply.
  * Note that the snapshot pointed to by the final commit you end up with, whether it’s the last of the rebased commits for a rebase or the final merge commit after a merge, is the same snapshot—it’s only the history that is different. 
  * Rebasing replays changes from one line of work onto another in the order they were introduced, whereas merging takes the endpoints and merges them.

1. You can also have your rebase replay on something other than the rebase target branch.
  * Suppose you are on 'client' branch, you have 'master', 'server', 'client' branches, 
  * Suppose you decide that you want to merge your client-side changes into your mainline for a release, but you want to hold off on the server-side changes until it’s tested further. You can take the changes on 'client' that aren’t on 'server' and replay them on your master branch by using the `--onto` option of `git rebase`:

```
$ git rebase --onto master server client
```

This basically says, “Check out the client branch, figure out the patches from the common ancestor of the 'client'
and 'server' branches, and then replay them onto 'master'.”

Now you can fast-forward your master branch:
```
$ git checkout master
$ git merge client
```

1. Checkout 'topicbranch' and replays it onto the base branch. 
`git rebase [basebranch] [topicbranch]`

Now fast-forward the base branch
`git checkout master`
`git merge server`

##### The Perils of Rebasing

1. Do not rebase commits that exist outside your repository.
  * When you rebase stuff, you’re abandoning existing commits and creating new ones that are similar but different. If you push commits somewhere and others pull them down and base work on them, and then you rewrite those commits with git rebase and push them up again, your collaborators will have to re-merge their work and things will
get messy when you try to pull their work back into yours.
  * It turns out that in addition to the commit SHA checksum, Git also calculate a checksum that is based just on the patch introduced with the commit. This is called a “patch-id.” If you pull down work that was rewritten and rebase it on top of the new commits from your partner, Git can often successfully figure out what is uniquely yours and apply them back on top of the new branch.
  * If you treat rebasing as a way to clean up and work with commits before you push them, and if you only rebase commits that have never been available publicly, then you’ll be fine. 
  * If you rebase commits that have already been pushed publicly, and people may have based work on those commits, then you may be in for some frustrating trouble, and the scorn of your teammates.
1. In general the way to get the best of both worlds is to rebase local changes you’ve made but haven’t shared yet before you push them in order to clean up your story, but never rebase anything you’ve pushed somewhere.

1. A remote repository is generally a bare repository—a Git repository that has no working directory. Because the repository is only used as a collaboration point, there is no reason to have a snapshot checked out on disk; it’s just the Git data. In the simplest terms, a bare repository is the contents of your project’s .git directory and nothing else.

# Distributed Git
In Git, however, every developer is potentially both a node and a hub—that is, every developer can both contribute code to other repositories and maintain a public repository on which others can base their work and which they can contribute to.


##### Centralized Workflow
1. One central hub, or repository, can accept code, and everyone synchronizes their work to it. A number of developers are nodes—consumers of that hub—and synchronize to that one place.

##### Integration-Manager Workflow
1. Because Git allows you to have multiple remote repositories, it’s possible to have a workflow where each developer has write access to their own public repository and read access to everyone else’s. This scenario often includes a canonical repository that represents the “official” project. 
1. To contribute to that project, you create your own public clone of the project and push your changes to it. Then, you can send a request to the maintainer of the main project to pull in your changes. 
1. The maintainer can then add your repository as a remote, test your changes locally, merge them into their branch, and push back to their repository.

1. This is a very common workflow with hub-based tools such as GitHub or GitLab, where it’s easy to fork a project and push your changes into your fork for everyone to see. One of the main advantages of this approach is that you can continue to work, and the maintainer of the main repository can pull in your changes at any time. Contributors don’t
have to wait for the project to incorporate their changes—each party can work at their own pace.

##### Dictator and Lieutenants Workflow
1. This is a variant of a multiple-repository workflow. 
1. It’s generally used by huge projects with hundreds of collaborators;
  * One famous example is the Linux kernel. 
  * Various integration managers are in charge of certain parts of the repository; they’re called lieutenants. 
  * All the lieutenants have one integration manager known as the benevolent dictator. The benevolent dictator’s repository serves as the reference repository from which all the collaborators need to pull.

##### Contributing to a Project
1. Some of the variables involved are active contributor count, chosen workflow, your commit access, and possibly the external contribution method.

##### Commit Guidelines

1. First, you don’t want to submit any whitespace errors. Git provides an easy way to check for this—before you commit, run `git diff --check`, which identifies possible whitespace errors and lists them for you.

1. Next, try to make each commit a logically separate changeset. If some of the changes modify the same file, try to use `git add --patch` to partially stage files.

1. The last thing to keep in mind is the commit message. Getting in the habit of creating quality commit messages makes using and collaborating with Git a lot easier. As a general rule, your messages should start with a single line that’s no more than about 50 characters and that describes the changeset concisely, followed by a blank line, followed by a more detailed explanation.

  * The Git project requires that the more detailed explanation include your motivation for the change and contrast its implementation with previous behavior—this is a good guideline to follow. 
  * It’s also a good idea to use the imperative present tense in these messages. In other words, use commands. Instead of “I added tests for” or “Adding tests for,” use “Add tests for.”

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

1. The Git project has well-formatted commit messages—try running `git log --no-merges` there to see what a nicely formatted project-commit history looks like.

##### Private Small Team
1. “Private,” in this context, means closed-source—not accessible to the outside world. You and the other developers all have push access
to the repository.

1. Jessica thinks her topic branch is ready, but she wants to know what she has to merge into her work so that she can push. She runs git log to find out:
```
$ git log --no-merges issue54..origin/master
commit 738ee872852dfaa9d6634e0dea7a324040193016
Author: John Smith <jsmith@example.com>
Date: Fri May 29 16:01:27 2009 -0700
removed invalid default value
```

The issue54..origin/master syntax is a log filter that asks Git to only show the list of commits that are on the latter branch (in this case origin/master) that are not on the first branch (in this case issue54).

##### Private Managed Team
You’ll learn how to work in an environment where small groups collaborate on features and then those team-based contributions are integrated by another party.
In this scenario, all work is done in team-based branches and pulled together by the integrators later

`git push -u origin featureB:featureBee`

This is called a `refspec`. Also notice the `-u` flag; this is short for `--set-upstream`, which configures the branches for easier pushing and pulling later.

##### Public Project, Fork
1. You clone the public project, make a feature branch, work on it.
1. You create a fork of that project. 
1. Make the fork your second remote
`git remote add myfork (url)`

1. Push your work to the new remote
  * It’s easiest to push the remote branch you’re working on up to your repository, rather than merging into your master branch and pushing that up. The reason is that if the work isn’t accepted or is cherry picked, you don’t have to rewind your master branch. If the maintainers merge, rebase, or cherry-pick your work, you’ll eventually get it back via pulling from their repository anyhow:
  
```
$ git push -u myfork featureA
```

1. When your work has been pushed up to your fork, you need to notify the maintainer. This is often called a **pull request**.
1. On a project for which you’re not the maintainer, it’s generally easier to have a branch like master always track origin/master and to do your work in topic branches that you can easily discard if they’re rejected. Having work themes isolated into topic branches also makes it easier for you to rebase your work if the tip of the main repository
has moved in the meantime and your commits no longer apply cleanly.

```
$ git checkout -b featureB origin/master
```

```
git push -f myfork featureA
```

specify the `-f` to your push command in order to be able to replace the `featureA` branch on the server with a commit that isn’t a descendant of it. An alternative would be to push this new work to a different branch on the server (perhaps called featureAv2).

1. Let’s look at one more possible scenario: the maintainer has looked at work in your second branch and likes the concept but would like you to change an implementation detail. You’ll also take this opportunity to move the work to be based off the project’s current master branch. You start a new branch based off the current origin/master branch, squash the featureB changes there, resolve any conflicts, make the implementation change, and then push that up as a new branch:

```
$ git checkout -b featureBv2 origin/master^{}
$ git merge --no-commit --squash featureB
# (change implementation)
$ git commit
$ git push myfork featureBv2
```

The --squash option takes all the work on the merged branch and squashes it into one non-merge commit on
top of the branch you’re on. The --no-commit option tells Git not to automatically record a commit. This allows you to
introduce all the changes from another branch and then make more changes before recording the new commit.


##### Maintaining a Project

1. When you’re thinking of integrating new work, it’s generally a good idea to try it out in a topic branch—a temporary branch specifically made to try out that new work. This way, it’s easy to tweak a patch individually and leave it if
it’s not working until you have time to come back to it. If you create a simple branch name based on the theme of
the work you’re going to try
you can create the branch based off your master branch like this:
`$ git branch sc/ruby_client master`

Or, if you want to also switch to it immediately, you can use the checkout -b option:
`$ git checkout -b sc/ruby_client master`

##### Checking out remote branches

you can test it by adding the remote and checking out that branch locally:
```
$ git remote add jessica git://github.com/jessica/myproject.git
$ git fetch jessica
$ git checkout -b rubyclient jessica/ruby-client
```
If you aren’t working with a person consistently but still want to pull from them in this way, you can provide the
URL of the remote repository to the git pull command. This does a one-time pull and doesn’t save the URL as a
remote reference:
$ git pull https://github.com/onetimeguy/project
From https://github.com/onetimeguy/project
* branch HEAD -> FETCH_HEAD
Merge made by recursive.

It’s often helpful to get a review of all the commits that are in this branch but that aren’t in your master branch.
You can exclude commits in the master branch by adding the --not option before the branch name. This does the
same thing as the master..contrib format that we used earlier.

```
git log contrib --not master
```

To see what changes each commit introduces, remember that you can pass the -p option to git log and it will
append the diff introduced to each commit.

To see a full diff of what would happen if you were to merge this topic branch with another branch, you may have
to use a weird trick to get the correct results. You may think to run this:
`$ git diff master`

This command gives you a diff, but it may be misleading. If your master branch has moved forward since you
created the topic branch from it, then you’ll get seemingly strange results. This happens because Git directly compares
the snapshots of the last commit of the topic branch you’re on and the snapshot of the last commit on the master
branch. For example, if you’ve added a line in a file on the master branch, a direct comparison of the snapshots will
look like the topic branch is going to remove that line.

If master is a direct ancestor of your topic branch, this isn’t a problem; but if the two histories have diverged, the
diff will look like you’re adding all the new stuff in your topic branch and removing everything unique to the master
branch.

What you really want to see are the changes added to the topic branch—the work you’ll introduce if you merge
this branch with master. You do that by having Git compare the last commit on your topic branch with the first
common ancestor it has with the master branch.

Technically, you can do that by explicitly figuring out the common ancestor and then running your diff on it:
```
$ git merge-base contrib master
36c7dba2c95e6bbb78dfa822519ecfec6e1ca649
$ git diff 36c7db
```
However, that isn’t convenient, so Git provides another shorthand for doing the same thing: the triple-dot syntax.
In the context of the —command, you can put three periods after another branch to do a —between the last commit
of the branch you’re on and its common ancestor with another branch:
```
$ git diff master...contrib
```
This command shows you only the work your current topic branch has introduced since its common ancestor
with master. That is a very useful syntax to remember.

##### Integrating Contributed Work
1. Merging Workflows

One simple workflow merges your work into your master branch. In this scenario, you have a master branch
that contains basically stable code. When you have work in a topic branch that you’ve done or that someone has
contributed and you’ve verified, you merge it into your master branch, delete the topic branch, and then continue
the process.

That is probably the simplest workflow, but it can possibly be problematic if you’re dealing with larger or more
stable projects where you want to be really careful about what you introduce

If you have a more important project, you might want to use a two-phase merge cycle. In this scenario, you have
two long-running branches, master and develop, in which you determine that master is updated only when a very
stable release is cut and all new code is integrated into the develop branch. You regularly push both of these branches
to the public repository. Each time you have a new topic branch to merge in (before a topic branch merge), you merge
it into develop (after a topic branch merge); then, when you tag a release, you fast-forward master to wherever the
now-stable develop branch is (after a project release).

This way, when people clone your project’s repository, they can either check out master to build the latest stable
version and keep up to date on that easily, or they can check out develop, which is the more cutting-edge stuff. You
can also continue this concept, having an integrate branch where all the work is merged together. Then, when the
codebase on that branch is stable and passes tests, you merge it into a develop branch; and when that has proven
itself stable for a while, you fast-forward your master branch.

1. Large-Merging Workflows
1. Rebasing and Cherry Picking Workflows

Other maintainers prefer to rebase or cherry-pick contributed work on top of their master branch, rather than
merging it in, to keep a mostly linear history. When you have work in a topic branch and have determined that you
want to integrate it, you move to that branch and run the rebase command to rebuild the changes on top of your
current master (or develop, and so on) branch. If that works well, you can fast-forward your master branch, and you’ll
end up with a linear project history.

The other way to move introduced work from one branch to another is to cherry-pick it. A cherry-pick in Git
is like a rebase for a single commit. It takes the patch that was introduced in a commit and tries to reapply it on
the branch you’re currently on.

`git cherry-pick e43a6fd3e94888d76779ad79fb568ed180e5fcdf`

##### Generating a Build Number

if you want to have a human-readable name to go with a commit, you can run git describe on that commit. Git gives you
the name of the nearest tag with the number of commits on top of that tag and a partial SHA-1 value of the commit
you’re describing:
```
$ git describe master
v1.6.2-rc1-20-g8c5b85c
```

#### The Shortlog

It summarizes all the commits in the range you give it; for example, the following gives you a
summary of all the commits since your last release, if your last release was named v1.0.1:
```
$ git shortlog --no-merges master --not v1.0.1
```































# Git Tricks
### Branch References
The most straightforward way to specify a commit requires that it have a branch reference pointed at it. If you want to see which specific SHA a branch points to, you can use a Git plumbing tool called `rev-parse`

`git rev-parse topic1`


### RefLog Shortnames
1. `git reflog`
  * One of the things Git does in the background while you’re working away is keep a “reflog”—a log of where your *HEAD* and branch references have been for the last few months.
  * Every time your branch tip is updated for any reason, Git stores that information for you in this temporary history. (`.git/logs/refs`)
  * And you can specify older commits with this data, as well. If you want to see the fifth prior value of the HEAD of your repository, you can use the `@{n}` reference that you see in the reflog output:
  ```
  $ git show HEAD@{5}
  ```
For instance, to see where your master branch was yesterday, you can type
`$ git show master@{yesterday}`

  * This technique only works for data that’s still in your reflog, so you can’t use it to look for commits older than a few months
  * To see reflog information formatted like the `git log` output, you can run `git log -g`: `git log -g master`

1. It’s important to note that the ref log information is strictly local—it’s a log of what you’ve done in your repository.
  * The references won’t be the same on someone else’s copy of the repository; and right after you initially clone a repository, you’ll have an empty reflog, as no activity has occurred yet in your repository.

### Ancestry References
1. The other main way to specify a commit is via its ancestry. If you place a `^` at the end of a reference, Git resolves it to mean the parent of that commit.
1. `git show HEAD^`: You can see the previous commit by specifying `HEAD^`, which means “the parent of HEAD.”
1. You can also specify a number after the `^`—for example, `d921970^2` means “the second parent of d921970.” This syntax is only useful for merge commits, which have more than one parent. The first parent is the branch you were on when you merged, and the second is the commit on the branch that you merged in
1. The other main ancestry specification is the `~`. This also refers to the first parent, so `HEAD~` and `HEAD^` are equivalent. The difference becomes apparent when you specify a number. `HEAD~2` means “the first parent of the first parent,” or “the grandparent”—it traverses the first parents the number of times you specify.

### Commit Ranges
##### Double Dot
1. This basically asks Git to resolve a range of commits that are reachable from one commit but aren’t reachable from another.
1. You want to see what is in your "experiment" branch that hasn’t yet been merged into your "master" branch. You can ask Git to show you a log of just those commits with `master..experiment`—that means “all commits reachable by experiment that aren’t reachable by master.”

`git log master..experiment`

1. If you want to see the opposite—all commits in master that aren’t in "experiment"—you can reverse the branch names. `experiment..master` shows you everything in master not reachable from experiment

1. Another very frequent use of this syntax is to see what you’re about to push to a remote

```
git log origin/master..HEAD
```

This command shows you any commits in your current branch that aren’t in the master branch on your origin remote.

1. You can also leave off one side of the syntax to have Git assume HEAD. For example, you can get the same results as in the previous example by typing
`git log origin/master..` – Git substitutes HEAD if one side is missing.


##### Multiple Points

1. perhaps you want to specify more than two branches to indicate your revision, such as seeing what commits are in any of several branches that aren’t in the branch you’re currently on. Git allows you to do this by using either the `^` character or `--not` before any reference from which you don’t want to see reachable commits. Thus these three commands are equivalent

```
$ git log refA..refB
$ git log ^refA refB
$ git log refB --not refA
```

For instance, if you want to see all commits that are reachable from refA or refB but not from refC, you can type one of these:
```
$ git log refA refB ^refC
$ git log refA refB --not refC
```

##### Triple Dots
1. It specifies all the commits that are reachable by either of two references but not by both of them. If you want to see what is in "master" or "experiment" but not any common references, you can run
```
$ git log master...experiment
```

# Interactive Staging
1. `git add -i`: A few interactive commands that can help you easily craft your commits to include only certain combinations and parts of files.
1. These tools are very helpful if you modify a bunch of files and then decide that you want those changes to be in several focused commits rather than one big messy commit. 
1. If you run git add with the `-i` or `--interactive` option, Git goes into an interactive shell mode.

1. Staging and Unstaging Files: Select '2' or 'u' (update): 
1. Staging Patches
  * It’s also possible for Git to stage certain parts of files and not the rest. 
  * For example, if you make two changes to your `simplegit.rb` file and want to stage one of them and not the other. From the interactive
prompt, type `5` or `p` (for `patch`). Git will ask you which files you would like to partially stage; then, for each section of
the selected files, it will display hunks of the file diff and ask if you would like to stage them, one by one
  * You also don’t need to be in interactive add mode to do the partial-file staging—you can start the same script by using `git add -p` or `git add --patch` on the command line.
  * Furthermore, you can use patch mode for partially resetting files with the `reset --patch` command, for checking out parts of files with the `checkout --patch` command and for stashing parts of files with the `stash save --patch` command. 

# Stashing and Cleaning
1. `git stash apply stash@{1}`: You can see that Git remodifies the files you reverted when you saved the stash.
1. The changes to your files were reapplied, but the file you staged before wasn’t restaged. 
  * To do that, you must run the `git stash apply` command with a `--index` option to tell the command to try to reapply the staged changes. If you
had run that instead, you’d have gotten back to your original position.

### Creative Stashing
1. The first option that is quite popular is the `--keep-index` option to the stash save command. This tells Git to not stash anything that you’ve already staged with the `git add` command.
  * This can be really helpful if you’ve made a number of changes but want to only commit some of them and then come back to the rest of the changes at a later time.

1. Another common thing you may want to do with stash is to stash the untracked files as well as the tracked ones. By default, `git stash` will only store files that are already in the index. If you specify `--include-untracked` or `-u`, Git will also stash any untracked files you have created.
1. Finally, if you specify the `--patch` flag, Git will not stash everything that is modified but will instead prompt you interactively which of the changes you would like to stash and which you would like to keep in your working directory.
```
$ git stash --patch
```

### Unapplying a Stash
1. In some use case scenarios you might want to apply stashed changes, do some work, but then unapply those changes that originally came from the stash. Git does not provide such a stash unapply command, but it is possible to achieve the effect by simply retrieving the patch associated with a stash and applying it in reverse:
```
$ git stash show -p stash@{0} | git apply -R
```

Again, if you don’t specify a stash, Git assumes the most recent stash:
`$ git stash show -p | git apply -R`

You may want to create an alias and effectively add a stash-unapply command to your git. For example:

```
$ git config --global alias.stash-unapply '!git stash show -p | git apply -R'
$ git stash
$ #... work work work
$ git stash-unapply
```

### Creating a Branch from a Stash

`git stash branch testchanges`: which creates a new branch for you, checks out the commit you were on when you stashed your work, reapplies your work there, and then drops the stash if it applies successfully.

### Cleaning Your Working Directory
1. You may not want to stash some work or files in your working directory, but simply get rid of them. The `git clean` command will do this for you.
1. Some common reasons for this might be to remove craft that has been generated by merges or external tools or to remove build artifacts in order to run a clean build
1. **You’ll want to be pretty careful with this command, because it’s designed to remove files from your working directory that are not tracked.**  
  * If you change your mind, there is often no retrieving the content of those files. 
  * A safer option is to run `git stash --all` to remove everything but save it in a stash.

1. To remove all the untracked files in your working directory, you can run `git clean -f -d`, which removes any files and also any subdirectories that become empty as a result. The `-f` means force or “really do this.”
1. If you ever want to see what it would do, you can run the command with the  `-n` option, which means “do a dry run and tell me what you would have removed.”
1. By default, the `git clean` command will only remove untracked files that are not ignored. Any file that matches a pattern in your `.gitignore` or other ignore files will not be removed. If you want to remove those files too, such as to remove all `.o` files generated from a build so you can do a fully clean build, you can add a `-x` to the clean command.
1. The other way you can be careful about the process is to run it with the `-i` or “interactive” flag.

# Searching

### Git Grep
1. `git grep -n gmtime_r`:  By default, grep looks through the files in your working directory. You can pass `-n` to print out the line numbers
where Git has found matches.
1. you can have Git summarize the output by just showing you which files matched and how many matches there were in each file with the `--count` option
1. If you want to see what method or function it thinks it has found a match in, you can pass `-p`: `git grep -p gmtime_r *.c`
1. You can also look for complex combinations of strings with the --and flag, which makes sure that multiple matches are in the same line.

```
$ git grep --break --heading \
-n -e '#define' --and \( -e LINK -e BUF_MAX \) v1.8.0
```

1. The git grep command has a few advantages over normal searching commands such as `grep` and `ack`. 
  * The first is that it’s really fast
  * The second is that you can search through any tree in Git, not just the working directory. As we saw in the previous example, we looked for terms in an older version of the Git source code, not the version that was currently checked out.

### Git Log Search
1. we can tell Git to only show us the commits that either added or removed that string with the `-S` option.
`git log -Sstring_pattern --oneline`

If you need to be more specific, you can provide a regular expression to search for with the `-G` option.

### Line Log Search

1. It is called with the `-L` option to git log and shows you the history of a function or line of code in your codebase.
  * For example, if we wanted to see every change made to the function `git_deflate_bound` in the `zlib.c` file, we could run `git log -L :git_deflate_bound:zlib.c`. 
  * This tries to figure out what the bounds of that function are and then looks through the history and shows every change that was made to the function as a series of patches back to when the function was first created.
1. If Git can’t figure out how to match a function or method in your programming language, you can also provide it a regex. 
  * For example, this would have done the same thing: 
  ```
  git log -L '/unsigned long git_deflate_bound/',/^}/:zlib.c
  ```
  *You could also give it a range of lines or a single line number and you’ll get the same sort of output.

# Rewriting History

### Change the Last Commit

##### `git commit --amend`
1. You can change the message of your last commit
1. You Change the snapshot you just recorded: adding, changing, removing files
  * Stage the changes you want by editing a file and running `git add` on it or `git rm` to a tracked file
  * Then run `git commit --amend`, which takes your current staging area and makes it the snapshot for the new commit.
  
1. You need to be careful with this technique because amending changes the SHA-1 of the commit. 
  * It’s like a very small rebase
  * **Don’t amend your last commit if you’ve already pushed it. **

### Change Multiple Commit Messages

1. To modify a commit that is farther back in your history, you must move to more complex tools. 
  * You can use the rebase tool to rebase a series of commits onto the HEAD they were originally based on instead of moving them to another one. With the interactive rebase tool, you can then stop after each commit you want to modify and change the message, add files, or do whatever you wish. You can run rebase interactively by adding the `-i` option to `git rebase`. You must indicate how far back you want to rewrite commits by telling the command which commit to rebase onto.
  * For example, if you want to change the last three commit messages, or any of the commit messages in that group, you supply as an argument to `git rebase -i` the parent of the last commit you want to edit, which is `HEAD~2^` or `HEAD~3`: `$ git rebase -i HEAD~3`
  * Remember again that this is a rebasing command—every commit included in the range `HEAD~3..HEAD` will be rewritten, whether or not you change the message. 
  * **Don’t include any commit you’ve already pushed to a central server**: doing so will confuse other developers by providing an alternate version of the same change.
  * Running this command gives you a list of commits in your text editor that looks something like this:

```
pick f7f3f6d changed my name a bit
pick 310154e updated README formatting and added blame
pick a5f4a0d added cat-file
# Rebase 710f0f8..a5f4a0d onto 710f0f8
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

  * Notice the reverse order. It’s important to note that these commits are listed in the opposite order than you normally see them using the log command.
  * The interactive rebase gives you a script that it’s going to run. It will start at the commit you specify on the command line (`HEAD~3`) and replay the changes introduced in each of these commits from top to bottom. It lists the oldest at the top, rather than the newest, because that’s the first one it will replay. You need to edit the script so that it stops at the commit you want to edit. To do so, change the word pick to the word "edit" for each of the commits you want the script to stop after.

1. You can also use interactive rebases to reorder or remove commits entirely
1. It’s also possible to take a series of commits and squash them down into a single commit with the interactive rebasing
tool.
  * If, instead of “pick” or “edit,” you specify “squash,” Git applies both that change and the change directly before it
and makes you merge the commit messages.

##### Splitting a Commit
1. Splitting a commit undoes a commit and then partially stages and commits as many times as commits you want to end up with. Then, when the script drops you to the command line, you reset that commit, take the changes that have been reset, and create multiple commits out of them.

1. `git reset HEAD^` effectively undoes that commit and leave the modified files unstages.
  * Once again, this changes the SHAs of all the commits in your list, so make sure no commit shows up in that list
that you’ve already pushed to a shared repository.

##### The Nuclear Option: filter-branch
There is another history-rewriting option that you can use if you need to rewrite a larger number of commits in some scriptable way—for instance, changing your e-mail address globally or removing a file from every commit. The command is `filter-branch`, and it can rewrite huge swaths of your history, so you probably shouldn’t use it unless
your project isn’t yet public and other people haven’t based work off the commits you’re about to rewrite.

# Reset Demystified
1. An easier way to think about reset and checkout is through the mental frame of Git being a content manager of three
different trees. By “tree” here we really mean “collection of files,” not specifically the data structure. (There are a few
cases where the index doesn’t exactly act like a tree, but for our purposes it is easier to think about it this way for now.)

1. Git as a system manages and manipulates three trees in its normal operation:

|Tree|Role|
|---|---|
|HEAD|Last commit snapshot, next parent|
|Index| Proposed next commit snapshot|
|Working Directory| Sandbox|

#### The HEAD
HEAD is the pointer to the current branch reference, which is in turn a pointer to the last commit made on that
branch. That means HEAD will be the parent of the next commit that is created. It’s generally simplest to think of
HEAD as the snapshot of your last commit.

Here is an example of getting the actual directory
listing and SHA checksums for each file in the HEAD snapshot
`git cat-file -p HEAD`
`git ls-tree -r HEAD`

#### The Index

The Index is your proposed next commit. We’ve also been referring to this concept as Git’s “Staging Area” as this is
what Git looks at when you run git commit.

Git populates this index with a list of all the file contents that were last checked out into your Working Directory
and what they looked like when they were originally checked out. You then replace some of those files with new
versions of them, and git commit converts that into the tree for a new commit.

`git ls-files -s`

The Index is not technically a tree structure—it’s actually implemented as a flattened manifest—but for our
purposes it’s close enough.

#### The Working Directory

The other two trees store their content in an efficient but inconvenient
manner, inside the .git folder. The Working Directory unpacks them into actual files, which makes it much easier
for you to edit them. Think of the Working Directory as a sandbox, where you can try changes out before committing
them to your staging area (Index) and then to history.

`tree`

#### The Workflow
Git’s main purpose is to record snapshots of your project in successively better states, by manipulating these three trees.

Let’s say you go into a new directory with a single file in it. We’ll call this v1 of the file, and we’ll indicate it in blue.
Now we run git init, which creates a Git repository with a HEAD reference that points to an unborn branch (master
doesn’t exist yet).
At this point, only the Working Directory tree has any content.

we use git add to take content in the Working Directory and copy it to the Index.

Then we run git commit, which takes the contents of the Index and saves it as a permanent snapshot, creates a
commit object which points to that snapshot, and updates master to point to that commit.
If we run git status, we’ll see no changes, because all three trees are the same.

Now we want to make a change to that file and commit it. We’ll go through the same process, changing the file in
our Working Directory. Let’s call this v2 of the file, and indicate it in red.

If we run git status right now, we’ll see the file in red as “Changes not staged for commit,” because that entry
differs between the Index and the Working Directory. Next we run git add on it to stage it into our Index.

At this point if we run git status we will see the file in green under “Changes to be committed” because the
Index and HEAD differ – that is, our proposed next commit is now different from our last commit. Finally, we run
git commit to finalize the commit.

Switching branches or cloning goes through a similar process. When you checkout a branch, it changes HEAD to
point to the new branch ref, populates your Index with the snapshot of that commit, then copies the contents of the
Index into your Working Directory.

#### The Role of Reset
1. Step 1: Move HEAD
The first thing reset will do is move what HEAD points to. This isn’t the same as changing HEAD itself (which is what
checkout does); reset moves the branch that HEAD is pointing to. This means if HEAD is set to the master branch
(i.e., you’re currently on the master branch), running git reset 9e5e64a will start by making master point
to 9e5e64a.

No matter what form of reset with a commit you invoke, this is the first thing it will always try to do. With
reset --soft, it will simply stop there.
`git reset --soft HEAD~`

Now take a second to look at that diagram and realize what happened—it essentially undid the last git commit
command. When you run git commit, Git creates a new commit and moves the branch that HEAD points to up to it.
When you reset to HEAD~ (the parent of HEAD), you are moving the branch back to where it was, without changing the
Index or Working Directory. You could now update the Index and run git commit again to accomplish what
git commit --amend would have done.


1. Step 2: Updating the Index (--mixed)

Note that if you run git status now you’ll see in green the difference between the Index and what the new HEAD is.
The next thing reset will do is to update the Index with the contents of whatever snapshot HEAD now points to.

`git reset [--mixed] HEAD~`

If you specify the --mixed option, reset will stop at this point. This is also the default, so if you specify no option at
all (just git reset HEAD~ in this case), this is where the command will stop.

it still undid your last commit, but
also unstaged everything. You rolled back to before you ran all your git add and git commit commands.

1. Step 3: Updating the Working Directory (--hard)

The third thing that reset does is makes the Working Directory look like the Index. If you use the --hard option, it will
continue to this stage.

You undid your last commit, the git add and git commit commands,
and all the work you did in your Working Directory.
It’s important to note that this flag (--hard) is the only way to make the reset command dangerous, and one of
the very few cases where Git will actually destroy data. Any other invocation of reset can be pretty easily undone, but
the --hard option cannot, since it forcibly overwrites files in the Working Directory.

In this particular case, we still
have the v3 version of our file in a commit in our Git DB, and we could get it back by looking at our reflog, but if we
had not committed it, Git still would have overwritten the file and it would be unrecoverable.

Recap
The reset command overwrites these three trees in a specific order, stopping when you tell it to:
  1.  Move the branch HEAD points to (stop here if --soft)
  2.  Make the Index look like HEAD (stop here unless --hard)
  3.  Make the Working Directory look like the Index
  
#### Reset with a Path

If you specify
a path, reset will skip step 1, and limit the remainder of its actions to a specific file or set of files. This actually sort of
makes sense—HEAD is just a pointer, and you can’t point to part of one commit and part of another. But the Index
and Working Directory can be partially updated, so reset proceeds with steps 2 and 3.

`git reset file.txt`

So, assume we run git reset file.txt. This form (because you did not specify a commit SHA or branch, and
you didn’t specify --soft or --hard) is shorthand for git reset --mixed HEAD file.txt, which:
  1.  Moves the branch HEAD points to (skipped)
  2.  Makes the Index look like HEAD (stop here)
So it essentially just copies file.txt from HEAD to the Index.

This has the practical effect of unstaging the file. If we look at the diagram for that command and think about
what git add does, they are exact opposites.

We could just as easily not let Git assume we meant “pull the data from HEAD” by specifying a specific commit to
pull that file version from. We would just run something like git reset eb43bf file.txt.

This effectively does the same thing as if we had reverted the content of the file to v1 in the Working Directory, ran
git add on it, then reverted to v3 again (without actually going through all those steps). If we run git commit now, it
will record a change that reverts that file to v1, even though we never actually had it in our Working Directory again.
It’s also interesting to note that like git add, the reset command will accept a --patch option to unstage content
on a hunk-by-hunk basis. So you can selectively unstage or revert content.

#### Squashing

Say you have a series of commits with messages like “oops”, “WIP,” and “forgot this file.” You can use reset to
quickly and easily squash them into a single commit that makes you look really smart.

#### Check It Out
Like reset, checkout manipulates the
three trees, and it is a bit different depending on whether or not you give the command a file path.

##### Without Paths
Running git checkout [branch] is pretty similar to running git reset --hard [branch] in that it updates all three
trees for you to look like [branch], but there are two important differences.
1. First, unlike reset --hard, checkout is Working-Directory safe; it will check to make sure it’s not blowing away
files that have changes to them. Actually, it’s a bit smarter than that—it tries to do a trivial merge in the Working
Directory, so all the files you haven’t changed in will be updated. reset --hard, and on the other hand, will simply
replace everything across the board without checking.
1. The second important difference is how it updates HEAD. Where reset will move the branch that HEAD points
to, checkout will move HEAD itself to point to another branch.

##### With Paths

The other way to run checkout is with a file path, which, like reset, does not move HEAD. It is just like git reset
[branch] file in that it updates the index with that file at that commit, but it also overwrites the file in the working
directory. It would be exactly like git reset --hard [branch] file (if reset would let you run that)—it’s not Working-
Directory safe, and it does not move HEAD.
Also, like git reset and git add, checkout will accept a --patch option to allow you to selectively revert file
contents on a hunk-by-hunk basis.

Here’s a cheat-sheet for which commands affect which trees. The “HEAD” column reads “REF” if that command
moves the reference (branch) that HEAD points to, and “HEAD” if it moves HEAD itself. Pay especial attention to the
WD Safe? column—if it says NO, take a second to think before running that command.

HEAD Index Workdir WD Safe?
Commit Level
reset --soft [commit] REF NO NO YES
reset [commit] REF YES NO YES
reset --hard [commit] REF YES YES NO
checkout [commit] HEAD YES YES YES
File Level
reset (commit) [file] NO YES NO YES
checkout (commit)
[file]
NO YES YES NO


# Advanced Merging

Git’s philosophy is to be smart about determining when a merge
resolution is unambiguous, but if there is a conflict, it does not try to be clever about automatically resolving it.
Therefore, if you wait too long to merge two branches that diverge quickly, you can run into some issues.

##### Merge Conflicts

First of all, if at all possible, try to make sure your working directory is clean before doing a merge that may have
conflicts. If you have work in progress, either commit it to a temporary branch or stash it. This makes it so that you can
undo anything you try here. If you have unsaved changes in your working directory when you try a merge, some of
these tips may help you lose that work.

The git merge --abort option tries to revert to your state before you ran the merge. The only cases where it may
not be able to do this perfectly would be if you had unstashed, uncommitted changes in your working directory when
you ran it, otherwise it should work fine.
If for some reason you find yourself in a horrible state and just want to start over, you can also run git reset
--hard HEAD or wherever you want to get back to. Remember again that this will blow away your working directory, so
make sure you don’t want any changes there.

1. Ignoring Whitespace
The default merge strategy can take arguments though, and a few of them are about properly ignoring whitespace
changes. If you see that you have a lot of whitespace issues in a merge, you can simply abort it and do it again, this
time with -Xignore-all-space or -Xignore-space-change. The first option ignores changes in any amount of existing
whitespace, the second ignores all whitespace changes altogether.

1. Manual File Re-merging
First, we get into the merge conflict state. Then we want to get copies of my version of the file, their version (from
the branch we’re merging in), and the common version (from where both sides branched off ). Then we want to fix up
either their side or our side and re-try the merge again for just this single file.
Getting the three file versions is actually pretty easy. Git stores all these versions in the index under “stages,”
which each have numbers associated with them. Stage 1 is the common ancestor, stage 2 is your version, and stage 3 is
from the MERGE_HEAD, the version you’re merging in (“theirs”).
You can extract a copy of each of these versions of the conflicted file with the git show command and a special
syntax.

```
$ git show :1:hello.rb > hello.common.rb
$ git show :2:hello.rb > hello.ours.rb
$ git show :3:hello.rb > hello.theirs.rb
```

If you want to get a little more hard core, you can also use the ls-files -u plumbing command to get the actual
SHAs of the Git blobs for each of these files.
`git ls-files -u`

The :1:hello.rb is just shorthand for looking up that blob SHA.

`git merge-file -p hello.ours.rb hello.common.rb hello.theirs.rb > hello.rb`

If you want to get an idea before finalizing this commit about what was actually changed between one side or the
other, you can ask git diff to compare what is in your working directory that you’re about to commit as the result of
the merge to any of these stages.

To compare your result to what you had in your branch before the merge, in other words, to see what the merge
introduced, you can run `git diff --ours`:

If we want to see how the result of the merge differed from what was on their side, you can run git diff
--theirs

Finally, you can see how the file has changed from both sides with git diff --base.

##### Checking out conflitcs
Perhaps we’re not happy with the resolution at this point for some reason, or maybe manually editing one or both
sides still didn’t work well and we need more context.

For this example, we have two longer lived branches that each have a few
commits in them but create a legitimate content conflict when merged.

`$ git log --graph --oneline --decorate --all`

One helpful tool is git checkout with the --conflict option. This re-checkouts the file and replaces the merge
conflict markers. This can be useful if you want to reset the markers and try to resolve them again.
You can pass --conflict either diff3 or merge (which is the default). If you pass it diff3, Git will use a slightly
different version of conflict markers, not only giving you the “ours” and “theirs” versions, but also the “base” version
inline to give you more context.
```
$ git checkout --conflict=diff3 hello.rb
```

The git checkout command can also take --ours and --theirs options, which can be a really fast way of just
choosing either one side or the other without merging things at all.

##### Merge Log
To get a full list of all the unique commits that were included in either branch involved in this merge, we can use
the “triple dot” syntax.
```
$ git log --oneline --left-right HEAD...MERGE_HEAD
```

We can further simplify this though to give us much more specific context. If we add the --merge option to
git log, it will only show the commits in either side of the merge that touch a file that’s currently conflicted.
$ git log --oneline --left-right --merge
< 694971d update phrase to hola world
> c3ffff1 changed text to hello mundo
If you run that with the -p option instead, you get just the diffs to the file that ended up in conflict. This can be
really helpful in quickly giving you the context you need to help understand why something conflicts and how to more
intelligently resolve it.

##### Combine Diff Format

Because Git stages any merge results that are successful, when you run git diff while in a conflicted merge state, you
only get what is currently still in conflict. This can be helpful to see what you still have to resolve.

When you run git diff directly after a merge conflict, it will give you information in a rather unique diff output format.

The format is called “Combined Diff” and gives you two columns of data next to each line. The first column
shows you if that line is different (added or removed) between the “ours” branch and the file in your working directory
and the second column does the same between the “theirs” branch and your working directory copy.
So in that example you can see that the <<<<<<< and >>>>>>> lines are in the working copy but were not in either
side of the merge. This makes sense because the merge tool stuck them in there for our context, but we’re expected to
remove them.

You can also get this from the git log for any merge after the fact to see how something was resolved after the
fact. Git will output this format if you run git show on a merge commit, or if you add a --cc option to a git log -p
(which by default only shows patches for non-merge commits).
```
$ git log --cc -p -1
```

#### Undoing Merges
1. Fix the References

If the unwanted merge commit only exists on your local repository, the easiest and best solution is to move the
branches so that they point where you want them to. In most cases, if you follow the errant git merge with git reset
--hard HEAD~, this will reset the branch pointers so they look like this

The downside of this approach is that it’s rewriting history, which can be problematic with a shared repository.
If other people have the commits you’re rewriting, you should probably avoid reset. This approach also won’t work if
any other commits have been created since the merge; moving the refs would effectively lose those changes.

1. Reverse the Commit

Git gives you the option of making a new commit
that undoes all the changes from an existing one. Git calls this operation a “revert,” and in this particular scenario,
you’d invoke it like this:
```
$ git revert -m 1 HEAD
```
The -m 1 flag indicates which parent is the “mainline” and should be kept. When you invoke a merge into HEAD
(git merge topic), the new commit has two parents: the first one is HEAD (C6), and the second is the tip of the
branch being merged in (C4). In this case, we want to undo all the changes introduced by merging in parent #2 (C4),
while keeping all the content from parent #1 (C6).

Git will get confused if you try to merge
topic into master again:
$ git merge topic
Already up-to-date.

There’s nothing in topic that isn’t already reachable from master. What’s worse, if you add work to topic and
merge again, Git will only bring in the changes since the reverted merge

The best way around this is to un-revert the original merge, because now you want to bring in the changes that
were reverted out, then create a new merge commit:
```
$ git revert ^M
[master 09f0126] Revert "Revert "Merge branch 'topic'""
$ git merge topic
```

So far we’ve covered the normal merge of two branches, normally handled with what is called the “recursive” strategy
of merging.

#### Other Types of Merges
##### Ours or Theirs Preference

By default, when Git sees a conflict between two branches being merged, it will add merge conflict markers into
your code and mark the file as conflicted and let you resolve it. If you would prefer for Git to simply choose a specific
side and ignore the other side instead of letting you manually merge the conflict, you can pass the merge command
either a -Xours or -Xtheirs.
If Git sees this, it will not add conflict markers. Any differences that are mergeable, it will merge. Any differences
that conflict, it will simply choose the side you specify in whole, including binary files


# Debugging with Git
1. File Annotation

If you track down a bug in your code and want to know when it was introduced and why, file annotation is often your
best tool. It shows you what commit was the last to modify each line of any file. So, if you see that a method in your
code is buggy, you can annotate the file with git blame to see when each line of the method was last edited and by
whom. This example uses the -L option to limit the output to lines 12 through 22:

`git blame -L 12,22 simplegit.rb` 

Also note the ^4832fe2
commit lines, which designate that those lines were in this file’s original commit. That commit is when this file was
first added to this project, and those lines have been unchanged since. This is a tad confusing, because now you’ve
seen at least three different ways that Git uses the ^ to modify a commit SHA, but that is what it means here

Another cool thing about Git is that it doesn’t track file renames explicitly. It records the snapshots and then
tries to figure out what was renamed implicitly, after the fact. One of the interesting features of this is that you
can ask it to figure out all sorts of code movement as well. If you pass -C to git blame, Git analyzes the file you’re
annotating and tries to figure out where snippets of code within it originally came from if they were copied from
elsewhere.
`git blame -C -L 141,153 GITPackUpload.m`

1. Binary Search

Annotating a file helps if you know where the issue is to begin with. If you don’t know what is breaking, and there
have been dozens or hundreds of commits since the last state where you know the code worked, you’ll likely turn to
git bisect for help. The bisect command does a binary search through your commit history to help you identify as
quickly as possible which commit introduced an issue.


First you run git bisect start to get things going, and then you use
git bisect bad to tell the system that the current commit you’re on is broken. Then, you must tell bisect when the
last known good state was, using git bisect good [good_commit]:
```
$ git bisect start
$ git bisect bad
$ git bisect good v1.0
Bisecting: 6 revisions left to test after this
[ecb6e1bc347ccecc5f9350d878ce677feb13d3b2] error handling on repo
```
Git figured out that about 12 commits came between the commit you marked as the last good commit (v1.0) and
the current bad version, and it checked out the middle one for you. At this point, you can run your test to see whether
the issue exists as of this commit. If it does, then it was introduced sometime before this middle commit; if it doesn’t,
then the problem was introduced sometime after the middle commit. It turns out there is no issue here, and you tell
Git that by typing git bisect good and continue your journey

Now you’re on another commit, halfway between the one you just tested and your bad commit. You run your test
again and find that this commit is broken, so you tell Git that with git bisect bad


When you’re finished, you should run git bisect reset to reset your HEAD to where you were before you
started, or you’ll end up in a weird state

In fact, if
you have a script that will exit 0 if the project is good or non-0 if the project is bad, you can fully automate git bisect.
First, you again tell it the scope of the bisect by providing the known bad and good commits. You can do this by listing
them with the bisect start command if you want, listing the known bad commit first and the known good commit
second:
$ git bisect start HEAD v1.0
$ git bisect run test-error.sh
Doing so automatically runs test-error.sh on each checked-out commit until Git finds the first broken commit.
You can also run something such as make or make tests or whatever you have that runs automated tests for you.


#### Submodules
It often happens that while working on one project, you need to use another project from within it. Perhaps it’s a
library that a third party developed or that you’re developing separately and using in multiple parent projects. A
common issue arises in these scenarios: you want to be able to treat the two projects as separate yet still be able to use
one from within the other.


The issue with including
the library is that it’s difficult to customize the library in any way and often more difficult to deploy it, because you
need to make sure every client has that library available. The issue with vendoring the code into your own project is
that any custom changes you make are difficult to merge when upstream changes become available.
Git addresses this issue using submodules. Submodules allow you to keep a Git repository as a subdirectory of
another Git repository. This lets you clone another repository into your project and keep your commits separate.








# Git GUI
### gitk
1. A powerful GUI shell over `git log` and `git grep`. This is the tool to use when you’re trying to find something that happened in the past, or visualize your project’s history.
1. Probably one of the most useful is the `gitk --all`, which tells gitk to show commits reachable from any ref, not just HEAD.

### git-gui
`git gui`: is primarily a tool for crafting commits

### Git in Bash
1. If you’re a Bash user, you can tap into some of your shell’s features to make your experience with Git a lot friendlier.
1. Git actually ships with plugins for several shells, but it’s not turned on by default.
  * First, you need to get a copy of the `contrib/completion/git-completion.bash` file out of the Git source code.
  * Copy it somewhere handy, like your home directory, and add this to your `.bashrc`:
  ```
  . ~/git-completion.bash
  ```
  * Once that’s done, change your directory to a git repository, and type:
  ```
  $ git chec<tab>
  ```
  
…and Bash will auto-complete to git checkout. This works with all of Git’s subcommands, command-line
parameters, and remotes and ref names where appropriate.
  
1. It’s also useful to customize your prompt to show information about the current directory’s Git repository.
  * This can be as simple or complex as you want, but there are generally a few key pieces of information that most
people want, like the current branch and the status of the working directory.
  * To add these to your prompt, just copy the `contrib/completion/git-prompt.sh` file from Git’s source repository to your home directory, add something like
this to your `.bashrc`:

```
. ~/git-prompt.sh
export GIT_PS1_SHOWDIRTYSTATE=1
export PS1='\w$(__git_ps1 " (%s)")\$ '
```

The `\w` means print the current working directory, the `\$` prints the `$` part of the prompt, and `__git_ps1 " (%s)"`
calls the function provided by `git-prompt.sh` with a formatting argument. Now your bash prompt will look like this
when you’re anywhere inside a Git-controlled project

# Git Internals

### Git is a Content-Addressable Filesystem
1. Content-addressable filesystem: means that it's key-value data store. You can insert any kind of content into it, and it will give you back a key that you can use to retrieve the content again at any time. 

1. Git thinks about its data more like a **"stream of snapshots"**. Other VCS (Subversion etc.) think of the information as a set of files and the changes (deltas) made to each file over time.

1. All the content is stored as tree and blob objects, with trees corresponding to UNIX directory entries and blobs corresponding more or less to inodes or file contents. A single tree object contains one or more tree entries, each of which contains a SHA-1 pointer to a blob or subtree with its associated mode, type, and filename.

### Git Objects
#### Blob Objects
1. `objects`: stores all the content for your database
1. `git hash-object`: create hash key
1. `git cat-file`: show the contents 

#### Tree Objects
1. `git cat-file -p master^{tree}`

#### Commit Objects

#### Tag Objects
1. The tag object is very much like a commit object – it contains a tagger, a date, a message, and a pointer. The main difference is that a tag object generally
points to a commit rather than a tree. It’s like a branch reference, but it never moves—it always points to the same commit but gives it a friendlier name.
1. Also notice that it doesn’t need to point to a commit; you can tag any Git object.

#### Pack Files
1. The initial format in which Git saves objects on disk is called a “loose” object format.
1. However, occasionally Git packs up several of these objects into a single binary file called a “packfile” in order to save space and be more efficient. 
  * Git does this if you have too many loose objects around, 
  * If you run the `git gc` command manually, 
  * If you push to a remote server. 
  
1. To see what happens, you can manually ask Git to pack up the objects by calling the `git gc` command
1. The packfile is a single file containing the contents of all the objects that were removed from your filesystem. 
* The index is a file that contains offsets into that packfile so you can quickly seek to a specific object.
1. What is also interesting is that the second version of the file is the one that is stored intact, whereas the original version is stored as a delta—this is
because you’re most likely to need faster access to the most recent version of the file.

####
1. `refs`: stores pointers into commit objects in that data (branches)
1. `HEAD`: points to the branch you currently have checked out
1. `index`: where Git stores your staging area information



## Git Usage Examples

  



# References
1. Scott Chacon and Ben Straub, Pro Git, Second Edition, 2014.

