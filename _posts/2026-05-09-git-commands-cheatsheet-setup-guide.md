---
title: "Git Commands Cheatsheet: Setup & Essential Guide"
description: "A quick reference Git cheatsheet covering setup, config,
  branching, staging, and essential commands for developers
  and sysadmins. Bookmark-worthy."
keywords:
  - git cheatsheet
  - git commands quick reference
  - git setup guide for beginners
  - basic git commands list
  - git init config commands
  - git branch merge workflow
  - git commit push pull cheatsheet
  - linux git commands terminal
  - git cheatsheet for developers
  - version control quick reference
categories:
  - Cheatsheets
tags:
  - git
  - version-control
  - cheatsheet
  - linux
  - developer-tools
  - terminal
  - open-source
author: kedarkhedkar
date: 2026-05-09 10:00:00 +0000
slug: git-commands-cheatsheet-setup-guide
canonical_url: https://kedar-khedkar.github.io/posts/git-commands-cheatsheet-setup-guide
image:
    path: /assets/img/posts/github-cheatsheet.png
    alt: github cheatsheet illustration

og_title: "Git Commands Cheatsheet: Setup & Essential Guide"
og_description: "All the Git commands you actually use — setup, branching,
  staging, merging. A clean quick-reference for devs & sysadmins."
og_type: article
og_url: https://kedar-khedkar.github.io/posts/git-commands-cheatsheet-setup-guide

twitter_card: summary_large_image
twitter_title: "Git Commands Cheatsheet: Setup & Essential Guide"
twitter_description: "All essential Git commands in one place — setup, branching,
  commits & more. Save this cheatsheet!"
twitter_site: "@khedkar_kedar"
---

# Git Cheatsheet

## Basic Git Commands

### Repository Setup

```bash
# Initialize a new repository
git init

# Clone an existing repository
git clone <repository-url>

# Configure user information
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Staging Changes

```bash
# Add files to staging
git add <file-name>
git add .                   # Add all changes

# Remove files
git rm <file-name>
git rm --cached <file-name> # Remove from staging only

# Move/rename files
git mv <old-name> <new-name>
```

### Committing Changes

```bash
# Commit staged changes
git commit -m "Commit message"
git commit -am "Commit message"  # Add & commit modified files
```

### Checking Status and History

```bash
# View status
git status

# View commit history
git log
git log --oneline          # Compact view
git log --graph --oneline  # Graph view
```

## Branching and Merging

### Branch Management

```bash
# Create new branch
git branch <branch-name>

# Switch branches
git checkout <branch-name>
git switch <branch-name>    # New command

# Create and switch
git checkout -b <branch-name>
git switch -c <branch-name>
```

### Merging

```bash
# Merge branch into current branch
git merge <branch-name>

# Rebase current branch
git rebase <branch-name>

# Abort merge/rebase
git merge --abort
git rebase --abort
```

## Remote Operations

### Managing Remotes

```bash
# List remotes
git remote -v

# Add remote
git remote add <name> <url>

# Remove remote
git remote remove <name>
```

### Syncing with Remote

```bash
# Fetch updates
git fetch <remote>

# Pull changes
git pull <remote> <branch>

# Push changes
git push <remote> <branch>
git push -u origin main    # Set upstream
```

## Advanced Operations

### Stashing

```bash
# Stash changes
git stash
git stash save "message"

# List stashes
git stash list

# Apply stash
git stash apply
git stash pop            # Apply and remove
```

### Cherry Picking

```bash
# Cherry pick commit
git cherry-pick <commit-hash>
```

### Reset and Revert

```bash
# Reset
git reset --soft HEAD~1   # Undo last commit, keep changes
git reset --hard HEAD~1   # Undo last commit, discard changes

# Revert commit
git revert <commit-hash>
```

## Troubleshooting

### Debug Commands

```bash
# Find problematic commit
git bisect start
git bisect bad
git bisect good <commit-hash>

# Show file changes
git blame <file-name>

# Show commit details
git show <commit-hash>
```

### Common Recovery Commands

```bash
# Recover deleted branch
git reflog
git checkout -b <branch-name> <hash>

# Restore file version
git checkout <commit-hash> -- <file-path>

