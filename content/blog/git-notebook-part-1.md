+++
title = "Git Notebook - Part 1"
date = "2025-09-13T21:25:01+03:00"
description = "Guide of how to use git"
series = "Mastering Git"
tags = ["linux", "tools", "guides"]
toc = true
+++
## Chapter 1 | Introduction

Git is _the_ distributed [version control system](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control) (VCS). Nearly every developer in the world uses it to manage their code. It has quite a monopoly on VCS. Developers use Git to:

- Keep a history of their code changes
- Revert mistakes made in their code
- Collaborate with other developers
- Make backups of their code

### Overview about the contents:
#### Porcelain: (most used)
- `git status`
- `git add`
- `git commit`
- `git push`
- `git pull`
- `git log`

#### Plumbing
- `git apply`
- `git commit-tree`
- `git hash-object`

---
## Chapter 2 | git repository (core concepts)

4 states:
1. Working Directory or "working tree"
    - The actual filesystem
    - Stored in desk
    - by editing the file

2. Index (Staging Area)
    - A snapshot of what you plan to commit
    - Stored in .git/index
    - by git add file (from tree to index)

3. Repository (Commits, HEAD)
    - The database of snapshots
    - Stored in .git/objects
    - by git commit (from index to local repo)

4. Remote Repository
    - a remote repo
    - Stored mostly on the cloudin (github, gitlab ...)
    - by git remote add (from local to remote)

### States of Changes

* Untracked: file exists in working tree but not in index.
* Modified (unstaged): file changed in working tree, not yet staged.
* Staged: change recorded in index, waiting to be committed.
* Committed: change saved permanently into the repo as a commit object.


---
## Chapter 3 |  git internals (Git’s Four Object Types)

Inside .git/objects/, Git stores content-addressed objects. The key is
the SHA-1 hash of the content (with a header). There are four object types:

1. Blob
* What it is: File contents (binary or text).
* Internals: Stored as "blob and hashed with SHA-1.
* Mental model: "Just the contents of one file, nothing else (no metadata)."

2. Tree
* What it is: Directory (list of entries).
* Mental model: "A folder structure mapping names → blobs/trees."

3. Commit
* What it is: Snapshot pointer.
* Internals: Stores the SHA0 of a tree (root directory snapshot), parent commits, author, message.
* Mental model: "A node in the history graph pointing to a tree."

4. Tag
* What it is: Named pointer to a commit (sometimes with extra metadata).

---
## Chapter 4 | git config

`git config <action> <scope> <section.key> "value"`

> **action**:    --add, --list, --get, --unset, --unset-all, --remove-section
> **scope**:     --global, --local
> **section**:   user.name, user.email, init.defaultBranch
> **value**:     main, master

---
## Chapter 5 | git branches

>**A branch is just a named pointer to a specific commit**

> [!info] Git Branch
> `git branch` → list branches
> `git branch <name>` → create branch
> `git branch -m <oldname> <newname>` → change branch's name

> [!info] Git switch
> `git switch <name>` → switch to branch
> `git switch -c <name>` → create + switch
> `git branch -d <name>` → delete branch

---
## Chapter 6 | git merge

>**A process to merge the two branches with a merge commit**

A - B - C - F    main
   \     /
    D - E        other_branch

> `git merge other_branch` → in main branch

---
## Chapter 7 | git rebase

>git rebase stands for changing the base commit of two branch hence the name re-base

from this
A - B - C    main
   \
    D - E    feature_branch
to
A - B - C         main
         \
          D - E   feature_branch

>[!HINT]
>- always rebase
>- squash to a single commit
>- revert when needed

>[!IMPORTANT]
Rebase your work onto others’ work → OK.
Your feature branch rebased onto main.}
`git rebase main`
Rebase others’ work onto your work → Bad.
{Public branch rebased onto your feature.}
`git rebase feature`

---
## Chapter 8 | git reset

`git reset <action> commit-ish`
action: --soft, --mixed, --hard
commit-ish : commit-hash, HEAD~n

HEAD~ or HEAD~1 --> commit before this one

HEAD~2 -> two commits before

