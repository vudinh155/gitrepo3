# 11 - Git Cheatsheet: Bảng Tổng Hợp Lệnh

> **Mục tiêu:** Tra cứu nhanh các lệnh Git thường dùng

---

## 📑 Nội Dung

1. [Lệnh Cơ Bản](#1-lệnh-cơ-bản)
2. [Branch](#2-branch)
3. [Merge & Rebase](#3-merge--rebase)
4. [Remote](#4-remote)
5. [Undo & Fix](#5-undo--fix)
6. [Log & History](#6-log--history)
7. [Stash](#7-stash)
8. [Tags](#8-tags)
9. [Config](#9-config)
10. [Workflows](#10-workflows)

---

## 1. Lệnh Cơ Bản

### Setup

```bash
# Clone repo
git clone <url>
git clone <url> <folder-name>
git clone -b <branch> <url>

# Khởi tạo repo mới
git init
```

### Status & Diff

```bash
# Xem trạng thái
git status
git status -s                    # Short format

# Xem thay đổi
git diff                         # Working dir vs staged
git diff --staged                # Staged vs last commit
git diff HEAD                    # Working dir vs last commit
git diff <branch1>..<branch2>    # So sánh 2 branches
```

### Add & Commit

```bash
# Stage files
git add <file>
git add .                        # Tất cả files
git add -A                       # Tất cả (bao gồm deleted)
git add -p                       # Interactive staging

# Commit
git commit -m "message"
git commit -am "message"         # Add + commit (tracked files)
git commit --amend -m "new msg"  # Sửa commit cuối
git commit --amend --no-edit     # Thêm files vào commit cuối
```

---

## 2. Branch

### Quản lý branches

```bash
# Xem branches
git branch                       # Local
git branch -r                    # Remote
git branch -a                    # Tất cả
git branch -v                    # Với commit cuối

# Tạo branch
git branch <name>                # Tạo (không switch)
git checkout -b <name>           # Tạo + switch
git switch -c <name>             # Tạo + switch (Git 2.23+)

# Chuyển branch
git checkout <name>
git switch <name>                # Git 2.23+

# Đổi tên
git branch -m <old> <new>
git branch -m <new>              # Đổi tên branch hiện tại

# Xóa branch
git branch -d <name>             # Safe delete (đã merge)
git branch -D <name>             # Force delete
git push origin --delete <name>  # Xóa remote branch
```

### Tracking branches

```bash
# Set upstream
git branch --set-upstream-to=origin/<branch>
git push -u origin <branch>      # Push + set upstream

# Xem tracking
git branch -vv
```

---

## 3. Merge & Rebase

### Merge

```bash
# Merge
git merge <branch>
git merge --no-ff <branch>       # Tạo merge commit
git merge --squash <branch>      # Gộp thành 1 commit
git merge --abort                # Hủy merge

# Resolve conflict
git checkout --ours <file>       # Giữ version của mình
git checkout --theirs <file>     # Giữ version của họ
git add <file>                   # Mark as resolved
git merge --continue
```

### Rebase

```bash
# Rebase
git rebase <branch>
git rebase -i HEAD~N             # Interactive rebase N commits
git rebase --onto <new> <old>    # Rebase onto different base
git rebase --abort               # Hủy rebase
git rebase --continue            # Sau khi resolve conflict
git rebase --skip                # Skip commit hiện tại
```

### Interactive rebase options

```
pick   = giữ commit
reword = đổi message
edit   = dừng để edit
squash = gộp vào commit trước (giữ message)
fixup  = gộp vào commit trước (bỏ message)
drop   = xóa commit
```

---

## 4. Remote

### Quản lý remote

```bash
# Xem remotes
git remote -v

# Thêm remote
git remote add <name> <url>
git remote add origin <url>

# Đổi URL
git remote set-url origin <new-url>

# Xóa remote
git remote remove <name>
```

### Fetch, Pull, Push

```bash
# Fetch
git fetch                        # Fetch từ origin
git fetch <remote>               # Fetch từ remote cụ thể
git fetch --all                  # Fetch từ tất cả remotes
git fetch --prune                # Xóa stale remote-tracking

# Pull
git pull                         # Fetch + merge
git pull --rebase                # Fetch + rebase
git pull origin <branch>

# Push
git push
git push -u origin <branch>      # Push + set upstream
git push --force-with-lease      # Force push an toàn
git push --force                 # Force push (NGUY HIỂM!)
git push --all                   # Push tất cả branches
git push --tags                  # Push tất cả tags
```

---

## 5. Undo & Fix

### Restore (Git 2.23+)

```bash
# Undo working directory changes
git restore <file>               # Undo modifications
git restore .                    # Undo tất cả

# Unstage
git restore --staged <file>      # Unstage file
git restore --staged .           # Unstage tất cả

# Restore từ commit cụ thể
git restore --source=<commit> <file>
```

### Reset

```bash
# Soft reset (giữ staged)
git reset --soft HEAD~1

# Mixed reset (unstage, giữ working)
git reset HEAD~1
git reset --mixed HEAD~1

# Hard reset (XÓA tất cả)
git reset --hard HEAD~1
git reset --hard <commit>

# Reset 1 file
git reset HEAD <file>            # Unstage
git reset <commit> -- <file>     # Reset file về commit
```

### Revert

```bash
# Tạo revert commit
git revert HEAD                  # Revert commit cuối
git revert <commit>              # Revert commit cụ thể
git revert <commit> --no-commit  # Không tự commit
git revert -m 1 <merge-commit>   # Revert merge commit
```

### Recovery

```bash
# Xem reflog
git reflog
git reflog show <branch>

# Recovery từ reflog
git reset --hard HEAD@{N}
git checkout -b recovery HEAD@{N}

# Cherry-pick lost commits
git cherry-pick <commit>
```

---

## 6. Log & History

### Git log

```bash
# Basic log
git log
git log --oneline
git log -N                       # N commits gần nhất

# Graph
git log --graph
git log --graph --oneline --all

# Filter
git log --author="name"
git log --grep="keyword"
git log --since="2024-01-01"
git log --until="2024-12-31"
git log -- <file>                # Log của file

# Format
git log --pretty=format:"%h %an %s"
git log --stat                   # Thêm file stats
git log -p                       # Thêm diff
```

### Git show

```bash
git show                         # Show commit cuối
git show <commit>                # Show commit cụ thể
git show <commit>:<file>         # Show file trong commit
git show --stat <commit>         # Stats only
```

### Git blame

```bash
git blame <file>                 # Ai viết từng dòng
git blame -L 10,20 <file>        # Chỉ dòng 10-20
```

### Git diff

```bash
git diff <commit1> <commit2>
git diff <branch1>..<branch2>
git diff --name-only             # Chỉ tên files
git diff --stat                  # Stats
```

---

## 7. Stash

```bash
# Stash changes
git stash                        # Stash tracked files
git stash -u                     # Include untracked
git stash save "message"         # Với message
git stash push -m "message"      # Git 2.13+

# List stashes
git stash list

# Apply stash
git stash pop                    # Apply + delete
git stash apply                  # Apply, giữ stash
git stash apply stash@{N}        # Apply stash cụ thể

# Drop stash
git stash drop                   # Drop stash cuối
git stash drop stash@{N}         # Drop stash cụ thể
git stash clear                  # Xóa tất cả stashes

# Show stash
git stash show                   # Stats
git stash show -p                # Full diff
```

---

## 8. Tags

```bash
# List tags
git tag
git tag -l "v1.*"                # Filter pattern

# Tạo tag
git tag <name>                   # Lightweight tag
git tag -a <name> -m "message"   # Annotated tag
git tag -a <name> <commit>       # Tag commit cụ thể

# Push tags
git push origin <tag>            # Push 1 tag
git push origin --tags           # Push tất cả tags

# Delete tag
git tag -d <name>                # Local
git push origin --delete <name>  # Remote

# Show tag
git show <tag>

# Checkout tag
git checkout <tag>               # Detached HEAD
git checkout -b <branch> <tag>   # Tạo branch từ tag
```

---

## 9. Config

```bash
# Xem config
git config --list
git config user.name
git config user.email

# Set config
git config --global user.name "Name"
git config --global user.email "email@example.com"

# Editor
git config --global core.editor "code --wait"

# Default branch
git config --global init.defaultBranch main

# Aliases
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.lg "log --oneline --graph --all"

# Line endings
git config --global core.autocrlf true   # Windows
git config --global core.autocrlf input  # Mac/Linux
```

### Useful aliases

```bash
# Thêm vào ~/.gitconfig

[alias]
    co = checkout
    br = branch
    ci = commit
    st = status
    lg = log --oneline --graph --all
    last = log -1 HEAD
    unstage = reset HEAD --
    visual = !gitk
    amend = commit --amend --no-edit
    undo = reset --soft HEAD~1
    cleanup = "!git branch --merged | grep -v 'main\\|develop' | xargs git branch -d"
```

---

## 10. Workflows

### Daily workflow

```bash
# Sáng (trước khi code)
git checkout develop
git pull origin develop

# Bắt đầu task
git checkout -b feat/xxx

# Trong ngày
git add .
git commit -m "feat: description"

# Cuối ngày
git push -u origin feat/xxx
```

### PR workflow

```bash
# Cập nhật branch trước PR
git checkout develop
git pull origin develop
git checkout feat/xxx
git merge develop            # Resolve conflicts nếu có
git push

# Tạo PR qua UI

# Sau khi merge
git checkout develop
git pull origin develop
git branch -d feat/xxx
```

### Hotfix workflow

```bash
# Tạo hotfix từ main
git checkout main
git pull origin main
git checkout -b hotfix/xxx

# Fix bug
git add .
git commit -m "hotfix: fix xxx"
git push -u origin hotfix/xxx

# Tạo PR vào main (và develop)
```

### Rollback workflow

```bash
# Revert (an toàn, tạo commit mới)
git revert HEAD
git push

# Reset (NGUY HIỂM, thay đổi history)
git reset --hard <commit>
git push --force-with-lease
```

---

## 📋 Quick Commands

### Hàng ngày

| Việc | Lệnh |
|------|------|
| Xem trạng thái | `git status` |
| Pull code mới | `git pull` |
| Tạo branch | `git checkout -b feat/xxx` |
| Add tất cả | `git add .` |
| Commit | `git commit -m "message"` |
| Push | `git push` |
| Xem log | `git log --oneline` |

### Xử lý conflict

| Việc | Lệnh |
|------|------|
| Merge develop | `git merge develop` |
| Xem conflict | `git status` |
| Giữ code mình | `git checkout --ours <file>` |
| Giữ code họ | `git checkout --theirs <file>` |
| Đánh dấu resolved | `git add <file>` |
| Hoàn tất merge | `git commit` |
| Hủy merge | `git merge --abort` |

### Undo

| Việc | Lệnh |
|------|------|
| Undo file modifications | `git restore <file>` |
| Unstage file | `git restore --staged <file>` |
| Undo commit (giữ code) | `git reset --soft HEAD~1` |
| Undo commit (xóa code) | `git reset --hard HEAD~1` |
| Revert commit đã push | `git revert HEAD` |
| Recovery | `git reflog` → `git reset --hard HEAD@{N}` |

---

## 🔑 Key Shortcuts

```
HEAD     = Commit hiện tại
HEAD~1   = 1 commit trước
HEAD~N   = N commits trước
HEAD^    = Parent commit
@        = HEAD (viết tắt)
origin   = Remote mặc định
```

---

## ⚠️ Dangerous Commands

```bash
# CẨN THẬN với các lệnh sau:

git reset --hard          # Mất code không recover được
git push --force          # Ghi đè remote history
git clean -fd             # Xóa untracked files
git checkout -- .         # Undo tất cả changes
git branch -D             # Force delete branch
```

---

## 📌 Tips

```bash
# Xem lệnh đã chạy
history | grep git

# Xem help
git help <command>
git <command> --help

# Autocomplete (bash)
# Add to ~/.bashrc:
source /usr/share/bash-completion/completions/git

# GUI tools
gitk                      # Built-in GUI
git gui                   # Built-in GUI
```

---

*Git Training Documentation v1.0 - Cheatsheet*
