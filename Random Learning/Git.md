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