### git reset internals
1- all three modes do the exact same first step
2- move HEAD to the target commit

> flags tell git what to do with (index, working directory)

`git reset --soft`
- staging area: preserved, All differences between your old commit and the target commit are placed here
- working directory: untouched
- All the changes from the deleted commits are instantly placed right back into your Staging Area.
- Use case: Squashing or editing recent commits without losing your work.

`git reset --hard`
- staging area: wiped to match target commit
- working directory: overwritten to match target commit
-> wipes the staging area, overwrites your working directory to match target commit
-> it only overwrites only unstaged but (untracked will not be touched)
* *Use case:* Nuclear option to discard bad experiments and return to a known state.

`git reset --mixed`
- wipes the staging area to match target comit
- working directory: untouched, diff will be in unstaged state

### Example:
main: A -> B -> C -> D
HEAD:                ^

if ... is used:
`git reset --hard HEAD~2`  -- goes to b (b commit-hash can be used)
-> C, D commit is gone (not forever "reflog")
-> like you have just commit B
> if you have staged and unstaged changes. you can't recove it  at all
but if they are commited git reflog may help you

`git reset --soft HEAD~2`  -- goes to b (b commit-hash can be used)
-> C, D changes are  staged in the index
-> like you have made all thea changes in c, d and only add them to index

`git reset --mixed HEAD~2`  -- goes to b (b commit-hash can be used)
-> C, D changes are  unstaged in the index
-> like you have made all thea changes in c, d and never added them to index

---

## Chapter 9 | git remote
`git remote <action> <name> <uri>`
action: add, rename, remove
uri(uniform resource identifier): urls, relative path

> Remote is just another repo that has branches

`git fetch`: downloads commits and updates remote-tracking branches
does not change the current branch or working files.

`git merge`: merging branches between local ******

---
## Chapter 10 | Github

> [!EXAMPLE]
> `git remote add origin git@github.com:userName/repoName`      // ssh
> `git remote add origin https://github.com/userName/repoName`  // https

`git ls-remote`: to list the rmeotes

### git push
`git push <remote-repository> <local-branch>`

`git push` sends local changes to any "remote"

> [!EXAMPLE]
>`git push -u origin main `
>  to push from current branch from local  -->  origin/main {remote}/{branch}
> -u set the upstream between remote and local repos


#### Alternative Options
`git push origin <localbranch>:<remotebranch>`
- git push origin main:main
- push my main local to origin main

- push with diff name: git push origin main:feature-grid
- delete remote branch:
    - git push origin :old-feature
    - git push origin --delete old-feature

### git pull
`git pull [<remote>/<branch>]`
   git-pull - Fetch from and integrate with another repository or a local

#### pull request
>pull request is a way to propose changes, typically to the rest of your team,
>or to the maintainer of a project you're contributing to.

`merge branches in remote`

merges branches in remote: (by pull request)
- Base: main <--  compare: branch

### git restore

discarding unstaged changes:    `git restore file`
unstaging a file:               `git restore --staged file`

---
## Chapter 11 | gitignore
> a file named .gitignore that gives instruction to git internals on which to track

> [!NOTE]
>- can be at root or in a specific folders
>- nested gitignore only applies to the directory it's in and its subdirectories.
>- .git/info/exclude is local gitignore that doesn't need to be commited
>- order matters in a `.gitignore` so be careful

### pattern
a test rule with wild cards, let's go over them

\* --> anynumber of characters `*.log`
\\ --> anchorying to the root directory not any sub `\main.py`
\! --> negating an existing ignore `*.txt` `!important.txt`
\# --> commenting any text `# idk`


> [!IMPORTANT]
some rules of thumb for coding projects:
>- Ignore things that can be generated (e.g. compiled code, minified files, etc.)
>- Ignore dependencies (e.g. node_modules, venv, packages, etc.)
>- Ignore things that are personal or specific to how you like to work (e.g. editor settings)
>- Ignore things that are sensitive or dangerous (e.g. .env files, passwords, API keys, etc.)

## References

[Git course on boot.dev by The Primeagen](https://youtu.be/rH3zE7VlIMs)

