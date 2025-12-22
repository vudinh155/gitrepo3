# 12 - Appendix: Tổng Hợp Chi Tiết Các Lệnh Git

> **Mục tiêu:** Tài liệu tham khảo đầy đủ về các lệnh Git phổ biến với giải thích chi tiết và ví dụ cụ thể

---

## 📑 Mục Lục

1. [Cấu Hình & Khởi Tạo](#1-cấu-hình--khởi-tạo)
2. [Làm Việc Với Files & Changes](#2-làm-việc-với-files--changes)
3. [Commit Management](#3-commit-management)
4. [Branch Operations](#4-branch-operations)
5. [Remote & Collaboration](#5-remote--collaboration)
6. [Merge & Rebase](#6-merge--rebase)
7. [Xem Lịch Sử & Thay Đổi](#7-xem-lịch-sử--thay-đổi)
8. [Undo & Recovery](#8-undo--recovery)
9. [Stash & Temporary Storage](#9-stash--temporary-storage)
10. [Tags & Releases](#10-tags--releases)
11. [Advanced Operations](#11-advanced-operations)
12. [Git Internals](#12-git-internals)

---

## 1. Cấu Hình & Khởi Tạo

### `git config`

**Tác dụng:** Cấu hình Git settings (user info, behavior, aliases)

**Cú pháp:**
```bash
git config [--global|--system|--local] <key> <value>
```

**Các mức config:**
- `--local`: Chỉ cho repository hiện tại (mặc định)
- `--global`: Cho tất cả repos của user
- `--system`: Cho tất cả users trên máy

**Ví dụ:**

```bash
# Cấu hình user info (bắt buộc)
git config --global user.name "Nguyen Van A"
git config --global user.email "nguyenvana@example.com"

# Cấu hình editor
git config --global core.editor "code --wait"        # VS Code
git config --global core.editor "vim"                # Vim

# Cấu hình default branch name
git config --global init.defaultBranch main

# Tạo alias cho lệnh
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'

# Xem tất cả config
git config --list
git config --list --show-origin                      # Xem cả file source

# Xem config cụ thể
git config user.name
git config --get user.email

# Xóa config
git config --unset user.name
git config --global --unset alias.st

# Line ending (quan trọng cho cross-platform)
git config --global core.autocrlf true               # Windows
git config --global core.autocrlf input              # Mac/Linux

# Color output
git config --global color.ui auto

# Merge/Diff tool
git config --global merge.tool vscode
git config --global diff.tool vscode
```

---

### `git init`

**Tác dụng:** Khởi tạo một Git repository mới

**Cú pháp:**
```bash
git init [directory]
git init --bare [directory]
```

**Ví dụ:**

```bash
# Khởi tạo repo trong thư mục hiện tại
git init

# Khởi tạo repo trong thư mục mới
git init my-project
cd my-project

# Khởi tạo bare repository (dùng cho remote server)
git init --bare project.git

# Khởi tạo với branch name cụ thể
git init -b main

# Sau khi init, thường làm:
git add .
git commit -m "Initial commit"
git remote add origin <url>
git push -u origin main
```

---

### `git clone`

**Tác dụng:** Sao chép một repository từ remote về local

**Cú pháp:**
```bash
git clone <url> [directory]
```

**Ví dụ:**

```bash
# Clone repo (tên folder = tên repo)
git clone https://github.com/user/repo.git

# Clone vào folder cụ thể
git clone https://github.com/user/repo.git my-folder

# Clone branch cụ thể
git clone -b develop https://github.com/user/repo.git

# Clone với depth (shallow clone, nhanh hơn)
git clone --depth 1 https://github.com/user/repo.git

# Clone single branch only
git clone --single-branch --branch main https://github.com/user/repo.git

# Clone qua SSH
git clone git@github.com:user/repo.git

# Clone và rename origin
git clone -o upstream https://github.com/user/repo.git
```

---

## 2. Làm Việc Với Files & Changes

### `git status`

**Tác dụng:** Xem trạng thái của working directory và staging area

**Cú pháp:**
```bash
git status [options]
```

**Ví dụ:**

```bash
# Xem trạng thái đầy đủ
git status

# Output ngắn gọn
git status -s
git status --short

# Output mẫu:
# ?? = untracked
# A  = staged (new file)
# M  = modified
# D  = deleted
# MM = modified, staged, và modified again

# Xem thêm untracked files trong subfolders
git status -u

# Xem branch info
git status -b
```

**Output giải thích:**
```
On branch main                          # Branch hiện tại
Your branch is up to date with...      # So với remote

Changes to be committed:                # Đã staged, sẵn sàng commit
  (use "git restore --staged..." )
        new file:   file1.txt

Changes not staged for commit:          # Modified nhưng chưa stage
  (use "git add <file>...")
        modified:   file2.txt

Untracked files:                        # Files chưa được Git quản lý
  (use "git add <file>...")
        file3.txt
```

---

### `git add`

**Tác dụng:** Thêm changes vào staging area (chuẩn bị commit)

**Cú pháp:**
```bash
git add <file|pattern>
```

**Ví dụ:**

```bash
# Add file cụ thể
git add file.txt

# Add nhiều files
git add file1.txt file2.txt file3.txt

# Add tất cả files trong thư mục hiện tại
git add .

# Add tất cả files (kể cả deleted files)
git add -A
git add --all

# Add tất cả files với extension cụ thể
git add *.js
git add src/**/*.ts

# Add chỉ modified files (không add new/deleted)
git add -u
git add --update

# Interactive staging (chọn từng phần)
git add -p file.txt
git add --patch file.txt

# Trong interactive mode:
# y = yes, stage this hunk
# n = no, don't stage
# s = split into smaller hunks
# e = manually edit the hunk
# q = quit

# Add folder và tất cả contents
git add src/

# Dry-run (xem sẽ add files gì)
git add -n .
git add --dry-run .
```

---

### `git diff`

**Tác dụng:** Xem sự khác biệt giữa các versions của code

**Cú pháp:**
```bash
git diff [options] [commit] [commit] [--] [path]
```

**Ví dụ:**

```bash
# Working directory vs Staging area (changes chưa stage)
git diff

# Staging area vs Last commit (changes đã stage)
git diff --staged
git diff --cached

# Working directory vs Last commit (tất cả changes)
git diff HEAD

# So sánh với commit cụ thể
git diff abc123
git diff HEAD~1                  # 1 commit trước
git diff HEAD~3                  # 3 commits trước

# So sánh 2 commits
git diff commit1 commit2
git diff abc123 def456

# So sánh 2 branches
git diff main develop
git diff main..develop           # Tương đương
git diff main...develop          # Changes từ common ancestor

# Chỉ xem file nào thay đổi
git diff --name-only
git diff --name-status           # Với status (M, A, D)

# Xem stats
git diff --stat

# Diff của file cụ thể
git diff file.txt
git diff main develop -- file.txt

# Ignore whitespace
git diff -w
git diff --ignore-all-space

# Word diff (highlight từng word thay vì dòng)
git diff --word-diff

# Diff với external tool
git difftool
git difftool main develop
```

---

### `git rm`

**Tác dụng:** Xóa files khỏi working directory và staging area

**Cú pháp:**
```bash
git rm <file>
```

**Ví dụ:**

```bash
# Xóa file (khỏi working dir + stage)
git rm file.txt
# Sau đó cần commit:
git commit -m "Remove file.txt"

# Chỉ xóa khỏi Git, giữ file trên disk
git rm --cached file.txt

# Xóa nhiều files
git rm file1.txt file2.txt

# Xóa với pattern
git rm '*.log'
git rm -r logs/

# Force xóa (nếu file đã modified)
git rm -f file.txt

# Dry-run
git rm -n '*.log'

# Xóa folder và contents
git rm -r folder/
```

---

### `git mv`

**Tác dụng:** Đổi tên hoặc di chuyển files

**Cú pháp:**
```bash
git mv <source> <destination>
```

**Ví dụ:**

```bash
# Đổi tên file
git mv old-name.txt new-name.txt

# Di chuyển file sang folder khác
git mv file.txt src/

# Tương đương với:
mv old-name.txt new-name.txt
git rm old-name.txt
git add new-name.txt

# Force overwrite
git mv -f source.txt destination.txt
```

---

### `git restore`

**Tác dụng:** Restore working tree files (Git 2.23+, thay thế cho git checkout)

**Cú pháp:**
```bash
git restore [options] <file>
```

**Ví dụ:**

```bash
# Undo changes trong working directory
git restore file.txt             # Restore 1 file
git restore .                    # Restore tất cả files

# Unstage file (giữ changes trong working dir)
git restore --staged file.txt
git restore --staged .

# Restore từ commit cụ thể
git restore --source=HEAD~1 file.txt
git restore --source=main file.txt

# Restore và unstage cùng lúc
git restore --staged --worktree file.txt
git restore -SW file.txt         # Short form

# Interactive restore
git restore -p file.txt
```

---

## 3. Commit Management

### `git commit`

**Tác dụng:** Lưu changes từ staging area vào repository history

**Cú pháp:**
```bash
git commit [options]
```

**Ví dụ:**

```bash
# Commit với message
git commit -m "Add user authentication feature"

# Commit với message nhiều dòng
git commit -m "Fix login bug" -m "- Fixed password validation
- Updated error messages
- Added unit tests"

# Add tất cả tracked files và commit
git commit -am "Update documentation"

# Commit và mở editor để viết message
git commit

# Amend commit cuối (thêm changes vào commit trước)
git commit --amend -m "Updated message"

# Amend không đổi message
git commit --amend --no-edit

# Empty commit (không có changes)
git commit --allow-empty -m "Trigger CI"

# Commit với author khác
git commit --author="John Doe <john@example.com>" -m "Message"

# Commit với date cụ thể
git commit --date="2024-01-01" -m "Message"

# Sign commit
git commit -S -m "Signed commit"

# Verbose (show diff trong commit message editor)
git commit -v

# Interactive commit (chọn changes)
git commit -p
```

**Conventional Commits:**
```bash
git commit -m "feat: add user login"
git commit -m "fix: resolve memory leak in parser"
git commit -m "docs: update README with setup instructions"
git commit -m "style: format code with prettier"
git commit -m "refactor: extract validation logic"
git commit -m "test: add unit tests for auth service"
git commit -m "chore: update dependencies"
git commit -m "perf: improve database query performance"
```

---

### `git log`

**Tác dụng:** Xem commit history

**Cú pháp:**
```bash
git log [options] [branch|commit]
```

**Ví dụ:**

```bash
# Log đầy đủ
git log

# Log ngắn gọn (1 dòng/commit)
git log --oneline

# N commits gần nhất
git log -5
git log -n 5

# Log với graph
git log --graph
git log --graph --oneline --all
git log --graph --oneline --decorate --all

# Filter theo author
git log --author="John"
git log --author="john@example.com"

# Filter theo message
git log --grep="fix"
git log --grep="bug" --grep="error"  # OR
git log --grep="bug" --and --grep="critical"  # AND

# Filter theo date
git log --since="2024-01-01"
git log --after="2024-01-01"
git log --until="2024-12-31"
git log --before="2024-12-31"
git log --since="2 weeks ago"
git log --since="yesterday"

# Log của file cụ thể
git log file.txt
git log -- path/to/file.txt
git log -p file.txt              # Với diff

# Log với stats
git log --stat
git log --shortstat

# Log với full diff
git log -p
git log --patch

# Custom format
git log --pretty=format:"%h - %an, %ar : %s"
# %h = short hash
# %H = full hash
# %an = author name
# %ae = author email
# %ar = author date (relative)
# %ad = author date
# %s = subject (message)

# Show merge commits only
git log --merges

# Exclude merge commits
git log --no-merges

# Log của branch cụ thể
git log main
git log origin/develop

# Log giữa 2 commits
git log abc123..def456

# Log giữa 2 branches
git log main..develop            # Commits in develop not in main
git log main...develop           # Commits in either, not both

# Follow file qua renames
git log --follow file.txt

# Search trong code changes
git log -S "function_name"       # Tìm commits thêm/xóa text
git log -G "regex_pattern"       # Tìm theo regex

# First parent only (skip merge details)
git log --first-parent
```

---

### `git show`

**Tác dụng:** Hiển thị chi tiết của commits, tags, hoặc objects

**Cú pháp:**
```bash
git show [object]
```

**Ví dụ:**

```bash
# Show commit cuối
git show
git show HEAD

# Show commit cụ thể
git show abc123
git show HEAD~1                  # 1 commit trước
git show main

# Show file trong commit cụ thể
git show abc123:path/to/file.txt

# Show chỉ stats
git show --stat abc123

# Show chỉ tên files
git show --name-only abc123

# Show tag
git show v1.0.0

# Show tree object
git show abc123^{tree}

# Show multiple commits
git show HEAD~3 HEAD~2 HEAD~1

# Show với format custom
git show --pretty=format:"%h %s" abc123
```

---

## 4. Branch Operations

### `git branch`

**Tác dụng:** Quản lý branches (tạo, xem, xóa, đổi tên)

**Cú pháp:**
```bash
git branch [options] [branch-name]
```

**Ví dụ:**

```bash
# Xem tất cả local branches
git branch

# Xem remote branches
git branch -r

# Xem tất cả branches (local + remote)
git branch -a

# Xem với commit info
git branch -v
git branch -vv                   # Với tracking info

# Tạo branch mới (không switch)
git branch feature/login
git branch hotfix/bug-123

# Tạo branch từ commit cụ thể
git branch new-branch abc123
git branch new-branch HEAD~3

# Đổi tên branch
git branch -m old-name new-name

# Đổi tên branch hiện tại
git branch -m new-name

# Xóa branch (safe - chỉ xóa nếu đã merge)
git branch -d feature/login

# Force xóa branch (chưa merge cũng xóa)
git branch -D feature/login

# Xóa remote branch
git push origin --delete feature/login

# Set upstream cho branch
git branch --set-upstream-to=origin/main
git branch -u origin/main

# Xem branches đã merge vào branch hiện tại
git branch --merged

# Xem branches chưa merge
git branch --no-merged

# Xem branches chứa commit cụ thể
git branch --contains abc123

# Copy branch
git branch new-branch existing-branch

# Xem branch creation date
git for-each-ref --sort=committerdate refs/heads/ --format='%(committerdate:short) %(refname:short)'
```

---

### `git checkout`

**Tác dụng:** Switch branches hoặc restore files (đa năng, phức tạp)

**Lưu ý:** Từ Git 2.23+, khuyến khích dùng `git switch` và `git restore` thay thế

**Cú pháp:**
```bash
git checkout [branch|commit|file]
```

**Ví dụ:**

```bash
# Switch branch
git checkout main
git checkout develop

# Tạo và switch branch mới
git checkout -b feature/new-feature

# Tạo branch từ commit cụ thể
git checkout -b hotfix abc123

# Checkout commit (detached HEAD state)
git checkout abc123

# Checkout tag
git checkout v1.0.0

# Tạo branch từ tag
git checkout -b version-1.0 v1.0.0

# Restore file từ staging area
git checkout -- file.txt

# Restore file từ commit cụ thể
git checkout abc123 -- file.txt

# Restore tất cả files
git checkout -- .

# Checkout remote branch
git checkout -b feature origin/feature
git checkout --track origin/feature  # Tự động set tracking

# Force checkout (discard local changes)
git checkout -f main

# Checkout với conflict resolution
git checkout --ours file.txt     # Trong merge, giữ version của mình
git checkout --theirs file.txt   # Giữ version của branch khác
```

---

### `git switch`

**Tác dụng:** Switch branches (Git 2.23+, safer alternative to checkout)

**Cú pháp:**
```bash
git switch [branch]
```

**Ví dụ:**

```bash
# Switch branch
git switch main
git switch develop

# Tạo và switch branch mới
git switch -c feature/login
git switch --create hotfix/bug

# Tạo branch từ commit/branch khác
git switch -c new-branch main
git switch -c hotfix abc123

# Switch về previous branch
git switch -

# Switch remote branch (tạo local tracking branch)
git switch feature
# Git tự tìm origin/feature và tạo local branch

# Force switch (discard changes)
git switch -f main
git switch --force main

# Detach HEAD
git switch --detach abc123
git switch --detach HEAD~3
```

---

### `git merge`

**Tác dụng:** Merge changes từ branch khác vào branch hiện tại

**Cú pháp:**
```bash
git merge [options] <branch>
```

**Ví dụ:**

```bash
# Merge branch vào current branch
git checkout main
git merge feature/login

# Fast-forward merge (nếu có thể)
git merge feature/login          # Mặc định

# Luôn tạo merge commit (no fast-forward)
git merge --no-ff feature/login

# Squash merge (gộp tất cả commits thành 1)
git merge --squash feature/login
git commit -m "Merge feature: login"

# Merge với message cụ thể
git merge -m "Merge feature X" feature/login

# Merge và auto commit (mặc định)
git merge feature/login

# Merge nhưng không auto commit
git merge --no-commit feature/login

# Abort merge khi có conflict
git merge --abort

# Continue merge sau khi resolve conflict
git merge --continue

# Strategy options
git merge -X theirs feature/login    # Ưu tiên changes của feature
git merge -X ours feature/login      # Ưu tiên changes của current

# Merge commit cụ thể
git merge abc123

# Merge remote branch
git merge origin/main

# Verify merge before doing it
git merge --no-commit --no-ff feature/login
git diff --cached
git merge --abort  # If not satisfied
```

**Xử lý conflict:**
```bash
# 1. Merge gây conflict
git merge feature/login
# CONFLICT (content): Merge conflict in file.txt

# 2. Xem files bị conflict
git status

# 3a. Resolve manually (edit files, remove markers)
# <<<<<<< HEAD
# current changes
# =======
# incoming changes
# >>>>>>> feature/login

# 3b. Hoặc choose version
git checkout --ours file.txt     # Giữ current
git checkout --theirs file.txt   # Giữ incoming

# 4. Mark as resolved
git add file.txt

# 5. Complete merge
git commit

# Hoặc abort
git merge --abort
```

---

## 5. Remote & Collaboration

### `git remote`

**Tác dụng:** Quản lý remote repositories

**Cú pháp:**
```bash
git remote [subcommand] [options]
```

**Ví dụ:**

```bash
# Xem list remotes
git remote
git remote -v                    # Với URLs

# Thêm remote
git remote add origin https://github.com/user/repo.git
git remote add upstream https://github.com/original/repo.git

# Xem chi tiết remote
git remote show origin

# Đổi URL của remote
git remote set-url origin https://github.com/user/new-repo.git
git remote set-url origin git@github.com:user/repo.git  # HTTPS -> SSH

# Đổi tên remote
git remote rename origin upstream

# Xóa remote
git remote remove origin
git remote rm origin

# Xem URLs
git remote get-url origin
git remote get-url --all origin

# Prune stale remote branches
git remote prune origin
git remote prune origin --dry-run  # Preview

# Update remote references
git remote update
git remote update origin
```

---

### `git fetch`

**Tác dụng:** Download objects và refs từ remote repository (không merge)

**Cú pháp:**
```bash
git fetch [remote] [branch]
```

**Ví dụ:**

```bash
# Fetch từ origin
git fetch

# Fetch từ remote cụ thể
git fetch origin
git fetch upstream

# Fetch branch cụ thể
git fetch origin main
git fetch origin feature/login

# Fetch tất cả remotes
git fetch --all

# Fetch và prune deleted remote branches
git fetch --prune
git fetch -p

# Fetch tất cả branches và tags
git fetch --all --tags

# Fetch với depth (shallow fetch)
git fetch --depth=1

# Unshallow (convert shallow to full)
git fetch --unshallow

# Fetch chỉ tags
git fetch --tags

# Dry run
git fetch --dry-run

# Sau khi fetch, xem changes:
git log HEAD..origin/main        # Commits in origin/main not in HEAD
git diff HEAD origin/main        # Changes
```

---

### `git pull`

**Tác dụng:** Fetch + merge (hoặc rebase) từ remote

**Cú pháp:**
```bash
git pull [remote] [branch]
```

**Ví dụ:**

```bash
# Pull từ tracked remote branch
git pull

# Pull từ origin main
git pull origin main

# Pull với rebase thay vì merge
git pull --rebase
git pull --rebase origin main

# Pull tất cả branches
git pull --all

# Pull và prune
git pull --prune

# Pull nhưng không commit merge
git pull --no-commit

# Pull với fast-forward only
git pull --ff-only

# Set default pull strategy
git config pull.rebase false     # Merge (default)
git config pull.rebase true      # Rebase
git config pull.ff only          # Fast-forward only

# Pull với auto-stash (stash changes trước khi pull)
git pull --autostash

# Pull từ upstream
git pull upstream main
```

**Pull workflow:**
```bash
# Best practice:
git fetch origin
git log HEAD..origin/main        # Review changes
git merge origin/main            # Or: git rebase origin/main

# Hoặc ngắn gọn:
git pull                         # Nếu đã review trước đó
```

---

### `git push`

**Tác dụng:** Upload local commits lên remote repository

**Cú pháp:**
```bash
git push [remote] [branch]
```

**Ví dụ:**

```bash
# Push current branch lên tracked remote
git push

# Push lên origin main
git push origin main

# Push và set upstream
git push -u origin feature/login
git push --set-upstream origin feature/login

# Push tất cả branches
git push --all
git push --all origin

# Push tags
git push --tags
git push origin --tags

# Push tag cụ thể
git push origin v1.0.0

# Delete remote branch
git push origin --delete feature/old
git push origin :feature/old     # Old syntax

# Delete remote tag
git push origin --delete v1.0.0
git push origin :refs/tags/v1.0.0

# Force push (NGUY HIỂM!)
git push --force
git push -f

# Force push an toàn hơn (reject nếu remote có commits mới)
git push --force-with-lease
git push --force-with-lease origin main

# Dry run (xem sẽ push gì)
git push --dry-run

# Push với different remote name
git push origin HEAD:main        # Push current branch to main

# Push empty commit để trigger CI
git commit --allow-empty -m "Trigger CI"
git push

# Mirror push (backup)
git push --mirror https://backup-repo.git
```

**Push scenarios:**
```bash
# 1. First time push new branch
git checkout -b feature/login
git add .
git commit -m "Add login"
git push -u origin feature/login

# 2. Push existing branch
git add .
git commit -m "Update"
git push

# 3. Push sau khi rebase (force push)
git rebase main
git push --force-with-lease

# 4. Push nhiều branches cùng lúc
git push origin main develop feature/x

# 5. Push all local branches to remote
git push --all origin
```

---

## 6. Merge & Rebase

### `git rebase`

**Tác dụng:** Reapply commits on top of another base (linear history)

**Cú pháp:**
```bash
git rebase [options] [branch]
```

**Ví dụ:**

```bash
# Rebase current branch lên main
git checkout feature/login
git rebase main

# Interactive rebase (edit N commits)
git rebase -i HEAD~5
git rebase -i abc123             # Từ commit cụ thể

# Rebase lên remote branch
git rebase origin/main

# Rebase với conflict resolution
git rebase main
# ... resolve conflicts ...
git add file.txt
git rebase --continue

# Skip commit hiện tại
git rebase --skip

# Abort rebase
git rebase --abort

# Rebase onto different base
git rebase --onto main feature develop
# Rebase commits từ develop (after feature) onto main

# Preserve merge commits
git rebase -p main
git rebase --preserve-merges main

# Autosquash (dùng với fixup/squash commits)
git rebase -i --autosquash main

# Exec command sau mỗi commit
git rebase -i --exec "npm test" main
```

**Interactive rebase commands:**
```
pick   = use commit
reword = use commit, but edit message
edit   = use commit, but stop for amending
squash = use commit, but meld into previous commit (keep message)
fixup  = like squash, but discard this commit's message
exec   = run command (the rest of the line) using shell
drop   = remove commit
```

**Ví dụ interactive rebase:**
```bash
git rebase -i HEAD~3

# Editor mở:
pick abc123 Add feature
pick def456 Fix typo
pick ghi789 Update docs

# Thay đổi thành:
pick abc123 Add feature
fixup def456 Fix typo
reword ghi789 Update documentation

# Save và close -> Git sẽ:
# 1. Gộp def456 vào abc123
# 2. Cho phép edit message của ghi789
```

---

### `git cherry-pick`

**Tác dụng:** Apply changes từ commits cụ thể vào branch hiện tại

**Cú pháp:**
```bash
git cherry-pick [options] <commit>
```

**Ví dụ:**

```bash
# Cherry-pick 1 commit
git cherry-pick abc123

# Cherry-pick nhiều commits
git cherry-pick abc123 def456 ghi789

# Cherry-pick range
git cherry-pick abc123..def456
git cherry-pick abc123^..def456  # Include abc123

# Cherry-pick nhưng không commit
git cherry-pick --no-commit abc123
git cherry-pick -n abc123

# Edit commit message
git cherry-pick --edit abc123
git cherry-pick -e abc123

# Continue sau khi resolve conflict
git cherry-pick --continue

# Abort
git cherry-pick --abort

# Skip
git cherry-pick --skip

# Sign off
git cherry-pick -s abc123
git cherry-pick --signoff abc123
```

**Use cases:**
```bash
# 1. Đưa hotfix từ main về develop
git checkout develop
git cherry-pick <hotfix-commit-hash>

# 2. Copy feature commit sang branch khác
git checkout release/v1.0
git cherry-pick <feature-commit>

# 3. Undo accidental commit to wrong branch
git checkout correct-branch
git cherry-pick <commit-from-wrong-branch>
git checkout wrong-branch
git reset --hard HEAD~1
```

---

## 7. Xem Lịch Sử & Thay Đổi

### `git blame`

**Tác dụng:** Xem ai viết từng dòng code trong file (debug, audit)

**Cú pháp:**
```bash
git blame [options] <file>
```

**Ví dụ:**

```bash
# Blame toàn bộ file
git blame file.txt

# Chỉ xem dòng 10-20
git blame -L 10,20 file.txt
git blame -L 10,+10 file.txt    # 10 dòng từ dòng 10

# Show email thay vì name
git blame -e file.txt

# Format ngắn
git blame -s file.txt

# Ignore whitespace changes
git blame -w file.txt

# Xem từ commit cụ thể trở về trước
git blame abc123 file.txt

# Follow file qua renames
git blame -C file.txt
git blame -C -C file.txt         # Detect copies aggressively

# Show commit info
git blame --show-name file.txt
git blame --show-email file.txt

# With color
git blame --color-lines file.txt
```

---

### `git reflog`

**Tác dụng:** Xem lịch sử của HEAD và branch references (recovery tool)

**Cú pháp:**
```bash
git reflog [subcommand] [options]
```

**Ví dụ:**

```bash
# Xem reflog của HEAD
git reflog
git reflog show HEAD

# Xem reflog của branch cụ thể
git reflog show main
git reflog show feature/login

# Với timestamps
git reflog --date=iso

# Relative dates
git reflog --relative-date

# Limit số entries
git reflog -10

# Format custom
git reflog --format="%h %gd %gs"

# Expire old reflog entries
git reflog expire --expire=30.days refs/heads/main
git reflog expire --all --expire=90.days

# Delete reflog
git reflog delete HEAD@{2}
```

**Recovery với reflog:**
```bash
# Scenario: Accidentally reset --hard
git reset --hard HEAD~5          # Oops! Lost 5 commits

# Recovery:
git reflog                       # Find the commit before reset
# abc123 HEAD@{1}: reset: moving to HEAD~5
# def456 HEAD@{2}: commit: Important feature

git reset --hard def456          # Restore to before reset
# Or:
git reset --hard HEAD@{2}

# Scenario: Deleted branch
git branch -D feature/login      # Oops!

# Recovery:
git reflog                       # Find last commit of deleted branch
git checkout -b feature/login abc123
```

---

### `git bisect`

**Tác dụng:** Binary search để tìm commit gây ra bug

**Cú pháp:**
```bash
git bisect <subcommand>
```

**Ví dụ:**

```bash
# Bắt đầu bisect
git bisect start

# Mark current commit as bad
git bisect bad

# Mark commit tốt (biết chắc không có bug)
git bisect good abc123

# Git sẽ checkout commit ở giữa, test và mark:
# If bug exists:
git bisect bad
# If bug doesn't exist:
git bisect good

# Lặp lại cho đến khi tìm được commit gây bug

# Kết thúc bisect
git bisect reset

# Automated bisect với script
git bisect start
git bisect bad
git bisect good abc123
git bisect run npm test          # Run tests automatically

# Skip commit (nếu không build được)
git bisect skip

# Visualize
git bisect visualize
git bisect view

# Bisect log
git bisect log
git bisect log > bisect.log

# Replay bisect
git bisect replay bisect.log
```

**Full workflow:**
```bash
# 1. Start bisect
git bisect start
git bisect bad                   # Current is bad
git bisect good v1.0.0           # v1.0.0 was good

# 2. Git checks out middle commit
# Test the code...

# 3. Mark as good or bad
git bisect good                  # If works
# Or:
git bisect bad                   # If broken

# 4. Repeat until found
# Git will say:
# abc123 is the first bad commit

# 5. Reset
git bisect reset
git show abc123                  # Examine the bad commit
```

---

### `git grep`

**Tác dụng:** Tìm kiếm text trong repository (nhanh hơn grep thông thường)

**Cú pháp:**
```bash
git grep [options] <pattern>
```

**Ví dụ:**

```bash
# Tìm text trong working directory
git grep "function_name"

# Tìm với case insensitive
git grep -i "todo"

# Tìm whole word only
git grep -w "user"

# Show line numbers
git grep -n "function"

# Show file names only
git grep -l "pattern"

# Count matches
git grep -c "TODO"

# Tìm trong commit cụ thể
git grep "pattern" abc123

# Tìm trong branch
git grep "pattern" main

# Tìm trong tag
git grep "pattern" v1.0.0

# Tìm với regex
git grep -E "function|class"
git grep -P "\w+@\w+\.\w+"       # Perl regex

# Tìm trong file types cụ thể
git grep "pattern" -- "*.js"
git grep "pattern" -- "*.ts" "*.tsx"

# Exclude files
git grep "pattern" -- . ":(exclude)*.min.js"

# Show context (before/after)
git grep -C 2 "pattern"          # 2 lines before & after
git grep -B 3 "pattern"          # 3 lines before
git grep -A 3 "pattern"          # 3 lines after

# Combine with other tools
git grep -l "TODO" | xargs wc -l # Count files with TODO

# And/Or patterns
git grep -e "pattern1" --and -e "pattern2"
git grep -e "pattern1" --or -e "pattern2"
```

---

## 8. Undo & Recovery

### `git reset`

**Tác dụng:** Reset HEAD về commit cụ thể (undo commits)

**Cú pháp:**
```bash
git reset [mode] [commit]
```

**3 modes chính:**
- `--soft`: Giữ staged changes và working directory
- `--mixed` (default): Giữ working directory, unstage changes
- `--hard`: XÓA tất cả changes

**Ví dụ:**

```bash
# Soft reset (giữ changes staged)
git reset --soft HEAD~1
# Use case: Sửa commit message hoặc add thêm files

# Mixed reset (unstage changes, giữ trong working dir)
git reset HEAD~1
git reset --mixed HEAD~1
# Use case: Chia 1 commit lớn thành nhiều commits nhỏ

# Hard reset (XÓA tất cả changes)
git reset --hard HEAD~1
git reset --hard abc123
# CẢNH BÁO: Mất code, chỉ dùng khi chắc chắn!

# Reset về commit cụ thể
git reset abc123
git reset --hard origin/main

# Unstage file (không đụng working directory)
git reset HEAD file.txt
git reset file.txt

# Reset 1 file về commit cụ thể
git reset abc123 -- file.txt

# Reset về N commits trước
git reset HEAD~3
git reset --hard HEAD~5

# Reset về remote state
git reset --hard origin/main
```

**Scenarios:**

```bash
# Scenario 1: Sửa commit message
git reset --soft HEAD~1
git commit -m "New message"

# Scenario 2: Add quên file vào commit
git reset --soft HEAD~1
git add forgotten-file.txt
git commit -m "Original message"

# Scenario 3: Chia commit lớn
git reset HEAD~1                 # Mixed reset
git add file1.txt
git commit -m "Part 1"
git add file2.txt
git commit -m "Part 2"

# Scenario 4: Undo tất cả local changes
git reset --hard origin/main

# Scenario 5: Unstage all
git reset
```

---

### `git revert`

**Tác dụng:** Tạo commit mới để undo commit cũ (safe, không thay đổi history)

**Cú pháp:**
```bash
git revert [options] <commit>
```

**Ví dụ:**

```bash
# Revert commit cuối
git revert HEAD

# Revert commit cụ thể
git revert abc123

# Revert nhiều commits
git revert abc123 def456

# Revert range
git revert abc123..def456

# Revert nhưng không auto commit
git revert --no-commit HEAD
git revert -n abc123

# Edit message
git revert --edit abc123
git revert -e abc123

# No edit (use default message)
git revert --no-edit abc123

# Continue sau khi resolve conflict
git revert --continue

# Abort revert
git revert --abort

# Revert merge commit
git revert -m 1 <merge-commit>
# -m 1 = giữ parent 1 (usually main branch)
# -m 2 = giữ parent 2 (feature branch)

# Strategy
git revert -X theirs abc123
```

**Reset vs Revert:**

```bash
# Reset: Thay đổi history (local only, chưa push)
git reset --hard HEAD~1
# ✓ Clean history
# ✗ Nguy hiểm nếu đã push

# Revert: Tạo commit mới (safe, có thể dùng sau khi push)
git revert HEAD
# ✓ Safe, không thay đổi history
# ✓ Có thể push ngay
# ✗ Tạo thêm commit (messy history)

# Best practices:
# - Local commits chưa push: dùng reset
# - Commits đã push: dùng revert
```

---

### `git clean`

**Tác dụng:** Xóa untracked files khỏi working directory

**Cú pháp:**
```bash
git clean [options]
```

**Ví dụ:**

```bash
# Dry-run (xem sẽ xóa gì)
git clean -n
git clean --dry-run

# Xóa untracked files
git clean -f
git clean --force

# Xóa cả directories
git clean -fd

# Xóa cả ignored files (.gitignore)
git clean -fX

# Xóa tất cả (untracked + ignored)
git clean -fx

# Interactive mode
git clean -i

# Xóa trong directory cụ thể
git clean -fd src/

# Exclude patterns
git clean -f -e "*.log"          # Giữ lại .log files
```

**CẢNH BÁO:** `git clean` không thể undo! Luôn dùng `-n` trước.

---

## 9. Stash & Temporary Storage

### `git stash`

**Tác dụng:** Lưu tạm changes chưa commit để switch branch hoặc làm việc khác

**Cú pháp:**
```bash
git stash [subcommand] [options]
```

**Ví dụ:**

```bash
# Stash tracked files
git stash
git stash push

# Stash với message
git stash save "Work in progress"
git stash push -m "WIP: feature X"

# Stash cả untracked files
git stash -u
git stash --include-untracked

# Stash cả ignored files
git stash -a
git stash --all

# Stash file cụ thể
git stash push -m "message" path/to/file.txt
git stash push src/*.js

# List stashes
git stash list
# Output:
# stash@{0}: WIP on main: abc123 Latest commit
# stash@{1}: On feature: def456 Other work

# Show stash content
git stash show                   # Stats of latest stash
git stash show stash@{1}         # Specific stash
git stash show -p                # Full diff
git stash show -p stash@{1}

# Apply stash
git stash pop                    # Apply latest + delete
git stash pop stash@{1}          # Apply specific + delete

git stash apply                  # Apply latest, keep stash
git stash apply stash@{1}        # Apply specific, keep stash

# Drop stash
git stash drop                   # Drop latest
git stash drop stash@{1}         # Drop specific

# Clear all stashes
git stash clear

# Create branch from stash
git stash branch new-branch-name
git stash branch new-branch stash@{1}
```

**Stash workflow:**

```bash
# Scenario 1: Cần switch branch nhưng chưa xong
git stash -u
git checkout other-branch
# ... work ...
git checkout original-branch
git stash pop

# Scenario 2: Apply stash trên nhiều branches
git stash
git checkout branch1
git stash apply               # Apply, keep stash
git checkout branch2
git stash apply               # Apply again
git stash drop                # Done, delete stash

# Scenario 3: Tạo branch từ stashed work
git stash
git stash branch feature/new-idea
# Tạo branch mới + apply stash + drop stash
```

---

## 10. Tags & Releases

### `git tag`

**Tác dụng:** Đánh dấu commits quan trọng (versions, releases)

**Có 2 loại:**
- **Lightweight tag**: Chỉ là pointer đến commit
- **Annotated tag**: Object đầy đủ với message, tagger info, date

**Cú pháp:**
```bash
git tag [options] <tag-name>
```

**Ví dụ:**

```bash
# List tất cả tags
git tag
git tag -l
git tag --list

# Filter tags
git tag -l "v1.*"
git tag -l "v2.0.*"

# Tạo lightweight tag
git tag v1.0.0

# Tạo annotated tag (recommended)
git tag -a v1.0.0 -m "Release version 1.0.0"
git tag --annotate v1.0.0 -m "Message"

# Tag commit cụ thể
git tag -a v1.0.0 abc123 -m "Message"

# Tag với multi-line message
git tag -a v1.0.0 -m "Version 1.0.0

Features:
- Feature A
- Feature B

Bug fixes:
- Fix X
- Fix Y"

# Show tag info
git show v1.0.0

# Show tag list với annotations
git tag -n                       # First line
git tag -n5                      # First 5 lines

# Push tag lên remote
git push origin v1.0.0

# Push tất cả tags
git push origin --tags
git push --tags

# Delete local tag
git tag -d v1.0.0
git tag --delete v1.0.0

# Delete remote tag
git push origin --delete v1.0.0
git push origin :refs/tags/v1.0.0

# Checkout tag (detached HEAD)
git checkout v1.0.0

# Create branch from tag
git checkout -b version-1.0 v1.0.0

# Replace existing tag
git tag -f v1.0.0
git tag -fa v1.0.0 -m "Updated message"

# Verify tag (if signed)
git tag -v v1.0.0

# Sign tag (GPG)
git tag -s v1.0.0 -m "Signed release"
```

**Semantic Versioning (recommended):**
```bash
# Format: vMAJOR.MINOR.PATCH

git tag -a v1.0.0 -m "Initial release"
git tag -a v1.0.1 -m "Bug fixes"
git tag -a v1.1.0 -m "New features, backward compatible"
git tag -a v2.0.0 -m "Breaking changes"

# Pre-release
git tag -a v2.0.0-alpha -m "Alpha release"
git tag -a v2.0.0-beta.1 -m "Beta release 1"
git tag -a v2.0.0-rc.1 -m "Release candidate 1"
```

---

## 11. Advanced Operations

### `git submodule`

**Tác dụng:** Quản lý repositories con trong repository

**Cú pháp:**
```bash
git submodule [subcommand]
```

**Ví dụ:**

```bash
# Add submodule
git submodule add https://github.com/user/lib.git libs/lib
git submodule add -b develop https://github.com/user/lib.git libs/lib

# Clone repo với submodules
git clone --recurse-submodules https://github.com/user/repo.git
git clone --recursive https://github.com/user/repo.git

# Init submodules sau khi clone
git submodule init
git submodule update

# Hoặc combine:
git submodule update --init
git submodule update --init --recursive

# Update submodules
git submodule update --remote
git submodule update --remote --merge

# List submodules
git submodule
git submodule status

# Foreach command
git submodule foreach git pull origin main
git submodule foreach 'git checkout -b feature'

# Remove submodule
git submodule deinit libs/lib
git rm libs/lib
rm -rf .git/modules/libs/lib
```

---

### `git worktree`

**Tác dụng:** Làm việc với nhiều branches cùng lúc trong các thư mục khác nhau

**Cú pháp:**
```bash
git worktree [subcommand]
```

**Ví dụ:**

```bash
# Add worktree
git worktree add ../project-feature feature/login
git worktree add -b hotfix/bug ../project-hotfix

# List worktrees
git worktree list

# Remove worktree
git worktree remove ../project-feature

# Prune stale worktrees
git worktree prune

# Move worktree
git worktree move ../project-feature ../new-location
```

**Use case:**
```bash
# Main project: working on feature
cd ~/project
git checkout -b feature/new

# Need to make hotfix without stashing:
git worktree add ../project-hotfix main
cd ../project-hotfix
git checkout -b hotfix/critical
# ... make fix ...
git push
cd ~/project

# Continue working on feature
```

---

### `git archive`

**Tác dụng:** Tạo archive (zip, tar) của repository

**Cú pháp:**
```bash
git archive [options] <tree-ish>
```

**Ví dụ:**

```bash
# Tạo zip của HEAD
git archive --format=zip --output=project.zip HEAD

# Tạo tar.gz
git archive --format=tar.gz --output=project.tar.gz HEAD

# Archive branch cụ thể
git archive --format=zip -o release.zip main

# Archive tag
git archive --format=tar.gz -o v1.0.0.tar.gz v1.0.0

# Archive với prefix (folder trong archive)
git archive --prefix=project/ --format=zip -o project.zip HEAD

# Archive path cụ thể
git archive HEAD src/ > src.tar

# Combine với pipe
git archive HEAD | gzip > project.tar.gz
```

---

### `git filter-branch` / `git filter-repo`

**Tác dụng:** Rewrite Git history (remove sensitive data, restructure)

**Lưu ý:** `filter-repo` là tool mới, tốt hơn `filter-branch`

**Ví dụ filter-branch (legacy):**

```bash
# Remove file from all history
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD

# Remove folder from all history
git filter-branch --tree-filter 'rm -rf folder/' HEAD

# Change email in all commits
git filter-branch --commit-filter '
    if [ "$GIT_AUTHOR_EMAIL" = "old@example.com" ];
    then
        GIT_AUTHOR_EMAIL="new@example.com";
        git commit-tree "$@";
    else
        git commit-tree "$@";
    fi' HEAD
```

**Ví dụ filter-repo (recommended):**

```bash
# Install: pip3 install git-filter-repo

# Remove file
git filter-repo --path passwords.txt --invert-paths

# Remove folder
git filter-repo --path secrets/ --invert-paths

# Replace text
echo "password123==>***REMOVED***" > replacements.txt
git filter-repo --replace-text replacements.txt

# Mailmap (change authors)
git filter-repo --mailmap mailmap.txt
```

---

### `git notes`

**Tác dụng:** Thêm notes vào commits (không thay đổi commit)

**Ví dụ:**

```bash
# Add note to commit
git notes add -m "Reviewed by John" abc123

# Show notes
git log --show-notes
git notes show abc123

# Edit note
git notes edit abc123

# Remove note
git notes remove abc123

# Push notes
git push origin refs/notes/*

# Fetch notes
git fetch origin refs/notes/*:refs/notes/*
```

---

## 12. Git Internals

### `git cat-file`

**Tác dụng:** Xem content của Git objects (blob, tree, commit, tag)

**Ví dụ:**

```bash
# Show object type
git cat-file -t abc123

# Show object size
git cat-file -s abc123

# Show object content
git cat-file -p abc123           # Pretty print
git cat-file blob abc123         # Show blob content
git cat-file commit abc123       # Show commit object
```

---

### `git rev-parse`

**Tác dụng:** Parse revision parameters, convert refs to SHA

**Ví dụ:**

```bash
# Get commit SHA
git rev-parse HEAD
git rev-parse main
git rev-parse HEAD~3

# Get short SHA
git rev-parse --short HEAD

# Verify object exists
git rev-parse --verify abc123

# Get branch name
git rev-parse --abbrev-ref HEAD

# Get repo root
git rev-parse --show-toplevel
```

---

### `git fsck`

**Tác dụng:** Verify integrity của Git repository

**Ví dụ:**

```bash
# Check repository
git fsck

# Full check
git fsck --full

# Show progress
git fsck --progress

# Check unreachable objects
git fsck --unreachable

# Check dangling objects
git fsck --lost-found
```

---

### `git gc`

**Tác dụng:** Garbage collection, cleanup unnecessary files, optimize

**Ví dụ:**

```bash
# Auto gc (safe, quick)
git gc

# Aggressive gc (thorough, slow)
git gc --aggressive

# Prune older than date
git gc --prune=now
git gc --prune=1.week.ago

# Don't prune
git gc --no-prune
```

---

## 📚 Bảng Tham Khảo Nhanh

### Lệnh Hàng Ngày

| Mục đích | Lệnh |
|----------|------|
| Khởi tạo repo | `git init` |
| Clone repo | `git clone <url>` |
| Xem status | `git status` |
| Stage file | `git add <file>` |
| Stage tất cả | `git add .` |
| Commit | `git commit -m "message"` |
| Pull updates | `git pull` |
| Push commits | `git push` |
| Xem log | `git log --oneline` |
| Tạo branch | `git checkout -b <branch>` |
| Switch branch | `git switch <branch>` |
| Merge branch | `git merge <branch>` |

### Undo Operations

| Mục đích | Lệnh |
|----------|------|
| Undo file changes | `git restore <file>` |
| Unstage file | `git restore --staged <file>` |
| Undo commit (giữ changes) | `git reset --soft HEAD~1` |
| Undo commit (discard changes) | `git reset --hard HEAD~1` |
| Revert commit đã push | `git revert <commit>` |
| Discard all local changes | `git reset --hard origin/main` |
| Recover deleted commit | `git reflog` → `git reset --hard <sha>` |

### Shortcuts & Aliases

```bash
HEAD      # Commit hiện tại
HEAD~1    # 1 commit trước
HEAD~N    # N commits trước
HEAD^     # Parent commit (merge có nhiều parents)
HEAD^^    # 2 commits trước
@         # Alias cho HEAD

# Set aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.unstage 'reset HEAD --'
```

---

## ⚠️ Lệnh Nguy Hiểm (Cần Cẩn Thận!)

| Lệnh | Nguy hiểm | Giải thích |
|------|-----------|-----------|
| `git reset --hard` | ⚠️⚠️⚠️ | Mất code, không recover được |
| `git push --force` | ⚠️⚠️⚠️ | Ghi đè history của team |
| `git clean -fd` | ⚠️⚠️ | Xóa untracked files vĩnh viễn |
| `git branch -D` | ⚠️⚠️ | Force delete branch, mất code |
| `git rebase` (public) | ⚠️⚠️ | Thay đổi history đã push |
| `git filter-branch` | ⚠️⚠️⚠️ | Rewrite toàn bộ history |
| `git push --force-with-lease` | ⚠️ | An toàn hơn --force, nhưng vẫn cẩn thận |

### An Toàn Hơn:

- Dùng `git push --force-with-lease` thay vì `--force`
- Dùng `git clean -n` (dry-run) trước khi `git clean -f`
- Dùng `git reset --soft` hoặc `--mixed` thay vì `--hard`
- Backup branch trước khi rebase: `git branch backup`
- Dùng `git reflog` để recovery

---

## 💡 Tips & Best Practices

### 1. Commit Messages

```bash
# Good
git commit -m "feat: add user authentication"
git commit -m "fix: resolve memory leak in parser"
git commit -m "docs: update API documentation"

# Bad
git commit -m "update"
git commit -m "fix bug"
git commit -m "asdfasdf"
```

### 2. Branch Naming

```bash
# Good
feature/user-authentication
feature/add-payment-gateway
bugfix/fix-login-error
hotfix/critical-security-patch
release/v1.2.0

# Bad
myfeature
test
tmp
new-branch
```

### 3. Pull Strategies

```bash
# Prefer rebase cho clean history
git config --global pull.rebase true
git pull --rebase

# Hoặc fetch + rebase manually
git fetch origin
git rebase origin/main
```

### 4. Cleanup

```bash
# Xóa merged branches
git branch --merged | grep -v "\*" | grep -v "main" | grep -v "develop" | xargs -n 1 git branch -d

# Cleanup remote tracking
git fetch --prune
git remote prune origin

# Optimize repo
git gc --aggressive
```

### 5. Aliases Hữu Ích

```bash
# Add to ~/.gitconfig

[alias]
    st = status -sb
    co = checkout
    br = branch
    ci = commit
    unstage = reset HEAD --
    last = log -1 HEAD
    visual = !gitk
    amend = commit --amend --no-edit
    undo = reset --soft HEAD~1
    lg = log --graph --oneline --all --decorate
    sync = !git fetch && git rebase origin/main
    cleanup = "!git branch --merged | grep -v 'main\\|develop' | xargs git branch -d"
```

---

## 🔍 Troubleshooting Commands

```bash
# Xem what changed
git diff HEAD
git diff --staged
git status

# Xem recent commits
git log --oneline -10
git log --graph --oneline --all

# Tìm khi nào file bị xóa
git log --all --full-history -- path/to/file

# Tìm commit chứa text
git log -S "text"
git log -G "regex"

# Xem ai change gì
git blame file.txt

# Find lost commits
git reflog
git fsck --lost-found

# Verify repo integrity
git fsck

# Clean up
git gc
git prune
```

---

*Git Training Documentation v1.0 - Appendix*
*Tài liệu tổng hợp chi tiết các lệnh Git phổ biến*
