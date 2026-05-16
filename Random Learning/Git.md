# Git Workflow
`
```
Working Directory
       ↓ git add
Staging Area
       ↓ git commit
Local Repository
       ↓ git push
Remote Repository
```

# 1. Git Setup Commands

## Initialize Git


```
git init
```

Creates a `.git/` directory in current folder.

Example:

```
mkdir project
cd project
git init
```

---

## Clone Existing Repository

```
git clone <repo-url>
```

Example:

```
git clone https://github.com/user/project.git
```

Downloads entire repository with history.

---

## Configure Username

```
git config --global user.name "Your Name"
```

---

## Configure Email

```
git config --global user.email "you@example.com"
```

---

## View Configurations

```
git config --list
```

# 2. File Tracking Commands

---

## Check Status

```
git status
```

Shows:

- modified files
- staged files
- untracked files
- current branch

---

## Add File to Staging

```
git add file.txt
```

---

## Add All Files

```
git add .
```

Stages everything.

---

## Remove from Staging

```
git restore --staged file.txt
```

---

## Delete File

```
git rm file.txt
```

Removes from filesystem + git tracking.

---

## Rename File

```
git mv old.txt new.txt
```

# 3. Commit Commands

---

## Commit Changes

```
git commit -m "message"
```

Creates snapshot of staged changes.

---

## Add + Commit Together

```
git commit -am "message"
```

Works only for tracked files.

---

## Amend Last Commit

```
git commit --amend
```

Modify:

- commit message
- forgotten changes

# 4. Viewing History

---

## Show Commit Logs

```
git log
```

---

## One Line Log

```
git log --oneline
```

---

## Compact Graph View

```
git log --oneline --graph --all
```

Useful for branch visualization.

---

## Show Specific Commit

```
git show <commit-id>
```

---

## View Changes

```
git diff
```

Working directory vs staging.

---

## Staged Changes Diff

```
git diff --staged
```


# 5. Branching Commands

Branches allow isolated development.

---

## Create Branch

```
git branch feature
```

---

## List Branches

```
git branch
```

---

## Switch Branch

```
git checkout feature
```

Modern way:

```
git switch feature
```

---

## Create + Switch Together

```
git checkout -b feature
```

Modern:

```
git switch -c feature
```

---

## Delete Branch

```
git branch -d feature
```

Force delete:

```
git branch -D feature
```

# 6. Merge Commands

---

## Merge Branch

```
git merge feature
```

Merges `feature` into current branch.

---

## Abort Merge

```
git merge --abort
```

# 7. Remote Repository Commands

---

## Add Remote

```
git remote add origin <url>
```

---

## View Remotes

```
git remote -v
```

---

## Push to Remote

```
git push origin main
```

---

## Pull Changes

```
git pull
```

Equivalent to:

```
git fetch + git merge
```

---

## Fetch Changes Only

```
git fetch
```

Downloads remote changes without merging.

---

## Clone with Specific Branch

```
git clone -b dev <url>
```

# 8. Undo & Recovery Commands

---

## Restore File

```
git restore file.txt
```

Discard local changes.

---

## Reset Commit

## Soft Reset

```
git reset --soft HEAD~1
```

Removes commit but keeps changes staged.

---

## Mixed Reset

```
git reset HEAD~1
```

Keeps file changes but unstages.

---

## Hard Reset

```
git reset --hard HEAD~1
```

Deletes commit + changes permanently.

---

## Revert Commit

```
git revert <commit-id>
```

Creates new commit undoing old commit.

Safer than reset in shared repositories.

---

## Recover Deleted Commits

```
git reflog
```

Shows hidden history.

Extremely powerful recovery tool.

# 9. Stashing Commands

Temporarily save unfinished work.

---

## Save Stash

```
git stash
```

---

## List Stashes

```
git stash list
```

---

## Apply Stash

```
git stash apply
```

---

## Pop Stash

```
git stash pop
```

Apply + remove stash.

---

## Delete Stash

```
git stash drop
```

---

# 10. Tagging Commands

Used for releases.

---

## Create Tag

```
git tag v1.0
```

---

## Annotated Tag

```
git tag -a v1.0 -m "Version 1.0"
```

---

## Push Tags

```
git push --tags
```

---

# 11. Advanced Commands

---

## Rebase

```
git rebase main
```

Moves branch commits onto another base.

Cleaner history than merge.

---

## Interactive Rebase

```
git rebase -i HEAD~5
```

Can:

- squash commits
- reorder commits
- edit commits

---

## Cherry Pick

```
git cherry-pick <commit-id>
```

Copy one commit into another branch.

---

## Blame

```
git blame file.txt
```

Shows who changed each line.

---

## Bisect

```
git bisect
```

Binary search for bug-causing commit.

---

## Clean Untracked Files

```
git clean -fd
```

Deletes untracked files/directories.

---

# 12. GitHub Workflow Commands

---

## First Push

```
git push -u origin main
```

Sets upstream tracking.

---

## Pull Request Workflow

```
git checkout -b featuregit add .git commit -m "feature added"git push origin feature
```

Then create PR on GitHub.

---

# 13. SSH Authentication Commands

---

## Generate SSH Key

```
ssh-keygen -t ed25519 -C "email@example.com"
```

---

## Start SSH Agent

```
eval "$(ssh-agent -s)"
```

---

## Add SSH Key

```
ssh-add ~/.ssh/id_ed25519
```

---

# 14. .gitignore

Ignore files/folders.

Example:

```
node_modules/.env*.logdist/
```

---

# 15. Internal Git Concepts

Git objects:

- Blob → file data
- Tree → directories
- Commit → snapshot metadata
- Tag → named reference

Git uses:

- SHA-1/SHA-256 hashes
- Directed acyclic graph (DAG)

---

## Most Important Commands You’ll Use Daily

```
git initgit clonegit statusgit add .git commit -m ""git pullgit pushgit branchgit switchgit mergegit log --oneline --graphgit stashgit resetgit revert
```

---

## Professional Git Workflow

```
git clonegit switch -c feature# workgit add .git commit -m "implemented feature"git pull origin maingit rebase maingit push origin feature
```

---

## Dangerous Commands

Be careful with:

```
git reset --hardgit clean -fdgit push --force
```

These can destroy history/data.

---

## Visual Understanding of Git

```
A---B---C main     \      D---E feature
```

After merge:

```
A---B---C------F main     \        /      D------E
```

# Bonus 

## Create GitHub Repo + Push Automatically

```
gh repo create myproject --public --source=. --remote=origin --push
```

DONE.

This single command:

- creates GitHub repository
- connects remote
- pushes code
- sets upstream tracking