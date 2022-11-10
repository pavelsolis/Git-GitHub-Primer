# Git and GitHub Primer

With Git and GitHub, you can keep track of the changes in a project and have a backup online, without the need to save multiple versions of a file!

**Git** is a version control software, and **GitHub** is a hosting service for Git projects. Below are step-by-step instructions on how to work with them from scratch using the terminal (or [shell](https://www.earthdatascience.org/courses/intro-to-earth-data-science/open-reproducible-science/bash/)).
- A line starting with '$' refers to commands typed in the terminal, and a line starting with '>' represents output shown in the terminal.
- The most commonly used [commands in the terminal](https://www.earthdatascience.org/courses/intro-to-earth-data-science/open-reproducible-science/bash/bash-commands-to-manage-directories-files/) when working with Git are `ls` to list files, and `cd` to navigate through folders.

All **Git commands** have the following syntax: `git verb options`.
- The most commonly used verbs are: `status`, `add`, `commit`, `pull`, `push`. Other useful ones include: `branch`, `checkout`, `reset`, `diff`, `merge`, `log`. All these commands are explained below and summarized [here](https://gist.github.com/davfre/8313299). You can also see [1](https://www.frankpinter.com/notes/git-for-economists-presentation.pdf), [2](https://www.sas.upenn.edu/~jesusfv/Chapter_HPC_5_Git.pdf), [3](https://rubygarage.org/blog/most-basic-git-commands-with-examples), [4](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow) and [5](https://nvie.com/posts/a-successful-git-branching-model/).
- Git commands only work when (in the terminal) you are in a directory or folder that contains a Git project, otherwise the terminal will display the message "Not a git repository".

A few key concepts to start:
- A **repository** or **repo** is a project (i.e. folder with files). To **commit** is to register the changes made in a file or files. A **Git repository** is a history of commits and how they relate. Git tracks changes in files line by line and stores data as a series of snapshots.
- A **branch** is a specific sequence of commits. An **upstream** is an online- or *remote*-tracking branch, associated with a regular branch (in your *local* machine). To **pull** is to download the changes from the remote repository to your local machine. To **push** is to upload the changes from your local machine to the remote repository.


## Contents
1. [Setting Up Git](#setting-up)
	1. [Accessing GitHub from Git](#access-github)
3. [Create a Repository](#repositories)
5. [Regular Git Workflow: Tracking Changes](#regular-workflow)
	1. [Commit Changes](#git-commit)
	1. [See Changes Before Committing](#git-diff)
	1. [See History of Commits](#git-log)
	1. [Discard Unwanted Changes](#git-restore)
	1. [Uncommit Changes](#git-reset)
7. [Refined Git Workflow: Branching and Merging](#refined-workflow)
	1. [Create a Branch](#git-branch)
	1. [Import Changes from the Remote Repo](#git-pull)
	1. [Upload Changes to the Remote Repo](#git-push)
	2. [Temporarily Save Changes](#git-stash)
	3. [Merge and Delete Branches](#delete)
9. [A Branching Model for Research](#branching-model)
	1. [Naming Conventions](#naming)
	1. [Implementation of the Model](#implementation)
1. [Annotations](#annotations)

## Setting Up Git <a name="setting-up"></a>
Compare your current version of Git with the [latest release](https://git-scm.com/downloads) by typing:
```bash
$ git --version
```
Verify that you have 1.7.10 or newer. If you don’t, update Git.

Git uses a username to associate commits with an identity. Set your Git username for every repository on your computer as follows:
```bash
$ git config --global user.name "Your Name"
```
- Your Git username is not the same as your GitHub username. 

Set your commit email address in Git:
```bash
$ git config --global user.email "Your Email"
```

To visualize changes more easily in the terminal,  tell Git to colorize its output:
```bash
$ git config --global color.ui "auto"
```

Other optional but useful Git configuartions are (see [1](https://spin.atomicobject.com/2020/05/05/git-configurations-default/) and [2](https://stackoverflow.com/questions/24114676/git-error-failed-to-push-some-refs-to-remote)):
```bash
$ git config --global init.defaultBranch main	# Rename the default branch name to main
$ git config --global fetch.prune true		# Clean removed remote branches
$ git config --global diff.colorMoved zebra	# Color changes when using git diff
$ git config --global rebase.autoStash true
```
<!-- $ git config --global pull.rebase true		# You replay your local commits on top of the newly updated origin/main -->

To check the configuration settings, type `git config --list`. For more on setting up Git see [here](https://help.github.com/en/articles/set-up-git).


### Accessing GitHub from Git <a name="access-github"></a>
To access a GitHub repository from Git, you need to [authenticate](https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories) with GitHub using either HTTPS or SSH protocols. If you don't authenticate, when you try to interact (e.g. clone, pull, push) with the remote repository, the terminal will display the following error:
```bash
> Permission denied (publickey)
```

#### HTTPS
If you want to use HTTPS with GitHub, you can use a [credential helper](https://cfss.uchicago.edu/setup/git-cache-credentials/) to tell Git to remember your credentials. You need Git 1.7.10 or newer to use the osxkeychain credential helper.

- Find out if the credential helper is already installed. In the terminal, type:
```bash
$ git credential-osxkeychain
> usage: git credential-osxkeychain <get|store|erase>
```
If you don't see the second line, follow step 2 on the [GitHub help page](https://docs.github.com/en/github/getting-started-with-github/caching-your-github-credentials-in-git#platform-mac).

- Tell Git to use the osxkeychain helper using the global credential.helper config:
```bash
$ git config --global credential.helper osxkeychain
```

- After this, the next time you try to clone, pull, push, etc. from the terminal, it will ask you to authenticate (only once). [Since 2019](https://developer.github.com/changes/2019-11-05-deprecated-passwords-and-authorizations-api/), authetication in GitHub uses a [personal access token](https://github.com/settings/tokens) (PAT) instead of a password. So, you need to [create](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) a PAT for the command line. Once you have a token, enter it instead of your password to perform Git operations over HTTPS.

[Update your GitHub access credentials](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/updating-your-github-access-credentials): When you change your username, password, or PAT on GitHub, you will need to [update your saved credentials](https://docs.github.com/en/github/using-git/updating-credentials-from-the-macos-keychain) in the the credential helper (`git credential-osxkeychain`) because they may be cached on your computer. To delete your credentials via the command line, type:
```bash
$ git credential-osxkeychain erase
> host=github.com
> protocol=https
> [Press Return]
```
If it's successful, nothing will print out. To test that it works, try using commands like `git clone`, `git fetch`, `git pull` or `git push` with HTTPS URLs. If you are prompted for your GitHub username and PAT, the keychain entry was deleted. For example, on the command line you would enter:
```bash
$ git clone https://github.com/USERNAME/REPOSITORY.git
> Username: your_username
> Password: your_PAT
```

#### SSH
If you want to use SSH with GitHub, follow [these instructions](https://docs.github.com/en/authentication/connecting-to-github-with-ssh).

#### Check the Protocol
The format of the remote URL from which the repository will fetch from and push to indicates whether Git is using HTTP or SSH: it starts with https<area>://github.com for HTTPS and with git@github<area>.com for SSH. To see the URL, type:
```bash
$ git remote -v
# OR
$ git remote show origin
```

#### Switch Protocols
If, after creating a repository, you need to [switch from one protocol to the other](https://docs.github.com/en/get-started/getting-started-with-git/managing-remote-repositories#changing-a-remote-repositorys-url), type:
```bash
$ git remote set-url origin https://github.com/USERNAME/REPOSITORY.git	# Switch from SSH to HTTPS
$ git remote set-url origin git@github.com:USERNAME/REPOSITORY.git	# Switch from HTTPS to SSH
```


## Create a Repository <a name="repositories"></a>
You need to designate a folder to be a Git repository. When you initialize a folder to be a repository, Git creates a subfolder called *.git* that it uses to do all its magic.

You can create a repository in github.com by clicking the plus sign at the top right corner and choosing 'New Repository'. Type a name for the repository (with no spaces) and choose whether you want the repository to be private or public.
It is recommended to include a README file, a license file and a GitHub-hosted *.gitignore* file. The *.gitignore* file includes the file extensions you want Git to ignore; for example, type 'tex' to exclude junk files from LaTeX, and modify it to add more extensions (e.g. autosave extensions for Word, Matlab, Stata, R, Python).

Once you created a repository in GitHub, click on 'Code' and copy the URL link that GitHub creates in order to clone the repository in your machine. You need the appropriate URL depending on how you decided to access GitHub when setting up Git above, either HTTPS or SSH. Use the `cd` command to go to the folder where you want to set the repository and paste the URL as follows:
```bash
$ cd <path/to/folder>
$ git clone <URL>
```
This initializes a folder as a Git repository in your machine. You can now make changes to your project, either in github.com or in your local machine. See below for how to pull down from github.com the most recent version of the project to your machine, and how to upload changes made to files in your local machine to github.com.
- You can also create a local repo from the terminal. Go to the desired folder using `cd` and type `git init`. Then, you need to type `git push -u origin main` in your first push to upstream the local repo to a remote (empty) repo. This creates an upstream branch `main` *and* sets `origin/main` as a remote-tracking branch (i.e. the local branch `main` will be pushed to the upstream branch (origin) `main`).
- Avoid keeping your local repo in the Dropbox folder: there is a chance of conflicts between the syncing of Dropbox and GitHub, and the space limit in Dropbox might be an issue when the project's size grows.
- Git does not include empty folders in updates to remote repositories in GitHub. To make Git to recognize such folders, the non-official convention is to put a *.gitkeep* file in them. For this, move to the empty folder with `cd` and type `touch .gitkeep`.
- Regarding the [*.gitignore* file](https://help.github.com/en/articles/ignoring-files):
	- Make sure to create it before including files with unwanted extensions in your local repository. If you include a file with an unwanted extension before creating the *.gitignore* file, you will need to untrack the file with `git rm --cached <filename.ext>`.
	- You may want to include .xls* files because of size limits. GitHub blocks files larger than 100 MB and sends warnings for files over 50 MB. In fact, very large files do not work well in version control because they are often duplicated in the history.
	- You can place the file within any folder in the Git repository except in the *.git* folder itself, in which case the file won't work. However, if you need to have a private version of the *.gitignore* file, you can add the rules to the *.git/info/exclude* file.
- If you want to relocate a local repo in your machine, [simply move](https://stackoverflow.com/questions/11384928/change-git-repository-directory-location) the whole repo (including the hidden `.git` directory).
- If at some point you want to [rename a repository](https://help.github.com/en/articles/renaming-a-repository), you need to change the name in github.com (under the settings of a project) and in your local machine. Use `git remote -v` to get the remote URL and `git remote set-url origin <new-URL>` to set the new URL.

<!---
You can create a local repo from the terminal with `git init` or from github.com. With the first option, you will later need to call that local repository from GitHub. With the second option, you will later need to clone the remote repository into your local machine.
or under the 'Repositories' tab select 'New'.
include a license file (or at least explicitly claim copyright by including: Copyright [yyyy] [name of copyright owner]).
very large files (> 100 MB).
-->


## Regular Git Workflow: Tracking Changes <a name="regular-workflow"></a>
When you modify files in a project, you don't want to keep a record of every little change you do. You want to make changes, go back and forth, and once you are happy with the new version (i.e. no mistakes in code, no compilation errors, consistent output), you register (or commit) the changes.

Git works by saving changes, not entire files. To track a timeline of edits, Git uses three internal state management systems, known as *trees*.
- The **working directory** tracks the immediate changes to the content of files and directories.
- The **index** is the staging area for Git to keep track of the changes in the working directory ready to be committed.
- The **commit history** is a permanent record of the changes. In this tree, HEAD is the name of the *last* commit in the current branch.

### Commit Changes <a name="git-commit"></a>
It is recommended to commit changes per discrete task (which may involve multiple files). However, you may need to modify more files than the ones involved in a particular task, in which case you want to choose which of the modified files have to do with a particular task, commit only the changes to those files and leave for a later commit the changes to the other modified files unrelated to the particular task. Staging is what allows you to commit changes per task; all the files in the staging area are committed. In summary, you first add (new or modified) files to the staging area and then commit their changes. Git refers to new files as 'untracked' and to modified files as 'unstaged'.

The workflow just described can be implemented with the `status`, `add`, `reset` and `commit` commands as follows:
```bash
$ git status 					# Show pending changes to the trees in your local machine

$ git add <filename1.ext> ... <filenameN.ext>	# Add modified files to the staging area
$ git add .					# Add all modified files to the staging area
$ git reset <filename.ext>			# Unstage a file without changing the working directory (i.e. a safe command)
$ git reset					# Unstage all files so that you can re-build the staging area

$ git commit -m "Brief meaningful comment"	# Lock in the changes to the files in the staging area

$ git push					# Sync up the changes made locally with the remote repository
```
- When your local version is not ahead of the remote (in GitHub), `git status` will display `Your branch is up to date with 'origin/<main>'. nothing to commit, working tree clean`. However, this does not tell you whether the remote version is ahead of your local version. For this reason, **always pull before you push**.
- If there are many unstaged and untracked files and you want to include them all to the staging area except one (or a few more), it is easiest to add them all and then remove the unwanted ones with `git add .` and `git reset <filename.ext>`.
- Always run tests and review changes *before* committing. Only commit working versions of code.
- Make 'small' frequent commits rather than big infrequent commits.
- Commit messages should be short (< 72 characters). For good practices on writing good commit messages, see [1](https://gist.github.com/robertpainsi/b632364184e70900af4ab688decf6f53), [2](https://chris.beams.io/posts/git-commit/) and [3](https://www.freecodecamp.org/news/writing-good-commit-messages-a-practical-guide/).
- If you opened up a string with an odd number of `'` characters when making a commit, you can exit the prompt `>` and go back to the normal bash prompt, by typing `'` again and hit `Return` to close the string. [Alternatively](https://stackoverflow.com/questions/26228848/how-do-i-exit-my-git-commit-message-im-not-in-the-vim-i-used-the-commit-m), you can press `ctrl` + `c`.
- If you run `git commit` without a message or option, your default text editor will open up in the screen of your terminal and you can write a multi-line message. **In a Mac**, if the Vi editor opens in command mode, press `i` (to switch to insert or edit mode) and start writing your comment. When you finish writing your message, sequentially press `Esc` + `:wq` + `Enter`, where `Esc` exits the insert mode, `:` enters the command mode, `w` writes and saves the commit message, `q` exits the editor (see [here](https://apple.stackexchange.com/questions/252541/how-do-i-escape-the-git-commit-window-from-os-x-terminal)). To change your default editor (e.g. to vim, nano, emacs), type `git config --global core.editor nano`. The difference between vim and nano is that vim is modal and nano is not. So with vim you are constantly changing between command mode and edit mode, whereas in nano, as in emacs, you are in one mode and your commands use special key combinations.

### See Changes Before Committing <a name="git-diff"></a>
If you don't remember what changes were made to each file, you can see them using three different ways (illustrated [here](https://stackoverflow.com/questions/1587846/how-do-i-show-the-changes-which-have-been-staged)):
```bash
$ git diff <filename.ext>		# Show unstaged changes (i.e. changes between the working directory and the index)

$ git diff --staged <filename.ext>	# Show what you're about to commit (i.e. changes between the index and the HEAD)

$ git diff HEAD <filename.ext>		# Show changes since the last commit (i.e. changes between the working directory and the HEAD)
```
- It'll work recursively on directories. If no path or file are given, `git diff` shows the changes in all files.

### See History of Commits <a name="git-log"></a>
See a list of previous commits:
```bash
$ git log				# Show sequence of local commits in current branch
$ git log HEAD..origin/<brachname>	# Show sequence of remote commits in current branch
$ git reflog				# Show sequence of actions in the repository
```

### Discard Unwanted Changes <a name="git-restore"></a>
You can discard *unwanted* changes to a file permanently before they are staged (**Warning**: It erases any unsaved work!):
```bash
$ git restore <filename.ext>		# For a specific file
$ git restore .				# For all unstaged files in the working directory
```

### Uncommit Changes <a name="git-reset"></a>
You can rewrite history and to throw out commits that you no longer want (e.g. incomplete or wrongly committed files):
```bash
$ git reset --soft HEAD^			# Undo last commit but put changes back in staging area (keep changes)
$ git reset --hard HEAD^			# Undo last commit and all changes
$ git checkout COMMIT_ID -- <file1>, <file2>	# Reverse changes from a previous commit
```
- If you also did a `git push`, see [here](https://stackoverflow.com/questions/12481639/remove-files-from-git-commit).

<!---
- `--cached` does not affect the working directory because it works directly in the index.
- `--` tells Git that what follows after the two dashes are filenames.
```bash
$ git stash		# Discard all local changes, but save them for possible re-use later
```
-->


## Refined Git Workflow: Branching and Merging <a name="refined-workflow"></a>
**Branches** are the most powerful part of Git. They allow to trying things out. By isolating features into separate branches, everybody can work independently and, when necessary, share the changes with other developers. Git encourages workflows that branch and merge often, even multiple times in a day.

Any new repository is created with a default branch called `main`, additional branches need to be created. All branches in the remote repo will hang from the remote URL *origin*, that is why it is important to have a branching model to distinguish between permanent and temporary branches. A branching model describes a structure, naming conventions, rules for branching off and merging to other branches. The next section describes a branching model for a research project.

### Create a Branch <a name="git-branch"></a>
When you create a new branch **off** the *current* branch, you can make changes to files while you are in the the new branch without affecting the version of the files that you see when you move to the original or parent branch. You can implement this process with the `branch` and `checkout` commands as follows:
```bash
$ git branch <branchname>		# Create a branch called <branchname>
$ git checkout <branchname>		# Switch to branch <branchname>
# OR
$ git checkout -b <branchname>		# Create branch <branchname> and switch to it

$ git push -u origin <branchname>	# Set upstream for branch <branchname> (for push and pull)
> Branch '<branchname>' set up to track remote branch '<branchname>' from 'origin'
# OR
$ git checkout -t origin/<branchname>	# Create <branchname>, switch to it and track (for push and pull) its remote branch
```
- Once in the new branch, you can modify files, add and commit as many edits as necessary with `git add` and `git commit`.
- Always commit *and* close the modified files *before* switching branches (with `git checkout`) because when you switch, Git will update the files in the repository to match the version to which you are moving to.
- See [here](http://genomewiki.ucsc.edu/index.php/Working_with_branches_in_Git) for more on how to work with branches in Git.
- See [here](https://stackoverflow.com/questions/10002239/difference-between-git-checkout-track-origin-branch-and-git-checkout-b-branch) for an explanation of the difference between `git checkout -b` and `git checkout -t` for tracking a remote branch.

To see the available branches:
```bash
$ git branch			# List all local branches
$ git branch -r			# List all remote branches
$ git branch -a			# List all local and remote branches
```   
- An asterisk before a branch name tells you the branch in which you are currently working on.
- If you configured Git to display its output in color, the current local branch will be displayed in green, the local non-current branches will be displayed in white, and the remote branches will be displayed in red.

### Import Changes from the Remote Repo <a name="git-pull"></a>
If you create a local branch `<branchname>` off a remote branch `<parent>` that is regularly updated by other user(s), the remote repo will be frequently ahead of your local repo. In those cases, you should [merge the changes in `<parent>` into `<branchname>`](https://stackoverflow.com/questions/6836461/updating-the-current-branch-from-parent-branch) to import the latest updates and to minimize the chances of conflicts when you eventually merge your local branch `<branchname>` back into the remote branch `<parent>`. The frequency to do this depends on the number of users pushing their changes to `<parent>`. Each time, first pull down the latest version of the `<parent>` branch, and then merge the changes into your local branch `<branchname>`.
```bash
$ git checkout <parent>		# Switch to the <parent> branch
$ git pull			# Sync up by downloading the latest version of the remote branch
$ git checkout <branchname>	# Switch to the <branchname> branch
$ git merge <parent>		# Incorporate the changes in <parent> into <branchname>
```
- The merge works as long as there are no conflicts. If there are conflicts, they will be indicated in the respective file with `<<<` as delimiters. Your changes are marked as HEAD. Manually resolve any conflicts and delete all the delimiters. Add the file back (`git add .`) and finish the merge (`git merge --continue`). To abort the merge use: `git merge --abort`. For more on how to resolve merge conflicts, see [this](https://www.simplilearn.com/tutorials/git-tutorial/merge-conflicts-in-git).
- Always **commit before** pulling (or pushing) because if there are conflicts, Git reconstructs using the commits.
- Always **pull before** you push so that the remote and the local repos are in sync.
- If you want to merge the changes in `<branchname>` into the `<parent>`: `git checkout <parent>`, `git merge <branchname>`.

### Upload Changes to the Remote Repo <a name="git-push"></a>
To upload the commits you made in the local branch `<branchname>` to the remote branch `<branchname>`:
```bash
$ git checkout <branchname>	# In case you are not already in branch <branchname>, switch to it
$ git push	   		# Upload the current branch (<branchname>) to the associated upstream
```
- For `git push` to work, you must have associated an upstream for `<branchname>` already (see `git push -u` or `git checkout -t` [above](#git-branch)).
- A longer syntax for the `push` command is `git push <to> <from>`, which would translate into `git push origin <branchname>`. The short version is just `git push`, assuming the local and remote branches have the same name. Since Git 1.7.11, the default push policy is `simple`: push only the current branch, and only if it has a similarly named remote-tracking branch on the upstream. See [1](https://stackoverflow.com/questions/17096311/why-do-i-need-to-explicitly-push-a-new-branch/17096880#17096880) and [2](https://stackoverflow.com/questions/37770467/why-do-i-have-to-git-push-set-upstream-origin-branch) for an explanation.
- Note that you need to switch to `<branchname>` before pushing. If you are on `<parent>` and type (1) `git push`, Git will push the `<parent>` not `<branchname>`, or (2) `git push origin <branchname>`, Git will try to push the local `<parent>` branch (being the *current* branch) to the remote `<branchname>`, which would be incorrect. If you are in `<parent>` and you don't want to checkout to `<branchname>`, you can use: `git push origin <branchname>:<branchname>`.
- The `git push` command pushes [just the current branch](https://stackoverflow.com/questions/820178/how-do-you-push-just-a-single-git-branch-and-no-other-branches), not other branches nor the `<parent>` branch. If, for every branch that exists on the local side, you want the remote side to be updated (as long as a branch of the same name already exists on the remote side) use: `git push origin :` or `git push origin +:` (for non-fast-forward updates).
- When collaborating with other people, you should create a **pull request** *before* merging your changes to a remote <parent> branch to allow other people to peer review the changes. This process starts a back and forth conversation about the changes. You can resolve conflicts if others have made changes to the repo, and make new commits and more merges to an existing pull request depending on the feedback received. When your changes are conflict-free and approved by someone with privileges, your branch is merged to the `<parent>` branch and everybody's branches can inherit those changes. It is usually a bad idea to merge your own pull requests when working in a team.

**Pushing to the same branch**. If you need to work on the [same branch](https://stackoverflow.com/questions/913012/committing-to-the-same-branch-with-git) from different machines (e.g. office and home), *in each machine* create a branch with the *same name* off the *same parent* and set the *same upstream*. You just need to account for the blackout after the first branch is created. That is, in the first machine, you create the branch off the parent and push changes to the remote. In the other machines, you need to create the branch off the parent but the target upstream will be ahead by the commits you push from the first machine, so you need to [set the upstream considering that the branch is behind the remote](https://stackoverflow.com/questions/520650/make-an-existing-git-branch-track-a-remote-branch).
```bash
# In the first machine:
$ git checkout -b <branchname> <parent>	# Create branch <branchname> off the <parent> branch and switch to it
$ git push -u origin <branchname>	# Set upstream for branch <branchname>
# Edit, test, commit, push in <branchname>

# In other machines:
$ git checkout -b <branchname> <parent>	# Create branch <branchname> off the <parent> branch and switch to it
$ git branch -u origin/<branchname>	# Set the behind-the-remote branch <branchname> to track origin/<branchname>
```
- When you finish working in one machine, always commit and push the changes. When you start working on another machine, always pull first.
- The blackout originates when you push changes to the remote from the first machine *before* creating the branch in the other machines.

### Temporarily Save Changes <a name="git-stash"></a>
If you are working in branch B and suddenly realize that you need to make minor changes in branch A, you can [commit your likely unfinished changes](https://stackoverflow.com/questions/22053757/checkout-another-branch-when-there-are-uncommitted-changes-on-the-current-branch) in branch B and switch to work on branch A. [Alternatively](http://genomewiki.ucsc.edu/index.php/Working_with_branches_in_Git#What_do_I_do_if_I'm_in_the_middle_of_a_project_on_another_branch_and_I_need_to_make_some_minor_change_to_the_main_branch_(master)?), you can temporarily save the changes in branch B, make the required edits in branch A and then restore the interrupted changes in branch B.
```bash
# Uncommitted changes in <branchB>
$ git stash			# Save your changes in a temporary branch

$ git checkout <branchA>	# Move to <branchA>
# Edit, test, commit, push in <branchA>

$ git checkout <branchB>	# Return to <branchB>
$ git stash pop			# Restore the interrupted changes in <branchB>
# Continue working on <branchB>
```
- `git stash` can also be used when your local changes conflict with the changes in the upstream (in which case, `git pull` will not merge): `git stash`, `git pull`, `git stash pop`.

### Merge and Delete Branches <a name="delete"></a>
After the changes for which the temporary branch `<branchname>` was created have been made, you want to incorporate (or merge) those changes into its upstream branch. Once the merge is done, `<branchname>` can be safely [deleted locally and remotely](https://stackoverflow.com/questions/2003505/how-do-i-delete-a-git-branch-locally-and-remotely).
```bash
$ git checkout <parent>				# Switch to the branch that will import the changes
$ git pull					# Pull before push to have the latest version of the <parent> branch
$ git merge --no-ff <branchname>		# Merge changes in <branchname> to <parent> without a fast-forward
$ git branch -d <branchname>			# Delete the local branch  (-d means "safe delete" e.g. only if already merged)
$ git push -d origin <branch_name>		# Delete the remote-tracking branch
```
- `git merge --no-ff` enforces the creation of an explicit merge commit, which gives you context when navigating the history. [Here](https://stackoverflow.com/questions/21817248/whats-the-difference-between-git-rebase-and-merge-no-ff) is an explanation of the difference between `git merge --no-ff` and `git rebase`.
- To delete a branch in GitHub, click on the 'branches' tab at the top of the project's contents and click the trash button nex to the name of the branch, but beware that it will still show up with `git branch -a`.

After deleting the local and remote branches, other machines may still have "obsolete tracking branches". To propagate the removal of remote branches in those other machines, they need to remove all such [stale](https://makandracards.com/makandra/6739-git-remove-information-on-branches-that-were-deleted-on-origin) branches locally:
```bash
$ git fetch --all --prune
```

<!---
In case you needed, you can rename an existing repository default branch from `master` to `main`, see [here](https://jarv.is/notes/github-rename-master/) and [here](https://www.git-tower.com/learn/git/faq/git-rename-master-to-main/). All references below assume that the default branch is `main`.

$ git checkout -b <branchname> origin/<branchname> # Same as previous but local and tracking branches can have different names
Only delete temporary branches (`ftr` and `fix`), not permanent branches (`dev`).

To delete a **remote** branch from the terminal ([link](https://stackoverflow.com/questions/2003505/how-do-i-delete-a-git-branch-both-locally-and-remotely), [link](https://stackoverflow.com/questions/5094293/git-remote-branch-deleted-but-still-appears-in-branch-a)):
$ git branch -d -r origin/<branch_name>		# Remove a particular remote-tracking branch
$ git push origin --delete <branch_name>	# Delete the remote-tracking branch

$ git remote prune origin
$ git fetch --prune
$ git fetch -p				# Prune remote branches
$ git pull -p

(`main`, `development`) (`feature`, `fixes`) 
-->


## A Branching Model for Research <a name="branching-model"></a>
This section adapts the following [git-flow branching model](https://nvie.com/posts/a-successful-git-branching-model/) of Vincent Driessen to a research project.
```bash
# Create branch
$ git checkout -b <branchname> <parent>	# Create branch <branchname> off the <parent> branch and switch to it
$ git push -u origin <branchname>	# Set upstream for branch <branchname> (for push and pull)

# Edit, test, commit, push in <branchname>

# Merge branch
$ git checkout <parent>
$ git pull				# Pull before push to have the latest version of the remote <parent> branch
$ git merge --no-ff <branchname>	# Merge changes in <branchname> into <parent> without a fast-forward
$ git push origin <parent>		# Push changes to the server

# Delete branch
$ git branch -d <branchname>		# Delete (fully merged) local branch
$ git push -d origin <branchname>	# Delete remote branch
```

### Naming Conventions <a name="naming"></a>
The model uses permanent and temporary branches. The permanent branches are `main` and `dev`.
- The `main` branch is the default branch for any new repository.
- The development or `dev` branch needs to be created, it branches off from `main` and merges back into `main`.

Temporary branches have two *categories*:
- Amend or `fix`, which branch off from `main` or `dev` and merge back into `main` or `dev`. Their *types* are thus `mai` and `dev`.
- Feature or `ftr`, which branch off from `dev` and merge back into `dev`. They are the most frequently used and can be of three *types*:
	- `data` branches deal with raw or analytic data, so this token will be followed by `raw` and `ans`.
	- `code` branches deal with tidying or analyzing the data, so this token will be followed by `tdy` and `ans`.
	- `docs` branches deal with issues in the paper, slides or both (e.g. figures), so this token will be followed by `ppr`, `sld` and `mix`.

Temporary branch names have the following structure: **category/type/task**.
- *Task* names should have no spaces, they should be brief and meaningful, and never be omitted. These branch names are thus **not** valid: `code/ans` (does not include a task name), `fix/mai/1` (not meaningful), `docs/mix/figure 5` (there's a space).
- **Examples** of valid names: `data/raw/import-csv`, `code/ans/add-comments`, `docs/ppr/edit-section3`, `fix/dev/date-format`.
- This structure for temporary branch names follows [useful naming conventions](https://stackoverflow.com/questions/273695/what-are-some-examples-of-commonly-used-practices-for-naming-git-branches) that facilitate the workflow.
- [This repository template](https://github.com/pavelsolis/Replication-Folder) (a folder structure that facilitates the reproducibility of research results) aligns with these naming conventions.


### Implementation of the Model <a name="implementation"></a>
Implementation of the git-flow branching model with the naming conventions for a research project:
```bash
	# Development branch
# Create dev branch
$ git checkout -b dev main		# Create dev branch off main branch and switch to it
$ git push -u origin dev		# Set upstream for dev branch

# Edit, test, commit, push in dev

# Merge dev branch into main (close all open files from dev branch first)
$ git checkout main
$ git pull				# Pull before push to have the latest version of remote dev branch
$ git merge --no-ff dev			# Merge changes in dev into main without a fast-forward
$ git push origin main			# Push changes to the server

# Delete dev branch
$ git branch -d dev			# Delete (fully merged) local dev branch
$ git push -d origin dev		# Delete remote dev branch


	# Temporary branches
# Create temporary branch
$ git checkout -b cat/typ/task dev	# Create temporary branch off dev branch and switch to it
$ git push -u origin cat/typ/task	# Set upstream for the temporary branch

# Edit, test, commit, push in temporary branch

# Merge temporary branch into dev (close all open files from temporary branch first)
$ git checkout dev
$ git pull				# Pull before push to have the latest version of the remote temporary branch
$ git merge --no-ff cat/typ/task	# Merge changes in temporary into dev without a fast-forward
$ git pull --no-rebase			# Optional: it might be needed when working in teams to ensure no conflicts
$ git push origin dev			# Push changes to the server

# Delete temporary branch
$ git branch -d cat/typ/task		# Delete (fully merged) local temporary branch
$ git push -d origin cat/typ/task	# Delete remote temporary branch
```
- For temporary `fix` branches which branch off from `main`, substitute `dev` for `main` above.

<!---
$ git push origin --delete <branchname>	# Delete remote branch
$ git push origin --delete dev		# Delete remote branch
$ git push origin --delete cat/typ/task	# Delete remote branch
[Implementation](https://stackoverflow.com/questions/4470523/create-a-branch-in-git-from-another-branch)
Use meaningful branch names.
Notice that a forward slash separates the tokens.
take any of three tokens
- All three of the different types of feature branches can be used for experimenting or testing minor things unrelated to the previous categories, in which case any of the three types will be followed by: `tst`.
because `dev` is a permanent branch and `fix` branches are aimed to correct bugs in `dev` or `main` (e.g. due to a recent merge from the `dev` branch). 
In sum, there are in total 13 possible types of temporary branches: 11 feautre branches and 2 fix branches.
That is, their names need to include the `/feature-name` part (see first link above).
`docs` branches deal with issues regarding the paper, slides, equations, figures, tables, references or settings so this token will be followed by `ppr`, `sld`, `eqn`, `fig`, `tbl`, `ref` and `set`.

# Fix branches
	# Create the branch
$ git checkout -b fix/dev/name dev	OR	$ git checkout -b fix/mst/name main
$ git push -u origin fix/dev/name	OR	$ git push -u origin fix/mst/name
	# Usual workflow
$ git status
$ git commit -am "Your message"
				# fix/mst branches
	# To merge fix branch into main
$ git checkout main
$ git merge --no-ff fix/mst/name	# Merge your changes to main without a fast-forward
$ git push origin main		# Push changes to the server
$ git push origin fix/mst/name
	# To merge fix branch into dev
$ git checkout dev
$ git merge --no-ff fix/mst/name	# Merge your changes to dev without a fast-forward
$ git push origin dev			# Push changes to the server
				# fix/dev branches
	# To merge fix branch into dev
$ git checkout dev
$ git merge --no-ff fix/dev/name	# Merge your changes to dev without a fast-forward
$ git push origin dev			# Push changes to the server
$ git push origin fix/dev/name
	# To delete the fix branch
$ git branch -d fix/xxx/name		# Optional: remove local and remote branches
$ git push origin --delete fix/xxx/name
-->


## Annotations <a name="annotations"></a>
- A **ref** is anything pointing to a commit (e.g. heads (branches), tags, remote branches), they are stored in the .git/refs directory (e.g. `refs/heads/main`, `refs/remotes/main`, `refs/tags`). For example, `refs/heads/0.58` specifies a branch named `0.58`. If you don't specify what namespace the ref is in, Git will look in the default ones, so using only `0.58` is ambiguous (there could be both a `branch` and a `tag` named `0.58`).
- When an update changes a branch (or more generally, a ref) that used to point at commit A to point at another commit B, it is called a **fast-forward update** if and only if B is a descendant of A. Hence a fast-forward update from A to B does not lose any history.
- GitHub blocks files larger than 100 MB and sends warnings in the terminal for files over 50 MB.
	- If you need to work with large files, see [here](https://docs.github.com/en/repositories/working-with-files/managing-large-files).
	- You can remove a big file [wrongly committed](https://thomas-cokelaer.info/blog/2018/02/git-how-to-remove-a-big-file-wrongly-committed/), but it may cause the local and remote branches to diverge (see [1](https://stackoverflow.com/questions/2452226/master-branch-and-origin-master-have-diverged-how-to-undiverge-branches) and [2](http://serebrov.github.io/html/2012-02-13-git-branches-have-diverged.html)).
```bash
$ git filter-branch --tree-filter 'rm -rf path/to/filename.ext' HEAD
$ git push

$ git rebase origin/<branchname>	# If local and remote branches diverge
```
- `git pull` equals either `git fetch` + `git merge` (default) or `git fetch` + `git rebase`, depending on configuration options or command line flags. Thus, the opposite of `git push` is `git fetch` not `git pull`. `git merge` and `git rebase` reconcile diverging branches, the choice between them depends on how you want the commit history to look like (see [1](https://stackoverflow.com/questions/21817248/whats-the-difference-between-git-rebase-and-merge-no-ff) and [2](https://stackoverflow.com/questions/28140434/is-there-a-difference-between-git-rebase-and-git-merge-ff-only)).
- To recover from an incomplete rebase operation and detached HEAD, see [this](https://stackoverflow.com/questions/5772192/how-can-i-reconcile-detached-head-with-master-origin).
- If `git push` displays the following (due to a git-lfs hook):
```bash
> This repository is configured for Git LFS but 'git-lfs' was not found on your path.
If you no longer wish to use Git LFS, remove this hook by deleting .git/hooks/pre-push.

> error: failed to push some refs to 'github.com:USERNAME/REPOSITORY.git'
```
You may want to [try to remove](https://github.com/git-lfs/git-lfs/issues/333) the folder `.git/lfs` and the file `.git/hooks/pre-push`.

<!---
- To understand GitHub from scratch: Healey (intuitively explains Git workflow); Youtube videos by Learn Code show the basic workflow; Pinter (2019) explains benefits and gives recommendations; Sviatoslav (2017); Notes by Fernández-Villaverde complement/reinforce the previous one; StackExchange links for clarification, reinforcement and understanding the daily workflow.
GitHub recomends a limit of 1 GB per project.
- Only push into [bare repositories](https://stackoverflow.com/questions/1298499/git-push-not-send-changes-to-remote-git-repository).
- [git rebase after previous git merge](https://stackoverflow.com/questions/6248231/git-rebase-after-previous-git-merge).
-->
