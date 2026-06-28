# Git Academy

[English](README.md) | [中文](README_CN.md)

A personal reference for Git commands — what they do, when to use them, and how they relate to each other.

---

## Quick Reference Cheat Sheet

**Setup & Init**
| Command | What it does |
|---|---|
| `git init` | Create a new local repo |
| `git clone <url>` | Download a repo from GitHub |
| `git remote add origin <url>` | Connect local repo to GitHub |
| `.gitignore` | Tell Git which files to never track |

**Daily Workflow**
| Command | What it does |
|---|---|
| `git status` | Show current state |
| `git add <file>` | Stage a file |
| `git add .` | Stage all changes |
| `git commit -m "msg"` | Save a snapshot |
| `git push` | Send commits to GitHub |
| `git pull` | Download + apply remote changes |
| `git fetch` | Download remote changes only |

**Branching & Merging**
| Command | What it does |
|---|---|
| `git branch` | List branches |
| `git branch <name>` | Create a branch |
| `git checkout <name>` | Switch to a branch |
| `git checkout -b <name>` | Create + switch in one step |
| `git merge <name>` | Merge branch into current |
| `git rebase <name>` | Replay commits on top of branch |
| `git stash` | Shelve uncommitted changes |
| `git stash pop` | Restore shelved changes |
| `git cherry-pick <hash>` | Apply one specific commit |

**Undoing Changes**
| Command | What it does |
|---|---|
| `git revert <hash>` | Undo a commit (safe, adds new commit) |
| `git reset HEAD~1` | Undo last commit, keep changes |
| `git reset --hard HEAD~1` | Undo last commit, discard changes entirely |

**Inspection**
| Command | What it does |
|---|---|
| `git log --oneline` | Compact commit history |
| `git diff` | Show unstaged changes |
| `git diff --staged` | Show staged changes |
| `git blame <file>` | Show who changed each line |
| `git tag v1.0.0` | Mark a release |
| `git bisect start` | Start binary bug search |

---

## Table of Contents

