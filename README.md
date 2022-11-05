# Git and GitHub Primer

**Git** is a version control software. **GitHub** is a hosting service to track changes. With them, you can keep track of the changes in a project and have a backup online.

A few basic concepts to start:
- A **repository** is a project (i.e. folder with files). Git tracks changes in files line by line and stores data as a series of snapshots. To **commit** is to register the changes made in a file or files. A **Git repository** is a history of commits and how they relate.
- A **branch** is a specific sequence of commits. An **upstream** is an online- or *remote*-tracking branch, associated with a regular branch (in your *local* machine). To **pull** is to download the changes from the remote repository to your local machine. To **push** is to upload the changes from your local machine to the remote repository.

Below are step-by-step instructions on how to work with Git and GitHub from scratch using the terminal (or shell).
- A line starting with '$' refers to commands typed in the terminal, and a line starting with '>' represents output shown in the terminal.
- The most commonly used commands in the terminal when working with Git are: `ls` to list files, and `cd` to navigate through folders.

All Git commands have the following syntax: `git verb options`.
- The most commonly used verbs are: `status`, `add`, `commit`, `pull`, `push`. Other useful ones include: `branch`, `checkout`, `reset`, `diff`, `merge`, `log`. All these commands are explained below, but you can see a useful Git cheat-sheet [here](https://gist.github.com/davfre/8313299).
- Git commands only work when (in the terminal) you are in a folder that contains a Git repository, otherwise the terminal will display the message "Not a git repository".

Notes: See [here](https://www.earthdatascience.org/courses/intro-to-earth-data-science/open-reproducible-science/bash/) for an overview of the terminal, shell, and bash; [here](https://www.earthdatascience.org/courses/intro-to-earth-data-science/open-reproducible-science/bash/bash-commands-to-manage-directories-files/) for the main bash commands to manage directories and files, and [1](https://www.frankpinter.com/notes/git-for-economists-presentation.pdf), [2](https://www.sas.upenn.edu/~jesusfv/Chapter_HPC_5_Git.pdf), [3](https://rubygarage.org/blog/most-basic-git-commands-with-examples), [4](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow), [5](https://nvie.com/posts/a-successful-git-branching-model/) for other great introductory pieces to Git and GitHub.


## Setting Up Git
Compare your current version of Git with the [latest release](https://git-scm.com/downloads) by typing:
```bash
$ git --version
```
Verify that you have 1.7.10 or newer. If you don’t, update Git.

Git uses a username to associate commits with an identity. Set your Git username for every repository on your computer as follows:
```bash
$ git config --global user.name "Your Name"
```
Note: Your Git username is not the same as your GitHub username. 

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


### Accesing Remote Repositories
To access a GitHub repository from Git, you need to [authenticate](https://docs.github.com/en/get-started/getting-started-with-git/about-remote-repositories) with GitHub using either HTTPS or SSH protocols. If you don't authenticate, when you try to clone, pull, push, etc. from or to the remote repository, the terminal will display the following error:
```bash
> Permission denied (publickey)
```

#### HTTPS
If you decide to use HTTPS with GitHub, you can use a [credential helper](https://cfss.uchicago.edu/setup/git-cache-credentials/) to tell Git to remember your credentials. You need Git 1.7.10 or newer to use the osxkeychain credential helper.

- Find out if the credential helper is already installed. In the terminal, type:
```bash
$ git credential-osxkeychain
```
You should see something like this:
```bash
> usage: git credential-osxkeychain <get|store|erase>
```
Otherwise, follow step 2 on the [GitHub help page](https://docs.github.com/en/github/getting-started-with-github/caching-your-github-credentials-in-git#platform-mac).

- Tell Git to use the osxkeychain helper using the global credential.helper config:
```bash
$ git config --global credential.helper osxkeychain
```

- After this, the next time you try to clone, pull, push, etc. using the terminal, it will ask you to authenticate (only once). [Since 2019](https://developer.github.com/changes/2019-11-05-deprecated-passwords-and-authorizations-api/), authetication in GitHub uses a personal access token or [PAT](https://github.com/settings/tokens) instead of a password. So, you need to [create](https://help.github.com/en/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) a PAT for the command line. Once you have a token, enter it instead of your password to perform Git operations over HTTPS.

You can [update your GitHub access credentials](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/updating-your-github-access-credentials). When you change your username, password, or personal access token on GitHub, you will need to [update your saved credentials](https://docs.github.com/en/github/using-git/updating-credentials-from-the-macos-keychain) in the the credential helper (`git credential-osxkeychain`) because they may be cached on your computer. To delete your credentials via the command line, type:
```bash
$ git credential-osxkeychain erase
host=github.com
protocol=https
> [Press Return]
```
If it's successful, nothing will print out. To test that it works, try using commands like `git clone`, `git fetch`, `git pull` or `git push` with HTTPS URLs. If you are prompted for your GitHub username and PAT, the keychain entry was deleted. For example, on the command line you would enter:
```bash
$ git clone https://github.com/USERNAME/REPOSITORY-NAME.git
Username: your_username
Password: your_token
```

If, after creating a repository, you need to [switch from HTTPS to SSH](https://docs.github.com/en/github/getting-started-with-github/managing-remote-repositories#switching-remote-urls-from-https-to-ssh), type:
```bash
$ git remote set-url origin git@github.com:USERNAME/REPOSITORY-NAME.git
```

The following command shows the URL that the repository will fetch from and push to, so it indicates whether Git is using HTTP or SSH:
```bash
$ git remote show origin
```
The exact format of the URL depends on the protocol you are using, it starts with https<area>://github.com for HTTPS and with git@github<area>.com for SSH.


## Create (Remote and Local) Repositories
You need to designate a folder to be a Git repository. When you initialize a folder to be a repository, Git creates a subfolder called *.git* that it uses to do all its magic.

You can create a Git repository from the terminal with `git init` or from github.com. With the first option, you will later need to call that local repository from GitHub. With the second option, you will later need to clone the remote repository into your local machine. 

### Creating a Repository in GitHub
In github.com click the plus sign at the top right corner and choose 'New Repository' or under the 'Repositories' tab select 'New'. Choose a name for the repository (with no spaces). Choose whether you want the repository to be private or public, and whether you want to initialize it with a README file (recommended). Also, it is recommended to include a GitHub-hosted *.gitignore* file, which includes the file extensions you want Git to ignore; for example, type 'tex' to exclude junk files from LaTeX. The *.gitignore* file can later be modified to add more extensions (e.g. autosave extensions for Word, Matlab, Stata, R, Python).
- Make sure to create the *.gitignore* file before including files with unwanted extensions in your local repository. If you include a file with an unwanted extension before creating the *.gitignore* file, you will need to untrack the file with `git rm --cached <filename.ext>`.
- You can place the *.gitignore* file within any folder in the Git repository except in the *.git* folder itself, in which case the file won't work. However, if you need to have a private version of the *.gitignore* file, you can add the rules to the *.git/info/exclude* file.
- It might be a good idea to include Excel files (.xls*) in the *.gitignore* file because of size limits. In fact, very large files (> 100 MB) do not work well in version control because they are often duplicated in the history and are not supported by GitHub.

Once you created a repository in GitHub, click on 'Code' and copy the URL link that GitHub creates in order to clone the repository in your machine. You need the appropriate URL depending on how you decided to clone when setting up Git above, either HTTPS or SSH.

Use the `cd` command to go to the folder where you want to set the repository and paste the URL as follows:
```bash
$ cd <path/to/folder>
$ git clone <URL>
```
This initializes a folder as a Git repository in your machine. You can now make changes to your project either in github.com or in your local machine.
- To pull down from github.com the most recent version of the project to your machine, type `git pull`.
- To upload changes made to files in your local machine, commit the changes and push them to the remote. See below.

Notes:
- Git does not include empty folders in updates to remote repositories in GitHub. To make Git to recognize such folders, the non-official convention is to put a *.gitkeep* file in them. For this, move to the empty folder with `cd` and type `touch .gitkeep`.
- If at some point you want to rename a repository, you need to change the name in github.com (under the settings of a project) and in your local machine. Use `git remote -v` to get the remote URL and `git remote set-url origin <new-URL>` to set the new URL.


## Regular Git Workflow: Track Changes
When you modify files in a project, you don't want to keep a record of every little change you do. You want to make changes, go back and forth, and once you are happy with the new version (i.e. no mistakes in code, no compilation errors, consistent output), you register (or commit) the changes.

It is recommended to commit changes per discrete task (which may involve multiple files). However, you may need to modify more files than the ones involved in a particular task, in which case you want to choose which of the modified files have to do with a particular task, commit only the changes to those files and leave for a later commit the changes to the other modified files unrelated to the particular task. Staging is what allows you to commit changes per task; all the files in the staging area are committed. In summary, you first add (new or modified) files to the staging area and then commit their changes. Git differences between new and modified files: new files are 'untracked' and modified files are 'unstaged'.

Git works by saving changes, not entire files. To track a timeline of edits, Git uses three internal state management systems, known as *trees*.
- The **working directory** tracks the immediate changes to the content of files and directories.
- The **index** is the staging area for Git to keep track of the changes in the working directory ready to be committed.
- The **commit history** is a permanent record of the changes. In this tree, HEAD is the name of the *last* commit in the current branch.

The workflow described above can be implemented with the `status`, `add`, `reset` and `commit` commands as follows:
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
- Commit messages should be short (< 72 characters). For good practices on writing good commit messages, see [1](https://gist.github.com/robertpainsi/b632364184e70900af4ab688decf6f53), [2](https://chris.beams.io/posts/git-commit/) and [3](https://www.freecodecamp.org/news/writing-good-commit-messages-a-practical-guide/).
- If you run `git commit` without a message or option, your default text editor will open up in the screen of your terminal and you can write a multi-line message. **In a Mac**, if the Vi editor opens in command mode, press `i` (to switch to insert or edit mode) and start writing your comment. When you finish writing your message, sequentially press `Esc` + `:wq` + `Enter`, where `Esc` exits the insert mode, `:` enters the command mode, `w` writes and saves the commit message, `q` exits the editor (see [here](https://apple.stackexchange.com/questions/252541/how-do-i-escape-the-git-commit-window-from-os-x-terminal)). To change your default editor (e.g. to vim, nano, emacs), type `git config --global core.editor nano`. The difference between vim and nano is that vim is modal and nano is not. So with vim you are constantly changing between command mode and edit mode, whereas in nano, as in emacs, you are in one mode and your commands use special key combinations.

### See Changes Before Committing
If you don't remember what changes were made to each file, you can see them using three different ways (illustrated [here](https://stackoverflow.com/questions/1587846/how-do-i-show-the-changes-which-have-been-staged)):
```bash
$ git diff <filename.ext>		# Show unstaged changes (i.e. changes between the working directory and the index)

$ git diff --staged <filename.ext>	# Show what you're about to commit (i.e. changes between the index and the HEAD)

$ git diff HEAD <filename.ext>		# Show changes since the last commit (i.e. changes between the working directory and the HEAD)
```
- It'll work recursively on directories. If no path or file are given, `git diff` shows the changes in all files.

### Discard Unwanted Changes
You can discard unwanted changes to a file permanently *before* they are staged (**Warning**: It erases any unsaved work!):
```bash
$ git restore <filename.ext>		# For a specific file
$ git restore .				# For all unstaged files in the working directory
```

### Uncommit Changes
To move incomplete or wrongly committed files back to the staging area from the immediate commit (maintaining the changes):
```bash
$ git reset --soft HEAD^
```
If you also did a `git push`, see [this](https://stackoverflow.com/questions/12481639/remove-files-from-git-commit).


<!---
- `--cached` does not affect the working directory because it works directly in the index.
- `--` tells Git that what follows after the two dashes are filenames.
```bash
$ git stash		# Discard all local changes, but save them for possible re-use later
```
-->


## Refined Git Workflow: Branching, Merging, Pull Requests
*Summary*: Make a branch to solve a feature request, code the feature, make commits, get latest version of the default branch, merge branches, push your branch up, make a pull request for other people to peer review the code, resolve conflicts and make more merges to an existing pull request depending on the feedback received. When your changes are approved, your branch is merged to the parent branch and everybody's branches can inherit those changes.

Branches are the most powerful part of Git. They allow to trying things out. By isolating features into separate branches, everybody can work independently, yet it is still possible to share changes with other developers when necessary.

Git encourages workflows that branch and merge often, even multiple times in a day.

All branches in the remote repo will hang from origin, that is why it is important to have a branching model (structure, naming conventions, rules for branching off and merging to) to distinguish between permanent (`main`, `development`) branches and among temporary (`feature`, `fixes`) branches.

*Note*: Since October 2020, any new repository is created with the default branch `main`, instead of `master`. In case you needed, you can rename an existing repository default branch from `master` to `main`, see [here](https://jarv.is/notes/github-rename-master/) and [here](https://www.git-tower.com/learn/git/faq/git-rename-master-to-main/). All references below assume that the default branch is `main`.

**CAUTION**: Always commit *and* close the modified files *before* switching branches (with `git checkout`) because when you switch Git will update the files in the repository to match the version to which you are moving to. If you introduce changes in one branch and suddenly realized to it would be better to swith to a different branch, Git [may or may not](https://stackoverflow.com/questions/22053757/checkout-another-branch-when-there-are-uncommitted-changes-on-the-current-branch) allow you to switch:
- If Git doesn't allow you to switch, you can use `git commit` to the current branch and then switch to a different branch; alternatively, you can save your changes in a temporary branch using `git stash`, make the required changes in another branch and then restore the interrupted changes using `git stash pop`. `git stash` can also be used when your local changes conflict with the changes in the upstream (in which case, `git pull` will not merge): `git stash`, `git pull`, `git stash pop`.

### Daily Workflow (Cont.)
Modify the files in the branch, add and commit to the branch as many edits as necessary with: `git add`, `git commit`.

Once you finish making changes, you want to incorporate the latest version of the `parent` branch in the remote repository into your local repository to ensure there are no conflicts: 
```bash
$ git checkout <parent>
$ git pull
$ git checkout <branchname>
$ git merge <parent>		# merges <parent> **into** the *current* branch (i.e. <branchname>)
```
- Always **commit before** pushing or pulling because if there are conflicts, Git reconstructs using the commits.
- Always **pull before** you push so that the local and the remote repositories are in sync.
- **Consult before** merging when working in a team in order to get feedback on a new feature branch. Create a pull request for this.
- If you want to merge the changes in `<branchname>` into the `<parent>`: `git checkout <parent>`, `git merge <branchname>`.

If there are conflicts, they will be indicated in the respective file (you are HEAD). Manually resolve any conflict. Delete all of the delimiters (`<<<`). Add the file back (`git add --all`) and finish the merge (`git merge --continue`). To abort the merge use: `git merge --abort`.


### Knowing Where You Are and How to Move
To see the available branches:
```bash
$ git branch			# Displays all local branches
$ git branch -r			# Displays all remote branches
$ git branch -a			# Displays all local and remote branches

> * main			# If only the main branch exists
> * main, <branchname>	# If there are two branches
```   
- The asterisk tells you the branch in which you are currently working on. If you configured Git to display its output in color, the current branch will be displayed in green; remote branches will be displayed in red; the rest of the branches (local non-current) will be displayed in white.   

### Download the Changes in the Remote Repository to the Local Repository
In the terminal, switch back to main and sync: 
```bash
$ git checkout main
$ git pull
```

In the terminal, go to the *local* `parent` branch (which initially will be the `main` branch) and make sure you have the most recent version of the *remote* `parent` branch:
```bash
$ git checkout <parent>		# Update the files to work on `parent`
$ git pull
```

### Create a Branch
Create a new branch **off** the *current* branch and go to the new branch:
```bash
$ git branch <branchname>		# Creates a branch called <branchname>
# OR
$ git branch --set-upstream-to=origin/<branch> <branch>	# To set tracking information for the branch
					# Message displayed: "Branch '<branchname>' set up to track remote branch '<branchname>' from 'origin'"
# OR

$ git checkout <branchname>		# Switches to branch <branchname>
# OR
$ git checkout -b <branchname>		# Creates branch <branchname> and switches to it
# OR
$ git checkout -t origin/<branchname>	# Creates <branchname>, switches to it and tracks (for push and pull) its remote branch
# OR
$ git checkout -b <branchname> origin/<branchname> # Same as previous but local and tracking branches can have different names
```
- You can now make changes to the new branch `<branchname>` without affecting the `main` branch.
- If after switching to the branch you type `git branch`, the terminal will display: `main`, `* <branchname>`.
- [Link](https://stackoverflow.com/questions/10002239/difference-between-git-checkout-track-origin-branch-and-git-checkout-b-branch) explaining the difference between `git checkout -b` and `git checkout -t` for tracking a remote branch.


### See List of Commits
See a list of previous commits:
```bash
$ git log				# Shows the sequence of commits in the current branch
$ git reflog				# Shows the sequence of actions in the repository
```

### Upload Changes to the Remote Repository
Save all your commits in the local branch `<branchname>` to the remote repository (your branch `<branchname>` in GitHub):
```bash
$ git checkout <branchname>

$ git push	   		   # Works like `git push <branchname>`, where `<branchname>` is the *current* branch’s remote
# OR
$ git push origin  		   # Pushes the *current* branch to the configured upstream, if it has the same name as the current branch.
# OR
$ git push origin <branchname> 	   # Essentially the syntax is `git push <to> <from>`
# OR
$ git push -u origin <branchname>  # If there is no associated remote branch to <branchname>, use this line in the first push for Git to set `origin/<branchname>` as the upstream for the current branch
				   # This is no needed if the branch was created with `git checkout -b` or `git checkout -t`
				   # Message: `Branch '<branchname>' set up to track remote branch '<branchname>' from 'origin'`
```
-Note that you need to switch to `<branchname>` because if you are on `<parent>` and type `git push origin <branchname>`, Git will try to push the local `<parent>` branch (being the *current* branch) to the remote `<branchname>`, which would be incorrect. If you are in `<parent>` and you don't want to checkout to `<branchname>`, you can use: `git push origin <branchname>:<branchname>`.
- The options above push [just the current branch](https://stackoverflow.com/questions/820178/how-do-you-push-just-a-single-git-branch-and-no-other-branches), not other branches nor the `<parent>`. However, if for every branch that exists on the local side, you want the remote side to be updated if a branch of the same name already exists on the remote side use: `git push origin :` or `git push origin +:` (for non-fast-forward updates).

In GitHub.com refresh, go to your branch `<branchname>` and click the green button 'Compare, review, create a pull request', which will show your changes in green. This is also helpful to understand some conflicts.

### Pull Requests
*Before* merging, create a pull request when collaborating with other colleagues.

Create a pull request for other people to peer review the changes by clicking the green button 'Create Pull Request'. After typing title and comments, click the green button 'Send pull request'.

Time for back and forth conversation about the changes, as well as necessary corrections (new commits and merges).
- Before you merge, you may have to resolve merge conflicts if others have made changes to the repo. 

When your pull request is approved and conflict-free, you can add your code to the `<parent>` branch.

Someone with privileges can accept the changes by clicking the green button 'Merge pull request', then the 'Confirm merge' button. The changes will now show up in the `<parent>` branch.
- It is usually a bad idea to merge your own pull requests when working with a team.

### Delete Branches
Only delete temporary branches (`ftr` and `fix`), not permanent branches (`dev`).

Once it has been merged to in its upstream branch, the branch `<branchname>` can be safely deleted.

To delete a **local** branch (which has already been fully merged in its upstream branch) from the terminal:
```bash
$ git branch -d <branchname>
```

To delete a **remote** branch from the terminal ([link](https://stackoverflow.com/questions/2003505/how-do-i-delete-a-git-branch-both-locally-and-remotely), [link](https://stackoverflow.com/questions/5094293/git-remote-branch-deleted-but-still-appears-in-branch-a)):
```bash
$ git branch -d -r origin/<branch_name>		# Remove a particular remote-tracking branch
# OR
$ git push origin --delete <branch_name>
# OR
$ git push origin :<remote_branch_name>
```

To delete a branch **in GitHub**:
- After a pull request has been approved and merged, you can delete a branch by clicking the grey button 'Delete branch'. 
- If you merge a branch locally and then deleted it from the terminal, to delete it in GitHub click on the 'branches' tab at the top of the project's contents and click the trash button nex to the name of the branch.

When you delete branches in GitHub, they will still show up in the terminal with `git branch -a`. Also, after deleting the local branch (with `git branch -d <branchname>`) and the remote branch (with `git push origin --delete <branchname>`) other machines may still have "obsolete tracking branches". To remove all such [stale](https://makandracards.com/makandra/6739-git-remove-information-on-branches-that-were-deleted-on-origin) branches locally:
```bash
$ git remote prune origin
# OR
$ git fetch --prune
# OR
$ git fetch --all --prune		# In other machines after deleting remote branches to propagate changes
# OR
$ git fetch -p				# Prune remote branches
# OR
$ git pull -p
```



## Driessen's Branching Model
Use meaningful branch names. 
- [Link](https://stackoverflow.com/questions/273695/what-are-some-examples-of-commonly-used-practices-for-naming-git-branches) for useful naming conventions that facilitate the workflow. 
- [Link](https://nvie.com/posts/a-successful-git-branching-model/) explaining a successful Git branching model.

Based on the previous two sources, I will use a forward slash separator and the following branching categories and rules: 
- `dev` branch off from `main` and merge back into `main`. It is a permanent branch.
- `ftr` branch off from `dev` and merge back into `dev`. It is a temporary branch.
- `fix` branch off from `main` or `dev` and merge back into `main` or `dev`. It is a temporary branch. `fix` branches deal with bugs found in the `dev` branch or in the `main` branch (due to a -recent- merge from the `dev` branch).

Since `dev` is a permanent branch and `fix` branches are mainly used to correct bugs, most of the branches that will be used are feature `ftr` branches. Therefore, naming conventions are needed to differentiate between them; also since `fix` branches can be branched off from `main` *or* `dev`, it will be useful to distinguish between them. Thus, these are the naming conventions for the temporary branches:
- To distinguish a `fix` branched off from `main` or `dev`, the names of `fix` branches will begin with: `fix/mst` or `fix/dev`.
- There can be three types of feature branches and so `ftr` can take any of three tokens: `data`, `code`, `docs`.
  - `data` branches deal with raw or analytic data so this token will be followed by: `raw` and `ans`.
  - `code` branches deal with pre-analysis or analysis of the data so this token will be followed by: `pre` and `ans`.
  - `docs` branches deal with issues on paper, slides, equations, figures, tables, statistics, references or settings so this token will be followed by: `ppr`, `sld`, `eqn`, `fig`, `tbl`, `sta`, `ref` and `set`.
- All three of the different types of feature branches can be used for experimenting or testing minor things unrelated to the previous categories, in which case any of the three types will be followed by: `tst`.
- **Examples**: `data/raw/feature-name`, `code/ans/feature-name`, `docs/eqn/feature-name`, `fix/dev/feature-name`, `code/tst/feature-name`, `docs/tst/feature-name`.
- Therefore, there are in total 17 possible types of temporary branches: 15 feautre branches (12 regular, 3 for tests), 2 fix branches.
- With this convention (names *and* forward slashes), no feature branch can have the following names: `ftr/cat` (e.g. `data/raw`,`code/ans`), `fix/dev`, `fix/mst`. That is, their names need to include the `/feature-name` part (see first link above).

### Driessen's Model Adapted To A Research Project
[Implementation](https://stackoverflow.com/questions/4470523/create-a-branch-in-git-from-another-branch) of Driessen's branching model to a research project:
```bash
$ git checkout -b <branchname> <parent>	# Create a new branch **off** the `<parent>` branch *and* go to the new branch
					# Same as: git checkout <parent>, git branch <branchname>, git checkout <branchname>

$ git commit -am "Your message"		# Commit changes

$ git checkout <parent>
$ git merge --no-ff <branchname>	# Merge your changes to <parent> without a fast-forward

$ git push origin <parent>		# Push changes to the server
$ git push origin <branchname>
$ git branch -d <branchname>		# Optional: remove local and remote branches
$ git push origin --delete <branchname>
```

Implementation following the naming conventions:
```bash
# Develop branch
$ git checkout -b dev main
$ git push -u origin dev		# Sets the upstream for dev and see it in GitHub
	# Usual workflow
$ git status
$ git commit -am "Your message"
	# To merge dev branch into main (close all open files from the dev branch first)
$ git checkout main
$ git pull
$ git merge --no-ff dev			# Merge your changes to main without a fast-forward
$ git push origin main		# Push changes to the server
$ git push origin dev			# At this point, you can go back to dev branch (`git checkout dev')
	# To delete the dev branch
$ git branch -d dev			# Lowercase -d means "safe delete" e.g. only delete if merged; instead -D means force delete 
$ git push origin --delete dev


# Feature branches
	# Create the branch
$ git checkout -b ftr/cat/name dev
$ git push -u origin ftr/cat/name	# Sets the upstream for the branch and see it in GitHub
	# Usual workflow
$ git status
$ git commit -am "Your message"
	# To merge feature branch into dev (close all open files from the feature branch first)
$ git checkout dev
$ git pull				# Pull before push to ensure you have the latest version of the remote dev branch
$ git merge --no-ff ftr/cat/name	# Merge your branch changes to dev without a fast-forward
$ git pull				# When working in teams, it might be needed to again pull to ensure you have the latest version of the remote dev branch and there are no conflicts
$ git push origin dev			# Push changes to the server
$ git push origin ftr/cat/name		# Might need `git branch` for the name of the feature branch
	# To delete the feature branch
$ git branch -d ftr/cat/name		# Optional: remove fully merged local and remote branches
$ git push origin --delete ftr/cat/name


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
```

## Collaborating with Git (need work)

[Can multiple people commit to the same branch?](https://stackoverflow.com/questions/913012/committing-to-the-same-branch-with-git#:~:text=Multiple%20people%20can%20work%20on,with%20both%20of%20your%20changes.)

[Working with branches in Git](http://genomewiki.ucsc.edu/index.php/Working_with_branches_in_Git)

[Update current branch from parent banch](https://stackoverflow.com/questions/6836461/updating-the-current-branch-from-parent-branch)

[Delete a branch locally and remotely](https://stackoverflow.com/questions/2003505/how-do-i-delete-a-git-branch-locally-and-remotely)

[Set an upstream when the current branch is behind the remote branch](https://stackoverflow.com/questions/520650/make-an-existing-git-branch-track-a-remote-branch)


## Details
- A **ref** is anything pointing to a commit (e.g. branches (heads), tags, and remote branches), they are stored in the .git/refs directory (e.g. `refs/heads/main`, `refs/remotes/main`, `refs/tags`). For example, `refs/heads/0.58` specifies a branch named `0.58`; if you don't specify what namespace the ref is in, Git will look in the default ones, so using only `0.58` is ambiguous (there could have both a `branch` and a `tag` named `0.58`).
- When an update changes a branch (or more in general, a ref) that used to point at commit A to point at another commit B, it is called a **fast-forward** update if and only if B is a descendant of A. Hence a fast-forward update from A to B does not lose any history.
- To check out files from a previous commit (to reverse changes): `git checkout COMMIT_IDENTIFIER -- file1, file2`.
- Warning: `git reset` have options `--hard` and `--soft` that can be used to rewrite history and to throw out commits that you no longer want.
- If you use `git init` to create a local repository, and then want to upstream it to a remote (empty) repo, in your first push you need to use: `git push -u origin main`. This will create an upstream `main` branch on the upstream (`git push origin main`) *and* will record `origin/main` as a remote tracking branch so that the local branch `main` will be pushed to the upstream (origin) `main` (upstream branch). Since Git 1.7.11, the default push policy is `simple`: push only the current branch, and only if it has a similarly named remote tracking branch on the upstream. [Link](https://stackoverflow.com/questions/17096311/why-do-i-need-to-explicitly-push-a-new-branch/17096880#17096880) for an explanation.
- [Why do I have to `git push --set-upstream origin <branch>`?](https://stackoverflow.com/questions/37770467/why-do-i-have-to-git-push-set-upstream-origin-branch)
- Reasons for not keeping the repository in Dropbox: there is a chance of conflicts between the syncing of Dropbox and GitHub, and the space limit in Dropbox might be an issue when the project grows in size.
- Reasons for having a project for each chapter: GitHub has a limit of 1 GB per project and has limits of 100MB per file, keeping them separate minimizes these issues.
- To understand GitHub from scratch: Healey (intuitively explains Git workflow); Youtube videos by Learn Code show the basic workflow; Pinter (2019) explains benefits and gives recommendations; Sviatoslav (2017); Notes by Fernández-Villaverde complement/reinforce the previous one; StackExchange links for clarification, reinforcement and understanding the daily workflow.
- It is recommended to include a license file in your repositories, or at least explicitly claim copyright by including: Copyright [yyyy] [name of copyright owner].
- [Working with large files](https://help.github.com/en/articles/working-with-large-files)
- [Ignoring files](https://help.github.com/en/articles/ignoring-files)
- [Rename a repository](https://help.github.com/en/articles/renaming-a-repository)
- [Relocate a local repo](https://stackoverflow.com/questions/11384928/change-git-repository-directory-location)

## Solving Issues
See history of local commits
```bash
$ git log
```

See history of remote commits
```bash
$ git log HEAD..origin/brach_name
```

To remove a big file [wrongly committed](https://thomas-cokelaer.info/blog/2018/02/git-how-to-remove-a-big-file-wrongly-committed/)
```bash
$ git filter-branch --tree-filter 'rm -rf Codes/Pre-Analysis/struct_data.mat' HEAD
$ git push
```

It may cause the local and remote branches to diverge, in which case (see [this](https://stackoverflow.com/questions/2452226/master-branch-and-origin-master-have-diverged-how-to-undiverge-branches) and [this](http://serebrov.github.io/html/2012-02-13-git-branches-have-diverged.html))
```bash
$ git rebase origin/code/ans/fit-models
```

To exit the prompt `>` (e.g. if you opened up a string with the odd number of `'` characters when making a commit) and go back to your normal bash prompt, [use](https://stackoverflow.com/questions/26228848/how-do-i-exit-my-git-commit-message-im-not-in-the-vim-i-used-the-commit-m) `ctrl` + `c` or close the string by typing `'` again.

If `git push` displays the following (due to a git-lfs hook):
```bash
This repository is configured for Git LFS but 'git-lfs' was not found on your path. If you no longer wish to use Git LFS, remove this hook by deleting .git/hooks/pre-push.

error: failed to push some refs to 'github.com:pavelsolis/Ch_MPS.git'
```
What [works](https://github.com/git-lfs/git-lfs/issues/333) is to remove the folder `.git/lfs` and the file `.git/hooks/pre-push`.

Only push into [bare repositories](https://stackoverflow.com/questions/1298499/git-push-not-send-changes-to-remote-git-repository).

To resolve merge conflicts, see [this](https://www.simplilearn.com/tutorials/git-tutorial/merge-conflicts-in-git).

To recover from an incomplete rebase operation and detached HEAD, see [this](https://stackoverflow.com/questions/5772192/how-can-i-reconcile-detached-head-with-master-origin). 
