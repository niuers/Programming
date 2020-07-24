
1. Git is a Distributed Version Control Systems (DVCSs): 
  * Each client not only checks out the latest snapshot of the files, but the full mirror of the repository.
  * Every checkout is a full backup of all the data.
  
1. Git thinks about its data more like a "stream of snapshots". Other VCS (Subversion etc.) think of the information as a set of files and the changes made to each file over time.
This makes Git more like a mini filesystem.

1. Nearly Every Operation is Local

1. Git has Integrity: Everything in Git is check-summed before it is stored and is then referred to by that checksum. The mechanism that Git uses for this checksumming is called a SHA-1 hash. This is a 40-character string
composed of hexadecimal characters (0–9 and a–f) and calculated based on the contents of a file or directory
structure in Git. Git stores everything in its database not by filename but by the hash value of its contents.

1. Git Generally Only Adds Data: 

1. The Three States
  * Committed: data is safely stored in your local database
  * Modified: You changed the file but have not committed it to your database yet.
  * Staged: You have marked a modified file in its current version to go into your next commit snapshot.
  
1. The Three main sections of a Git project:
  * The Git directory: The Git directory is where Git stores the metadata and object database for your project.
  * The working directory: The working directory is a single checkout of one version of the project. These files are pulled out of the
compressed database in the Git directory and placed on disk for you to use or modify.
  * The staging area: The staging area is a file, generally contained in your Git directory, that stores information about what will go into
your next commit. It’s sometimes referred to as the “index”, but it’s also common to refer to it as the staging area.

1. Git configs
  * /etc/gitconfig
  * ~/.gitconfig
  * .git/config under each repository
  
  
  
1.   



