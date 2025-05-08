# Comprehensive Git Documentation for Beginners

## Table of Contents
1. [Introduction to Git](#introduction-to-git)
2. [Installation and Setup](#installation-and-setup)
3. [Basic Concepts](#basic-concepts)
4. [Getting Started](#getting-started)
5. [Core Git Commands](#core-git-commands)
6. [Working with Remote Repositories](#working-with-remote-repositories)
7. [Branching and Merging](#branching-and-merging)
8. [Inspecting and Comparing](#inspecting-and-comparing)
9. [Undoing Changes](#undoing-changes)
10. [Git Configuration](#git-configuration)
11. [Advanced Git Features](#advanced-git-features)
12. [Common Workflows](#common-workflows)
13. [Troubleshooting](#troubleshooting)
14. [Recommended Tools](#recommended-tools)
15. [Additional Resources](#additional-resources)

## Introduction to Git

### What is Git?
Git is a distributed version control system designed to track changes in source code during software development. It was created by Linus Torvalds in 2005 for the development of the Linux kernel. Git allows multiple developers to collaborate on projects of any size with speed and efficiency.

### Why Use Git?
- **History tracking**: Git maintains a complete history of changes made to your codebase
- **Collaboration**: Makes it easy for multiple people to work on the same project
- **Branching**: Allows you to create separate lines of development
- **Backup**: Serves as a backup for your code when pushed to remote repositories
- **Experimentation**: Provides a safe environment to try new ideas without risking the main codebase

### How Git Works
Git stores data as a series of snapshots. When you commit changes, Git takes a picture of what all your files look like at that moment and stores a reference to that snapshot. For efficiency, if files haven't changed, Git doesn't store them again—just a link to the previous identical file.

## Installation and Setup

### Installing Git

#### On Windows:
Download the installer from [git-scm.com](https://git-scm.com/download/win) and follow the installation instructions.

#### On macOS:
1. Install with Homebrew (recommended):
   ```
   brew install git
   ```
2. Or download from [git-scm.com](https://git-scm.com/download/mac)

#### On Linux (Debian/Ubuntu):
```
sudo apt-get update
sudo apt-get install git
```

#### On Linux (Fedora):
```
sudo dnf install git
```

### Verifying Installation
After installation, verify Git is installed correctly:
```
git --version
```
This should display the installed version of Git.

### First-Time Setup
Configure your identity (this will be associated with your commits):
```
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Configure default editor:
```
git config --global core.editor "code --wait"  # For VS Code
```

## Basic Concepts

### Repository (Repo)
A Git repository is a directory that contains your project's files and the entire history of changes. It includes a special `.git` directory that stores all the version history data.

### Commit
A commit is a snapshot of your repository at a specific point in time. Each commit has a unique identifier (hash) and includes metadata such as author, date, and message.

### Working Directory
The working directory is where you modify files before committing them to the repository.

### Staging Area (Index)
The staging area is a space where you can group changes before committing them. This allows you to create meaningful commits that include related changes.

### Branch
A branch is a parallel version of the repository. It allows you to work on different features or ideas without affecting the main codebase.

### Remote
A remote is a version of your repository hosted on the internet or network. It allows for collaboration with others.

## Getting Started

### Initializing a Repository
To start tracking a project with Git:
```
git init
```
This creates a new Git repository in your current directory.

### Cloning an Existing Repository
To get a local copy of an existing repository:
```
git clone <repository-url>
```

Options:
- `--branch <branch-name>`: Clone a specific branch
- `--depth <depth>`: Create a shallow clone with limited history
- `--recursive`: Clone submodules too

Example:
```
git clone https://github.com/username/project.git
```

## Core Git Commands

### Checking Status
To see the state of your working directory and staging area:
```
git status
```

Options:
- `-s` or `--short`: Give a short output
- `-b` or `--branch`: Show branch information

### Adding Files to Staging
To add files to the staging area:
```
git add <file-name>
```

Common variants:
- `git add .`: Add all new and modified files
- `git add -u`: Add all modified and deleted files (but not new files)
- `git add -A` or `git add --all`: Add all changes

Options:
- `-p` or `--patch`: Interactively choose parts of files to add
- `-i` or `--interactive`: Enter interactive mode

### Committing Changes
To create a new commit with the changes in the staging area:
```
git commit -m "Your commit message"
```

Options:
- `-a` or `--all`: Automatically stage all modified and deleted files
- `--amend`: Modify the most recent commit
- `-S`: Sign the commit with your GPG key

Best practices for commit messages:
- Keep the first line under 50 characters
- Use imperative mood ("Add feature" not "Added feature")
- Explain what and why, not how

Example:
```
git commit -m "Add user authentication feature"
```

### Removing Files
To remove files from both the working directory and the Git repository:
```
git rm <file-name>
```

Options:
- `--cached`: Remove from Git tracking but keep the file locally
- `-r`: Recursively remove directories

### Moving/Renaming Files
To move or rename files in Git:
```
git mv <old-file-path> <new-file-path>
```

## Working with Remote Repositories

### Viewing Remotes
To see the configured remote repositories:
```
git remote -v
```

### Adding a Remote
To add a new remote repository:
```
git remote add <name> <url>
```

Example:
```
git remote add origin https://github.com/username/project.git
```

### Fetching from Remote
To download changes from a remote repository without merging:
```
git fetch <remote>
```

Options:
- `--all`: Fetch from all remotes
- `-p` or `--prune`: Remove remote-tracking branches that no longer exist on the remote

### Pulling from Remote
To fetch and merge changes from a remote repository:
```
git pull <remote> <branch>
```

Example:
```
git pull origin main
```

Options:
- `--rebase`: Rebase local commits on top of fetched commits instead of merging
- `--ff-only`: Only fast-forward if possible

### Pushing to Remote
To send local changes to a remote repository:
```
git push <remote> <branch>
```

Example:
```
git push origin main
```

Options:
- `-u` or `--set-upstream`: Set the upstream branch for the current branch
- `--force`: Force push (use with caution)
- `--tags`: Push tags

## Branching and Merging

### Listing Branches
To see all local branches:
```
git branch
```

Options:
- `-r`: Show remote branches
- `-a`: Show all branches (local and remote)
- `-v`: Show last commit on each branch

### Creating a Branch
To create a new branch:
```
git branch <branch-name>
```

Or create and switch to it in one command:
```
git checkout -b <branch-name>
```

With Git 2.23+, you can also use:
```
git switch -c <branch-name>
```

### Switching Branches
To switch to an existing branch:
```
git checkout <branch-name>
```

With Git 2.23+:
```
git switch <branch-name>
```

### Merging Branches
To merge changes from another branch into your current branch:
```
git merge <branch-name>
```

Options:
- `--no-ff`: Create a merge commit even if fast-forward is possible
- `--squash`: Combine all changes into one commit
- `--abort`: Abort the merge in case of conflicts

### Handling Merge Conflicts
When Git cannot automatically merge changes, you need to resolve conflicts manually:

1. Git will mark the conflicts in the affected files
2. Edit the files to resolve the conflicts
3. Add the resolved files with `git add <file-name>`
4. Complete the merge with `git commit`

### Deleting Branches
To delete a branch:
```
git branch -d <branch-name>
```

Options:
- `-D`: Force delete even if the branch isn't fully merged

### Rebasing
To reapply your changes on top of another branch:
```
git rebase <branch-name>
```

Options:
- `-i` or `--interactive`: Enter interactive mode to modify commits
- `--continue`: Continue after resolving conflicts
- `--abort`: Abort the rebase

## Inspecting and Comparing

### Viewing Commit History
To see the commit history:
```
git log
```

Options:
- `--oneline`: Show each commit on a single line
- `--graph`: Show a text-based graph of the commit history
- `--decorate`: Show branch and tag names
- `--stat`: Show statistics for modified files
- `-p` or `--patch`: Show the changes introduced by each commit
- `--author="<name>"`: Filter by author
- `--since="<date>"`: Show commits more recent than a specific date
- `--until="<date>"`: Show commits older than a specific date

Example:
```
git log --oneline --graph --decorate
```

### Examining File Changes
To see changes between working directory and staging area:
```
git diff
```

To see changes between staging area and last commit:
```
git diff --staged
```

To see changes between two commits:
```
git diff <commit1>..<commit2>
```

Options:
- `--name-only`: Show only names of changed files
- `--word-diff`: Show differences at word level
- `--color-words`: Show colorized word diff

### Showing Commit Details
To see the details of a specific commit:
```
git show <commit>
```

### Blame
To see who last modified each line of a file:
```
git blame <file-name>
```

Options:
- `-L <start>,<end>`: Restrict to specified line range
- `-w`: Ignore whitespace changes

## Undoing Changes

### Discarding Changes in Working Directory
To discard changes in the working directory:
```
git checkout -- <file-name>
```

With Git 2.23+:
```
git restore <file-name>
```

### Unstaging Changes
To remove changes from the staging area:
```
git reset HEAD <file-name>
```

With Git 2.23+:
```
git restore --staged <file-name>
```

### Reverting Commits
To create a new commit that undoes a previous commit:
```
git revert <commit>
```

Options:
- `--no-commit` or `-n`: Don't create a commit, just update the working directory and index

### Resetting Commits
To move the current branch head to a different commit:
```
git reset <commit>
```

Options:
- `--soft`: Keep changes in staging area
- `--mixed` (default): Keep changes in working directory
- `--hard`: Discard all changes (use with caution)

Example:
```
git reset --hard HEAD~1  # Completely discard the most recent commit
```

### Recovering Lost Commits
To see all commits, including those that are no longer referenced:
```
git reflog
```

To recover a lost commit:
```
git checkout <commit-hash>
git branch recover-branch
```

### Stashing Changes
To temporarily store uncommitted changes:
```
git stash
```

Options:
- `save "message"`: Add a description to the stash
- `--include-untracked` or `-u`: Include untracked files

To list stashed changes:
```
git stash list
```

To apply a stash:
```
git stash apply  # Applies the most recent stash
git stash apply stash@{n}  # Applies a specific stash
```

To apply and remove a stash:
```
git stash pop
```

To remove a stash:
```
git stash drop stash@{n}
```

To clear all stashes:
```
git stash clear
```

## Git Configuration

### Viewing Configuration
To see all your Git configuration:
```
git config --list
```

To see a specific setting:
```
git config user.name
```

### Setting Configuration
To set a configuration value:
```
git config <option> <value>
```

Scope options:
- `--local`: For the current repository only (default)
- `--global`: For all repositories of the current user
- `--system`: For all users on the system

Common configuration options:
```
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global core.editor "code --wait"
git config --global init.defaultBranch main
git config --global pull.rebase false
git config --global color.ui auto
```

### Aliases
You can create shortcuts for common commands:
```
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
```

Usage:
```
git co main  # Same as git checkout main
```

## Advanced Git Features

### Cherry-Picking
To apply a specific commit to your current branch:
```
git cherry-pick <commit>
```

Options:
- `-e`: Edit the commit message
- `-n` or `--no-commit`: Apply changes without committing
- `-x`: Add a reference to the original commit

### Tags
To create a lightweight tag:
```
git tag <tag-name>
```

To create an annotated tag:
```
git tag -a <tag-name> -m "Tag message"
```

To list tags:
```
git tag
```

To push tags to a remote:
```
git push <remote> <tag-name>
git push <remote> --tags  # Push all tags
```

### Submodules
To add a submodule:
```
git submodule add <repository-url> <path>
```

To initialize and update submodules:
```
git submodule init
git submodule update
```

Or clone with submodules:
```
git clone --recursive <repository-url>
```

### Interactive Rebase
To modify multiple commits:
```
git rebase -i <commit>^
```
For example:
```
git rebase -i HEAD~3  # Modify the last 3 commits
```

Options in interactive rebase:
- `pick`: Use the commit as is
- `reword`: Use the commit but edit the message
- `edit`: Use the commit but stop for amending
- `squash`: Combine with previous commit
- `fixup`: Combine with previous commit, discard message
- `drop`: Remove the commit

### Bisect
To find the commit that introduced a bug:
```
git bisect start
git bisect bad  # Mark current commit as bad
git bisect good <commit>  # Mark a known good commit
```

Git will help you find the first bad commit through binary search.
When finished:
```
git bisect reset
```

### Hooks
Git hooks are scripts that run automatically before or after Git commands.
They're stored in the `.git/hooks` directory.

Common hooks:
- `pre-commit`: Run before a commit is created
- `prepare-commit-msg`: Edit commit message
- `commit-msg`: Validate commit message
- `post-commit`: Run after a commit is created
- `pre-push`: Run before pushing to a remote

To enable a hook, remove the `.sample` extension and make it executable.

## Common Workflows

### Feature Branch Workflow
1. Create a branch for a new feature:
   ```
   git checkout -b feature/new-feature
   ```

2. Make changes and commit:
   ```
   git add .
   git commit -m "Add new feature"
   ```

3. Push to remote:
   ```
   git push -u origin feature/new-feature
   ```

4. Create a pull request (on GitHub, GitLab, etc.)

5. After approval, merge:
   ```
   git checkout main
   git pull
   git merge feature/new-feature
   git push
   ```

6. Delete the branch:
   ```
   git branch -d feature/new-feature
   git push origin --delete feature/new-feature
   ```

### Gitflow Workflow
A robust workflow with dedicated branches for features, releases, and hotfixes.

Main branches:
- `main`: Production code
- `develop`: Integration branch for features

Supporting branches:
- `feature/*`: New features
- `release/*`: Preparing for a release
- `hotfix/*`: Urgent fixes for production

### Forking Workflow
Used in open-source projects:

1. Fork the repository on the hosting service
2. Clone your fork:
   ```
   git clone https://github.com/yourusername/project.git
   ```

3. Add the original repository as "upstream":
   ```
   git remote add upstream https://github.com/original/project.git
   ```

4. Create a branch for your contribution:
   ```
   git checkout -b feature/contribution
   ```

5. Make changes, commit, and push to your fork:
   ```
   git push -u origin feature/contribution
   ```

6. Create a pull request to the original repository

7. Keep your fork updated:
   ```
   git fetch upstream
   git checkout main
   git merge upstream/main
   git push
   ```

## Troubleshooting

### Common Issues and Solutions

#### "fatal: not a git repository"
You're trying to run Git commands outside of a Git repository.
Solution: Navigate to a Git repository or create one with `git init`.

#### "fatal: refusing to merge unrelated histories"
When trying to pull or merge branches with unrelated commit histories.
Solution:
```
git pull origin main --allow-unrelated-histories
```

#### "fatal: remote origin already exists"
When trying to add a remote that already exists.
Solution:
```
git remote remove origin
git remote add origin <new-url>
```

#### Merge conflicts
When Git can't automatically merge changes.
Solution:
1. Open the conflicted files and resolve the conflicts manually
2. Add the resolved files: `git add <file-name>`
3. Complete the merge: `git commit`

#### Detached HEAD state
When you checkout a specific commit instead of a branch.
Solution:
```
git checkout -b new-branch
```

#### "Permission denied (publickey)"
When you don't have the right SSH keys set up.
Solution:
1. Check if your SSH key is added: `ssh-add -l`
2. If not, add it: `ssh-add ~/.ssh/id_rsa`
3. Make sure the public key is added to your Git hosting service

#### Large files causing issues
When trying to commit large files.
Solution:
1. Use Git LFS: `git lfs track "*.extension"`
2. Or add large files to `.gitignore`

### Recovering from Mistakes

#### Accidentally committed to the wrong branch
```
git branch correct-branch
git reset HEAD~ --hard
git checkout correct-branch
```

#### Accidentally deleted a branch
If you know the commit hash:
```
git branch branch-name <commit-hash>
```

Or check the reflog:
```
git reflog
git branch branch-name HEAD@{n}
```

#### Made a typo in a commit message
For the last commit:
```
git commit --amend -m "Correct message"
```

#### Added a wrong file to staging
```
git reset <file-name>
```

## Recommended Tools

### GUI Clients
- **GitHub Desktop**: Simple client for GitHub users
- **GitKraken**: Powerful cross-platform client
- **Sourcetree**: Feature-rich client for Windows and Mac
- **VS Code**: Has built-in Git integration
- **Fork**: Fast and friendly git client

### Command Line Enhancements
- **Oh My Zsh**: Provides Git aliases and information in prompt
- **Git Bash**: Unix-like shell for Windows users
- **diff-so-fancy**: Better diff formatting
- **tig**: Text-mode interface for Git
- **Lazygit**: Terminal UI for Git

## Additional Resources

### Documentation
- [Official Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book) (free online)
- [Git Reference](https://git-scm.com/docs)

### Interactive Learning
- [Learn Git Branching](https://learngitbranching.js.org/)
- [GitHub Learning Lab](https://lab.github.com/)
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)

### Cheat Sheets
- [GitHub Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Atlassian Git Cheat Sheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)

### Best Practices
- Use meaningful commit messages
- Commit early and often
- Pull before pushing
- Use branches for new features
- Write a good `.gitignore` file
- Never rewrite published history
- Review changes before committing