1. [git init](#git-init)
2. [git clone](#git-clone)
3. [git remote](#git-remote)
4. [.gitignore](#gitignore)
5. [git status](#git-status)
6. [git add](#git-add)
7. [git commit](#git-commit)
8. [git push](#git-push)
9. [git pull](#git-pull)
10. [git fetch](#git-fetch)
11. [git pull vs git fetch](#git-pull-vs-git-fetch)
12. [git branch](#git-branch)
13. [git checkout](#git-checkout)
14. [git merge](#git-merge)
15. [git rebase](#git-rebase)
16. [git merge vs git rebase](#git-merge-vs-git-rebase)
17. [git stash](#git-stash)
18. [git cherry-pick](#git-cherry-pick)
19. [git revert](#git-revert)
20. [git reset](#git-reset)
21. [git revert vs git reset](#git-revert-vs-git-reset)
22. [git log](#git-log)
23. [git diff](#git-diff)
24. [git blame](#git-blame)
25. [git tag](#git-tag)
26. [git bisect](#git-bisect)

---

## git init

Creates a brand new Git repository in the current folder.

```bash
git init                    # initialize repo in current directory
git init my-project         # create a new folder and initialize inside it
```

It creates a hidden `.git` folder which is where Git stores all history, branches, commits — everything. Without it, Git knows nothing about the folder.

Typical flow for a new project:
```bash
mkdir my-project && cd my-project
git init
# create some files...
git add .
git commit -m "Initial commit"
git remote add origin <github-url>
git push -u origin main
```

Think of it as "turn this plain folder into a Git-tracked project." You only run it once at the start. If you're cloning an existing repo from GitHub, you don't need `git init` — `git clone` handles it automatically.

---

## git clone

Downloads a full copy of a repository from GitHub (or any remote) to your local machine for the first time.

```bash
git clone <url>
```

It's a one-time setup action. After that you use `pull`/`push` to sync. Think of it as the starting point before `pull`/`push` become relevant.

---

## git remote

Manages connections to remote repositories (like GitHub).

```bash
git remote -v                          # list remotes and their URLs
git remote add origin <url>            # add a remote named "origin"
git remote remove origin               # remove a remote
git remote set-url origin <new-url>    # change the URL of a remote
```

"origin" is just the conventional name for the default remote — it's not special, just a nickname for the GitHub URL.

Example output of `git remote -v`:
```
origin  https://github.com/username/repo.git (fetch)
origin  https://github.com/username/repo.git (push)
```

Think of it as your local repo's address book — it maps short names like `origin` to full GitHub URLs so you don't have to type the URL every time you push or pull.

---

## .gitignore

A file that tells Git which files and folders to **never track or commit**.

Place a file named `.gitignore` in the root of your repo. Git will never stage or commit anything matching the patterns inside it — even if you run `git add .`.

```
# macOS
.DS_Store

# Editor
.vscode/
.idea/

# Misc
*.log
```

Common patterns:
| Pattern | What it ignores |
|---|---|
| `*.log` | All files ending in `.log` |
| `folder/` | An entire folder |
| `**/*.log` | All `.log` files in any subfolder |
| `!important.log` | Exception — track this even if `*.log` is ignored |

Common use cases: preventing accidental commits of secrets (`.env`), OS clutter (`.DS_Store`), editor config (`.vscode/`), or build artifacts (`node_modules/`, `dist/`).

---

## git status

Shows the current state of your working directory and staging area.

```bash
git status
```

It tells you:
- Which files are **modified** but not yet staged
- Which files are **staged** and ready to commit
- Which files are **untracked** (new, Git doesn't know about them yet)
- Which branch you're on
- Whether you're ahead/behind the remote

Example output:
```
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  modified:   README.md

Untracked files:
  new-file.txt
```

Think of it as a snapshot of "what's going on right now" before you decide what to stage and commit. Most people run it constantly — it's the most frequently used Git command day-to-day.

---

## git add

Stages files, telling Git "include these changes in the next commit."

```bash
git add README.md           # stage a specific file
git add .                   # stage everything in current directory
git add -p                  # interactively stage chunks (pick what to include)
```

The staging area exists so you can be deliberate — you might have changes in 5 files but only want to commit 2 of them right now.

```bash
# typical flow
git status        # see what changed
git add README.md # stage just what you want
git commit -m "update readme"
```

Think of it as packing a box before shipping — `git add` puts things in the box, `git commit` seals and sends it.

---

## git commit

Saves a permanent snapshot of your staged changes to the repository's history.

```bash
git commit -m "your message"          # commit with a message
git commit -am "your message"         # stage all tracked files + commit in one step
git commit --amend                    # edit the last commit (before pushing)
```

Every commit has:
- A unique hash (e.g. `fdb14c4`)
- Author + timestamp
- A message describing what changed
- A snapshot of all staged files

Good commit messages describe **why**, not what:
```bash
# bad
git commit -m "changed README"

# good
git commit -m "Add GitHub token generation guide to prerequisites"
```

The full flow:
```bash
git add README.md          # stage
git commit -m "message"    # snapshot
git push                   # send to GitHub
```

Think of it as hitting Save on a video game — it creates a checkpoint you can always return to.

---

## git push

Sends your local commits to the remote repository on GitHub.

```bash
git push                        # push current branch to its tracked remote
git push origin main            # explicitly push main to origin
git push -u origin main         # push + set upstream (first time only)
git push origin v1.0.0          # push a specific tag
git push --force                # overwrite remote history (dangerous)
```

The `-u` flag (upstream) is only needed once — it links your local branch to the remote so future `git push` with no arguments just works.

Think of it as the final step that makes your work visible to the outside world — everything before it (add, commit) is local only. Nothing reaches GitHub until you push.

---

## git pull

Downloads remote changes from GitHub and immediately applies them to your current branch (fetch + merge in one step).

```bash
git pull                        # pull from tracked remote branch
git pull origin main            # explicitly pull main from origin
```

---

## git fetch

Downloads remote changes but does **not** apply them. Your local files stay untouched — you're just updating your local knowledge of what's on GitHub.

```bash
git fetch                       # fetch all remotes
git fetch origin                # fetch from origin
```

---

## git pull vs git fetch

| | `git fetch` | `git pull` |
|---|---|---|
| Downloads remote changes | Yes | Yes |
| Updates local files | No | Yes |
| Safe to run anytime | Yes | Usually |

- **`git fetch`** = "check what changed"
- **`git pull`** = "check + apply"

---

## git branch

Creates, lists, or deletes branches.

```bash
git branch                    # list all local branches
git branch feature-x          # create a new branch
git branch -d feature-x       # delete a branch (safe, warns if unmerged)
git branch -D feature-x       # force delete
git branch -a                 # list all branches including remote
```

A branch is just a pointer to a commit — it lets you work on something in isolation without touching `main`.

Common flow:
```bash
git branch feature-x          # create
git checkout feature-x        # switch to it
# do work, commit...
git checkout main
git merge feature-x           # bring it back
```

---

## git checkout

Switches between branches or restores files.

```bash
git checkout main              # switch to main branch
git checkout feature-x         # switch to another branch
git checkout -b feature-x      # create + switch in one step
git checkout abc1234           # switch to a specific commit (detached HEAD)
git checkout -- README.md      # discard changes to a file, restore last commit
```

It does two somewhat unrelated things:
1. **Switch branches** — move your working directory to a different branch
2. **Restore files** — throw away uncommitted changes to a specific file

Because it does too much, Git later split it into two cleaner commands:
- `git switch` — for switching branches
- `git restore` — for restoring files

`git checkout` still works and is widely used, but `switch`/`restore` are the modern replacements.

---

## git merge

Combines branches by creating a new "merge commit". Preserves the full history of both branches exactly as they happened.

```bash
git checkout main
git merge feature-x            # merge feature-x into main
```

Think of it as: "these two roads met at an intersection" — you can see both paths in the history.

---

## git rebase

Replays your commits on top of another branch as if you branched off later. History looks linear and clean, but it rewrites commits.

```bash
git checkout feature-x
git rebase main                # replay feature-x commits on top of main
```

Think of it as: "pretend you always started from the other road" — one clean, linear path.

---

## git merge vs git rebase

| | `git merge` | `git rebase` |
|---|---|---|
| History | Preserves both paths | Linear, rewritten |
| Safety | Safe on pushed branches | Dangerous on pushed branches |
| Readability | Shows full context | Cleaner log |

- Use **merge** when working with others or on shared branches.
- Use **rebase** when you want a clean history on a local branch before merging.

---

## git stash

Temporarily shelves your uncommitted changes so you can switch context, then come back to them later.

```bash
git stash        # save uncommitted changes, revert to clean state
git stash pop    # restore your shelved changes
git stash list   # see all stashed entries
```

Think of it as a clipboard for work-in-progress. Nothing is committed, nothing is lost — it's just set aside.

---

## git cherry-pick

Takes a specific commit from anywhere in history and applies it to your current branch, without merging the whole branch.

```bash
git cherry-pick abc1234   # apply just that one commit here
```

Use case: you fixed a bug on `main` and want that exact fix on an older branch too — without pulling in everything else from `main`.

Think of it as "I want just that one commit, not the whole branch."

---

## git revert

Creates a **new commit** that undoes a previous one. History is preserved. Safe to use on shared/pushed branches.

```bash
git revert abc1234        # create a new commit that undoes abc1234
```

Think of it as: "I made a mistake, let me record the fix."

---

## git reset

Moves the branch pointer backwards, **erasing** commits from history. Destructive. Dangerous on pushed branches because it rewrites history others may have pulled.

```bash
git reset HEAD~1           # undo last commit, keep changes staged
git reset --hard HEAD~1    # undo last commit, discard changes entirely
```

Think of it as: "pretend that commit never happened."

---

## git revert vs git reset

| | `git revert` | `git reset` |
|---|---|---|
| History | Preserved (adds a new commit) | Rewritten (erases commits) |
| Safe on pushed branches | Yes | No |
| Use when | Already pushed to GitHub | Local only, not yet pushed |

Simple rule:
- Already pushed → use `revert`
- Local only → `reset` is fine

---

## git log

Shows the commit history of the repository.

```bash
git log                      # full history
git log --oneline            # compact, one line per commit
git log --oneline --graph    # visual branch/merge tree
git log -5                   # last 5 commits only
git log -- README.md         # history for a specific file
```

Think of it as the full timeline of everything that was ever committed. `git log` tells you *what commits* exist; `git diff` tells you *what actually changed* inside them.

---

## git diff

Shows exactly what changed, line by line.

```bash
git diff                        # unstaged changes (working tree vs last commit)
git diff --staged               # staged changes (what's about to be committed)
git diff abc1234 def5678        # difference between two commits
git diff main feature-branch    # difference between two branches
git diff README.md              # changes in a specific file only
```

Output uses `+` for added lines and `-` for removed lines.

---

## git blame

Shows who last modified each line of a file, and in which commit.

```bash
git blame README.md
```

Example output:
```
abc1234 (Alan  2026-06-20) ## Overview
def5678 (Alan  2026-06-18) Some content here...
```

Use case: something is broken or confusing in a specific line — `git blame` tells you who wrote it, when, and which commit to look at for context.

The name sounds accusatory but it's really just "who do I ask about this line."

---

## git tag

Marks a specific commit with a named label, typically used to mark release versions.

```bash
git tag v1.0.0           # create a tag at current commit
git push origin v1.0.0   # push the tag to GitHub
git tag                  # list all tags
```

Unlike branch names which move forward as you commit, tags stay fixed to that exact point in history forever.

Most common use: marking releases (`v1.0`, `v2.1.3`) so you can always check out the exact code that was shipped.

---

## git bisect

Binary searches through your commit history to find which commit introduced a bug.

```bash
git bisect start
git bisect bad           # current commit is broken
git bisect good v1.0.0   # this old commit was fine
# Git checks out a middle commit — you test it, then:
git bisect good          # or: git bisect bad
# Git keeps narrowing until it finds the exact culprit commit
git bisect reset         # done, go back to normal
```

Instead of checking 100 commits one by one, Git cuts the search space in half each time — finds the bad commit in ~7 steps instead of 100.

---

*Built with [Git Academy](https://github.com/zzpy20/Git-Academy)*
