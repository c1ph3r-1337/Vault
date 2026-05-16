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
mkdir projectcd projectgit init
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


