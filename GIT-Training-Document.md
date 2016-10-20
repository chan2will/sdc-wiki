**CONTENTS:**
* History
* Introduction
* Git Basic & Installation
* Staging Area
* Setup & Configuration
* Getting & Creating Projects
* Basic Snapshot

## **1. History:**
Git is a widely used version control systems for software development. 
Git was initially designed and developed by Linus Torvalds for Linux kernel development in April 2005 (10 years ago).
Git is primarily developed on Linux, although it also supports most major operating systems including BSD, Solaris, OS X, and Microsoft Windows.
## **2. Introduction:**
### **Git is a free and open source tool**
Distributed version control system designed to handle everything from small to very large projects with speed and efficiency.
### **Git is fast**
With Git, nearly all operations are performed locally, giving it a huge speed advantage on centralized systems that constantly have to communicate with a server somewhere.
### **Distributed**
One of the nicest features of any Distributed SCM, Git included, is that it's distributed. This means that instead of doing a "checkout" of the current tip of the source code, you do a "clone" of the entire repository.
### **SHA**
* SHA-1 produces a 160-bit (20-byte) hash value. A SHA-1 hash value is typically rendered as a hexadecimal number, 40 digits long.
* This hash is calculated based on the content of the files, the content of the directories, the complete history of up to the new commit, the committer, the commit message, and several other factors.
	**E.g.: 734713bc047d87bf7eac9674765ae793478c50d3**
* Git is smart enough to figure out what commit you meant to type if you provide the first few characters, as long as your partial SHA-1 is at least four characters long and unambiguous.
* The core of Git was originally written in the programming language C, but Git has also been re-implemented in other languages, **e.g.: Java, Ruby and Python.**

## **3. Git Basic & Installation**
### **Download & Install**
               - Git & Git Bash
			     http://git-scm.com/Latest source Release 2.5.3version
		       - Tortoisegit (Windows)
			     https://tortoisegit.org/download/Downloads 64 bit OS 1.8.15version
               - SmartGit or Pycharm with GIT or GITEYE etc… for Linux
* With Git Bash you’ll be able to use a number of UNIX command line tools along with access to Git and can run it by right clicking mouse on the desktop, and selecting Git Bash from pop up window.
* TortoiseGit is a widely used Git client for windows. TortoiseGit is a free open-source client for the Git version control system.

## **4. Staging Area:**
1. Unlike the other systems, Git has something called the "staging area" or "index". This is an intermediate area where commits can be formatted and reviewed before completing the commit.
2. Add selected changes to the something called the staging area and after wards we commit the changes stored in the staging area to the repository


## **5. Setup and configurations:**
### **Configuration**
Use –global to set the configuration for all projects. If git config is used without –global and run inside a project directory, the settings are set for the specific project.

    * git config --global user.name “Cnanda”
    * git config --global user.email “XXX@gmail.com”
### **Help**
Get help for a specific git command

    * git help clone

## **6. Getting and Creating Projects:**

### **git -version**

This command helps to check the git version

    * git  --version
### **git-init**

This command creates an empty Git repository basically '.git' directory with sub `		directories objects, refs/heads, refs/tags, and template files. An initial HEAD file 		that references the HEAD of the master branch is also created.

    * git init [directory]

### **git-clone**
Clone a repository into a new directory

    * git clone repository-url
      Ex: git clone git@github.com: srinivasbapathu/scc-api.git

## **7. Basic Snapshot:**

### **git-add**

Add file contents to the index. The index holds a snapshot of the content of the working tree, and it is this snapshot that is taken as the contents of the next commit. Thus after making any changes to the 	working directory, and before 		running the commit command, you must use the add command to add any new or modified files to the index

    * git add [file-name] [directory name]
      Ex:
         git add filename
         git add directorypath

### **git-status**

Show the working tree status. Displays paths that have differences between the index file and the current HEAD commit, paths that have differences between the working tree and the index file, and paths in the working tree that are not 		tracked by Git.

    * git status

### **git-commit**

Stores the current contents of the index in a new commit along with a log message from the user describing the changes.

    * git commit –m “message”

### **git-reset**

Reset current HEAD to the specified state or undo of add. For already committed the delete, then we can Reset to a commit before we deleted the files. Be warned that if we use reset, you will no longer see the log the commit(s) after the commit you reset to

    * git reset [<mode>] [<commit>]
      Ex:git reset --hard Head (reset the index and working tree, if any changes it will discarded)
         git reset --soft Head (Does not touch the index file or the working tree at all. This leaves all your changed  
                                files "Changes to be committed", as git status would put it.)

## **git-rm**

Remove files from the working tree and from the index.

    * git rm index.html

## **git-diff**

Show changes between commits, commit and working tree, etc.

    * git diff [options] <commit> <commit>

### **git-branch**

List, create, or delete branches

    * git branch [branchname]
    * git branch –d [branchname]

### **git-checkout**

Switch branches or restore working tree files.

    * git checkout [branchname]
    * git checkout –b [branchname] will creates a branch

### **git-log**

Show commit logs.

    * git log [<options>] [<revision range>]

### **git-stash**

Stash the changes in a dirty working directory away

    * git stash list [<options>]
    * git stash show [<stash>]

### **git-tag**

Create, list, delete or verify a tag object signed with GPG

    * git tag [option] [tagname]

### **git-fetch**

Download objects and refs from another repository

    * git fetch origin
	
### **git-pull**

git pull is git fetch followed by git merge.

    * git pull <remote>

### **git-revert**

Deleted few files and have not made a commit yet, Revert will work just fine. Selecting TortoiseGit -> Revert... will display a window for you to select the files you want restored. Deleted files will show in red.

### **git-push**

Update remote refs along with associated objects
		
    * git push --all origin master
    * Git push origin --tags
    * git push origin master branchname
	
### **git-remote**

Manage set of tracked repositories
	
    * git remote [-v | --verbose]
    * git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [–mirror=<fetch|push>] <name> <url>
    * git remote rename <old> <new>
    * git remote remove <name>

### **Edit conflicts**

* A conflict occurs when two or more developers have changed the same few lines of a file. As Git knows nothing of your project, it leaves resolving the conflicts to the developers. Whenever a conflict is reported, you should open the file in question, and search for lines starting with the string <<<<<<<. The conflicting area is marked like this.
* We can launch an external merge tool / conflict editor with TortoiseGit  → Edit Conflicts or you can use any other editor to manually resolve the conflict.
	
### **.ignore**
		
If we right click on one or more unversioned files, and select the command TortoiseGit→Add to Ignore List from the context menu, a sub menu appears allowing you to select ignore by names or by extensions. Ignore dialog shows that allows you to select ignore type and ignore file
	
### **Show log**

For every change we make and commit, we should provide a log message for that change. That way we can later find out what changes we made and why, and we have a detailed log for your development process.
	
### **Cleanup**
		
Remove untracked files from the working tree.
TortoiseGit→Cleanup Cleans the working tree by recursively removing files that are not under version control, starting from the current directory or on the whole working tree.

### **Export**

Sometimes you may want a copy of your working tree without any of those .git directories, e.g. to create a zipped tarball of your source, or to export to a web server. Instead of making a copy and then deleting all those .git directories 		manually, TortoiseGit offers the command 

    * TortoiseGit → Export