# Clean working directory
git clean -n    # Dry run
git clean -f    # Force clean
```

## Common Error’s and there solution

```bash
#error: RPC failed; curl 92 HTTP/2
git config --global http.version HTTP/1.1
git config --global http.postBuffer 524288000 #Increasing the git buffer size 
```

The commands shown above represent the most commonly used Git operations for version control. This cheatsheet serves as a quick reference guide for both beginners and experienced developers. Remember to always check the Git documentation for more detailed information about specific commands and their options.

# Git config

## Set vscode as default editor

```bash
git config --global core.editor "code --wait"
```

```bash
git config --global --edit
```

```bash
[user]
	name = <name>
	email = <email>
[core]
	editor = code --wait
[help]
	autocorrect = prompt
[push]
	autoSetupRemote = true
	
[fetch]
	prune = true
[branch]
	sort = -committerdate
[diff]
	colorMoved = default
	colorMovedWS = allow-indentation-change
[alias]
	swm = switch master
	br = branch
	st = status
	co = checkout
	ci = commit
	ciam = commit -a -m
	rh = reset HEAD
	rhsoft = reset --soft HEAD
	rhhard = reset --hard HEAD
	amend = commit -a --amend
	amendnoedit = commit -a --amend --no-edit
	amendwip = commit -a --amend -m "WIP"
	amendwipsoft = commit -a --amend -m "WIP"
	amendwipsofta = commit -a --amend -m "WIP"
	aa = add --all
	cdf = clean -df
	pr = pull --rebase
	pra = pull --rebase --autostash
[http]
	version = HTTP/1.1
	postBuffer = 1048576000
[https]
	postBuffer = 1048576000

```

# How to Clone Larger Repositories (3GB+) from GitHub

Cloning large repositories from GitHub can be challenging due to the 2GB push limit. Here are several approaches to successfully work with repositories larger than 3GB:

## Method 1: Use Git LFS (Git Large File Storage)

Git LFS is designed specifically for handling large files in Git repositories:

1. Install Git LFS on your system if you haven't already:
    
    ```bash
    git lfs install
    ```
    
2. Clone the repository normally:
    
    ```bash
    git clone <https://github.com/username/large-repository.git>
    ```
    
3. Git LFS will automatically handle the large files during the clone process.

## Method 2: Mirror Clone for Large Repositories

For repositories with Git LFS objects:

1. Create a bare clone of the repository:
    
    ```bash
    git clone --bare <https://github.com/username/large-repository.git>
    ```
    
2. Navigate to the cloned repository:
    
    ```bash
    cd large-repository.git
    ```
    
3. Pull in the repository's Git LFS objects:
    
    ```bash
    git lfs fetch --all
    ```
    
4. Now you can work with the repository locally.

## Method 3: Clone in Smaller Chunks

If you're facing issues with the initial clone:

1. Clone the repository with a limited depth to get only the most recent commits:
    
    ```bash
    git clone --depth=1 <https://github.com/username/large-repository.git>
    ```
    
2. After the shallow clone completes, you can gradually fetch more history:
    
    ```bash
    cd large-repository
    git fetch --unshallow
    ```
    

## Method 4: Use GitHub CLI

GitHub CLI can handle large clones more efficiently:

1. Install GitHub CLI (if not already installed)
2. Clone using GitHub CLI:
    
    ```bash
    gh repo clone username/large-repository
    ```
    

## Troubleshooting Common Issues

If you encounter errors like `fatal: the remote end hung up unexpectedly` or `remote: fatal: pack exceeds maximum allowed size`, try these solutions:

1. Ensure you have a stable internet connection
2. Try cloning with the `-depth` parameter to limit history
3. Use Git LFS for repositories containing large files
4. Consider using a tool like GitHub Desktop which handles large repositories well

Remember that cloning a 3GB+ repository will require sufficient disk space and may take some time depending on your internet connection speed.

Sources:

- [Troubleshooting the 2 GB push limit](https://docs.github.com/en/get-started/using-git/troubleshooting-the-2-gb-push-limit)
- [Duplicating a repository](https://docs.github.com/en/repositories/creating-and-managing-repositories/duplicating-a-repository)