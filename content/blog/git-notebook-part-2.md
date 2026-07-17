+++
title = "Git Notebook - Part 2"
date = "2025-09-27T23:21:06+03:00"
description = "Guide of how to use git"
series = "Mastering Git"
tags = ["linux", "tools", "guides"]
toc = true
+++
## Prerequisites
>To keep in your pocket

`git commit --amend`      --> changes the last commit msg

`git reset --soft HEAD~1` --> remove last commit but keep changes staged

---
## Chapter 1 | fork

> fork is a copy of a repo to your repos

- on github instead of `creator/megacorp` to `luvimsan/megacorp`
- using gh cli  `gh repo fork `

### PRs from a fork

can be seen as a merge of branch with permission in the remote

> [!NOTE] the standard way to contribute to someone else's open-source project
>- Fork their repo into your account
>- Clone your fork to your local machine
>- Create a new branch (let's call it your_feature)
>- Make changes
>- Commit and push changes to your fork's remote your_feature branch
>- Create a pull request to original_owner/repo main from your_username/repo your_feature

---
## Chapter 2 | reflog

`git reflog` --> like `git log` but stands for "reference log"

### format of a reflog

| reflog   | meaning    |
|--------------- | --------------- |
| HEAD@{0}   | where HEAD is now   |
| HEAD@{1}   | where HEAD was 1 move ago   |
| HEAD@{2}   | where HEAD was 2 move ago   |
| HEAD@{3}   | where HEAD was 2 move ago   |
| ...        | ... |

>HEAD is a reference to the branch you're currently on.

### usage
>[!INFO]
>reflog can be used to rescue a commit has been removed
>1. git reflog to get commit hash
>2. git cat-file -p commit
>3. traverse till get the content of the commit


>[!TIP]
> a better way of rescuing a commit will be like this
> `git merge commitish` just like that
> or just `git merge HEAD@{1}`

---
## Chapter 3 | Merge conflicts
> when two branches modify the same part since their common merge base
>- merging and rebasing is the common way of making conflicts
>- git modify the conflicted file with both changes separating them with >> << ==

>[!EXAMPLE]
```csv
first_name,last_name,company,title
 <<<<<<< HEAD (branch im in) (ours)
 karson,yummy,intercooler,ceo
 ======= (end)
 jayson,gross,htmz,contributor
 >>>>>>> main (the other branch) (theirs)
```

- "ours" --> refers to the branch we are on (merging into)
- "theirs" --> refers to the branch being merged
- `git merge their_branch`

### checkout conflict

> this will resolve a conflict without the need to edit the conflicted file

`git checkout --theirs path/to/file`

- `--ours` will overwrite the file with the changes from the branch you are currently on and merging into
- `--theirs` will overwrite the file with the changes from the branch you are merging into the current branch


---
## Chapter 4 | Rebase conflicts

>[!NOTE] rebase rewrites history which sometimes may be dangerous But it provies linear history, easier for reverting

`git rebase main`
- it switch to the source branch `main`
- replays the commits from target branch im in `feat`
- it has a diff state called rebasing state

in conflict
-  ours means the source branch `main`
-  theirs means the target branch `feat`
-  `git add . && git rebase --continue`

### RERERE to the rescue

> The git rerere functionality is a bit of a hidden feature.
The name stands for “reuse recorded resolution” and,
as the name implies, it allows you to ask Git to remember
how you’ve resolved a hunk conflict so that the next time it sees the same conflict,
Git can resolve it for you automatically.

1. `Recorded preimage for 'customers/favs.md'`
2. `Recorded resolution for 'customers/favs.md'`
3. `Resolved 'customers/favs.md' using previous resolution`

### Accidental commit after rebase

If you accidentally commit the resolution of a rebase conflict, just:

`git reset --soft HEAD~1`

- The --soft flag will keep your changes, and just undo the commit.
- Then you can simply `--continue` the rebase as normal.

---
## Chapter 5 | Squash
>to squash some commits in one commit

`git rebase -i HEAD~n`
i : interactive
n : number of commits to squash
change pick --> s, squash

> [!NOTE] Why Does Rebase Squash?
> Remember, rebase is all about replaying changes. When we rebase onto a
specific commit (HEAD~n), we're telling Git to replay all the changes from
the current branch on top of that commit. Then we use the interactive flag
to "squash" those changes so that Rebase will apply them all in a single commit

### NOTES:
- never squash on main branch
    - squash on a feat branch
- the purpose of squash is to allow you to make lots of commits and then leave
the important ones and squash the other in a signle commit


---
## Chapter 6 | Stash

Definition: Temporarily saves uncommitted changes and reverts the working directory to match the latest commit (HEAD).
Command: `git stash`

**stash** stores the changes in a stack (LIFO) --> get the most recent changes first

`git stash list` example
    stash@{0}: On main: bad marketing
    stash@{1}: On main: good marketing

### useful flags
- `git stash list`           --> (list all the stashes)
- `git stash pop`            --> (applay the most recent stash entry to the working directory)
- `git stash apply`          --> Apply a specific stash without deleting it
- `git stash drop`           --> Delete a stash without applaying
- `git stash -m 'message'`   --> multiple staashes with message


---
## Chapter 7 | Revert

Definition: it's a commit that remove another commit by replaying it in reverse
and keep the full history of the change and the undoing
Command: `git revert commit-ish`

### git reset vs git revert


Mostly personal branches:
git reset --soft: Undo commits but keep changes staged
git reset --hard: Undo commits and discard changes

Mostly public branches:
git revert: Create a new commit that undoes a previous commit

---
## Chapter 8 | Cherry Pick

Definition: a way to pick a commit or a range of commits from a branch to add to
another branch without getting all the commits (if merging and rebasing is not needed)
Command: `git cherry-pick commit-ish`

### workflow
- release has a b c commits
- main has a b c d g h n
    - >instead of getting all the commits from main to release
- we can just cherry-pick g commit and added to release

---
## Chapter 9 | Bisect
a way to find where a change or a bug was introduced using binary search in O(log n)

### how to bisect
1. start with `git bisect start`
1. select bad commit `git bisect bad {commit-ish}`
    or just `git bisect bad` for the current commit
3. select good commit with git by `git bisect good {commit-ish}`
4. Git will checkout a commit between the good and bad commits for you to test to see if the bug is present
5. Execute git bisect good or git bisect bad to say the current commit is good or bad
6. Loop back to step 4 (until git bisect completes)
7. Exit the bisect mode with `git bisect reset`

### automated bisect script
>instead of selecting bad and good we can make a script to automate that such that
--> return 0 when it's a good commit
--> return 1-127 except 125 when it's a bad commit
```sh
if grep -q "SCANNING" "scripts/scan.sh"; then
    exit 1
else
    exit 0
fi
```

using this command `git bisect run ./script.sh`

---
## Chapter 10 | Worktrees
A worktree in git is an additional working directory linked to the same Git repository.

>[!NOTE] main worktree
all that time we was working with the main working tree that was created by
git-init or git-clone

all stored at .git/worktrees directory

### pros
- checkout multiple branches at the same time
- no need for git commands just two directories each has it's own branch
- replace the continous stashing

### cons
- no duplicate branches



### syntax
add  --> `git worktree add {path} {branch_name}`
    example -->   `git worktree add ../ultracorp` // using same worktree name for branch

list -->  `git worktree list`

remove --> `git worktree remove {path}`
or
prune (remove the directory) --> `git worktree prune`


---
## Chapter 11 | Tags
an immutable pointer to a specific commit

>[!INFO] unlike branches
tags can be created and deleted, but not modified

### syntax

list        --> `git tag`
create      --> `git tag {name}`

flags
name (-a)   --> `git tag -a {name}` //default first parameter
msg  (-m)   --> `git tag -a {name} -m {msg}`

push        --> `git push --tags`

### semver
semver stands for "Semantic Versioning" for versioning software

two primary purposes:
- To give us a standard convention for versioning software
- To help us understand the impact of a version change and if it's safe (how hard it will be) to upgrade to

`v3.12.5`
3  --> MAJOR (breaking changes)
12 --> MINOR (safe features)
5  --> PATCH (fafe bug fixes)
- MAJOR increments when we make "breaking" changes (this is typically a big release, for example, Python 2 -> Python 3)
- MINOR increments when we add new features in a backward-compatible manner
- PATCH increments when we make backward-compatible bug fixes

## References

[Git course on boot.dev by The Primeagen](https://youtu.be/rH3zE7VlIMs)
