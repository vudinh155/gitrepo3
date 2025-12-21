# 08 - Advanced Git: Git Nâng Cao

> **Đối tượng:** Senior Developer, Git Master
> **Thời gian học:** 2-3 giờ
> **Mục tiêu:** Thành thạo các kỹ thuật Git nâng cao

---

## 📖 Mục Lục

1. [Git Internals - Hiểu Bản Chất Git](#1-git-internals---hiểu-bản-chất-git)
2. [Rebase Nâng Cao](#2-rebase-nâng-cao)
3. [Cherry-pick và Patch](#3-cherry-pick-và-patch)
4. [Reset, Revert, Restore](#4-reset-revert-restore)
5. [Git Tag và Versioning](#5-git-tag-và-versioning)
6. [Reflog - Time Machine](#6-reflog---time-machine)
7. [Submodules và Subtrees](#7-submodules-và-subtrees)
8. [Chiến Lược Rollback](#8-chiến-lược-rollback)

---

## 1. Git Internals - Hiểu Bản Chất Git

### 1.1. Git Object Model

```
┌─────────────────────────────────────────────────────────────────────┐
│                      GIT OBJECTS                                    │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Git lưu 4 loại objects:                                            │
│                                                                     │
│  1. BLOB (Binary Large Object)                                      │
│     = Nội dung file                                                 │
│     $ git cat-file -p <hash>                                        │
│                                                                     │
│  2. TREE                                                            │
│     = Thư mục (danh sách blobs và trees con)                       │
│                                                                     │
│  3. COMMIT                                                          │
│     = Metadata + pointer to tree + parent commit(s)                │
│                                                                     │
│  4. TAG                                                             │
│     = Pointer to commit với metadata                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

Cấu trúc:

         ┌──────────┐
         │  COMMIT  │
         │  abc123  │
         └────┬─────┘
              │
              ▼
         ┌──────────┐
         │   TREE   │ (root directory)
         └────┬─────┘
              │
    ┌─────────┼─────────┐
    │         │         │
    ▼         ▼         ▼
┌──────┐  ┌──────┐  ┌──────┐
│ BLOB │  │ BLOB │  │ TREE │ (subdirectory)
│app.js│  │pkg.js│  │ src  │
└──────┘  └──────┘  └──┬───┘
                       │
                       ▼
                   ┌──────┐
                   │ BLOB │
                   │util.js│
                   └──────┘
```

### 1.2. Xem Git Objects

```bash
# Xem commit object
git cat-file -p HEAD
# tree abc123...
# parent def456...
# author Name <email> timestamp
# committer Name <email> timestamp
#
# Commit message

# Xem tree object
git cat-file -p HEAD^{tree}
# 100644 blob abc123... app.js
# 100644 blob def456... package.json
# 040000 tree ghi789... src

# Xem blob (nội dung file)
git cat-file -p <blob-hash>

# Xem type của object
git cat-file -t <hash>
```

### 1.3. Refs và HEAD

```
┌─────────────────────────────────────────────────────────────────────┐
│                    REFS VÀ HEAD                                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  .git/                                                              │
│  ├── HEAD                    → Pointer đến branch hiện tại         │
│  │   (ref: refs/heads/main)                                        │
│  │                                                                  │
│  └── refs/                                                          │
│      ├── heads/              → Local branches                      │
│      │   ├── main           → abc123...                            │
│      │   └── develop        → def456...                            │
│      │                                                              │
│      ├── remotes/origin/     → Remote tracking branches            │
│      │   ├── main           → abc123...                            │
│      │   └── develop        → def456...                            │
│      │                                                              │
│      └── tags/               → Tags                                │
│          └── v1.0.0         → ghi789...                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

HEAD có thể là:
- Symbolic ref: ref: refs/heads/main (đang ở branch)
- Detached HEAD: abc123... (checkout commit trực tiếp)
```

---

## 2. Rebase Nâng Cao

### 2.1. Interactive Rebase Options

```bash
# Interactive rebase N commits gần nhất
git rebase -i HEAD~N

# Editor mở với các options:
# pick   = giữ nguyên commit
# reword = giữ commit, đổi message
# edit   = giữ commit, dừng để edit
# squash = gộp vào commit trước, giữ message
# fixup  = gộp vào commit trước, bỏ message
# drop   = xóa commit
# exec   = chạy command
```

### 2.2. Ví dụ Squash

```bash
# Trước:
# abc Commit 3
# def Commit 2
# ghi Commit 1

git rebase -i HEAD~3

# Sửa:
# pick ghi Commit 1
# squash def Commit 2
# squash abc Commit 3

# Kết quả: 1 commit chứa tất cả changes
```

### 2.3. Rebase onto

```bash
# Di chuyển branch sang base khác

# Trước:
#       A---B---C  feat
#      /
# D---E---F---G  main
#          \
#           H---I  develop

# Muốn: Di chuyển feat từ E sang G (main mới nhất)

git rebase --onto main E feat

# Sau:
#               A'--B'--C'  feat
#              /
# D---E---F---G  main
#          \
#           H---I  develop
```

### 2.4. Autosquash

```bash
# Khi commit với prefix đặc biệt
git commit -m "feat: add login"
git commit -m "fixup! feat: add login"  # Sẽ tự squash vào commit login
git commit -m "squash! feat: add login" # Tương tự

# Khi rebase
git rebase -i --autosquash HEAD~3
# Git tự sắp xếp fixup!/squash! commits
```

### 2.5. Preserve Merge Commits

```bash
# Mặc định rebase làm phẳng history
# Để giữ merge commits:
git rebase -i --rebase-merges HEAD~10
```

---

## 3. Cherry-pick và Patch

### 3.1. Cherry-pick Cơ Bản

```bash
# Lấy 1 commit
git cherry-pick abc123

# Lấy nhiều commits
git cherry-pick abc123 def456

# Lấy range (không bao gồm commit đầu)
git cherry-pick abc123..def456

# Lấy range (bao gồm cả 2 đầu)
git cherry-pick abc123^..def456
```

### 3.2. Cherry-pick Options

```bash
# Không tự động commit (để review/edit)
git cherry-pick abc123 --no-commit

# Edit message
git cherry-pick abc123 --edit

# Cherry-pick merge commit (chọn parent)
git cherry-pick -m 1 abc123  # -m 1 = first parent

# Tiếp tục sau khi resolve conflict
git cherry-pick --continue

# Abort
git cherry-pick --abort
```

### 3.3. Tạo và Áp Dụng Patch

```bash
# Tạo patch từ commits
git format-patch HEAD~3              # 3 commits gần nhất
git format-patch abc123..def456      # Range
git format-patch -o patches/ HEAD~3  # Output to folder

# Xem patch content
git apply --stat 0001-commit-message.patch
git apply --check 0001-commit-message.patch

# Áp dụng patch
git apply 0001-commit-message.patch           # Apply changes only
git am 0001-commit-message.patch              # Apply + commit
git am patches/*.patch                        # Apply nhiều patches
```

### 3.4. Diff và Patch thủ công

```bash
# Tạo diff
git diff > changes.patch
git diff HEAD~3 HEAD > changes.patch
git diff main feature/xxx > feature.patch

# Áp dụng diff
git apply changes.patch
patch -p1 < changes.patch
```

---

## 4. Reset, Revert, Restore

### 4.1. Ba lệnh, ba mục đích

```
┌─────────────────────────────────────────────────────────────────────┐
│              RESET vs REVERT vs RESTORE                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  RESET: Di chuyển branch pointer, thay đổi history                 │
│         Dùng khi: Undo commits CHƯA push                           │
│         $ git reset [--soft|--mixed|--hard] <commit>               │
│                                                                     │
│  REVERT: Tạo commit mới để undo, KHÔNG đổi history                 │
│          Dùng khi: Undo commits ĐÃ push                            │
│          $ git revert <commit>                                     │
│                                                                     │
│  RESTORE: Khôi phục files, KHÔNG ảnh hưởng commits                 │
│           Dùng khi: Undo thay đổi trong working directory          │
│           $ git restore <file>                                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2. Reset Chi Tiết

```
┌─────────────────────────────────────────────────────────────────────┐
│                    3 MODES CỦA RESET                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  --soft:   HEAD moves, Staging giữ nguyên, Working giữ nguyên      │
│            → Commits biến mất, changes vẫn staged                   │
│                                                                     │
│  --mixed:  HEAD moves, Staging reset, Working giữ nguyên (default) │
│            → Commits biến mất, changes trong working dir            │
│                                                                     │
│  --hard:   HEAD moves, Staging reset, Working reset                │
│            → Mọi thứ biến mất! ⚠️                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

           Commit          Staging         Working
           History          Area          Directory
              │               │               │
  --soft      ✓ changed       ✗ kept         ✗ kept
  --mixed     ✓ changed       ✓ reset        ✗ kept
  --hard      ✓ changed       ✓ reset        ✓ reset
```

```bash
# Undo 1 commit, giữ changes staged
git reset --soft HEAD~1

# Undo 1 commit, unstage changes
git reset HEAD~1
git reset --mixed HEAD~1

# Undo hoàn toàn (CẨN THẬN!)
git reset --hard HEAD~1

# Reset về commit cụ thể
git reset --hard abc123

# Reset 1 file về trạng thái của commit
git reset HEAD~1 -- file.txt
```

### 4.3. Revert Chi Tiết

```bash
# Revert 1 commit (tạo new commit)
git revert abc123

# Revert không tự commit
git revert abc123 --no-commit

# Revert nhiều commits
git revert HEAD~3..HEAD --no-commit
git commit -m "Revert last 3 commits"

# Revert merge commit
git revert -m 1 abc123  # -m 1 = giữ first parent
```

### 4.4. Restore Chi Tiết

```bash
# Khôi phục file từ staging (undo modifications)
git restore file.txt

# Unstage file (giữ modifications)
git restore --staged file.txt

# Khôi phục từ commit cụ thể
git restore --source=HEAD~2 file.txt

# Khôi phục cả staged và working
git restore --source=HEAD~2 --staged --worktree file.txt
```

---

## 5. Git Tag và Versioning

### 5.1. Loại Tags

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LIGHTWEIGHT vs ANNOTATED                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Lightweight: Chỉ là pointer đến commit                            │
│  $ git tag v1.0.0                                                   │
│                                                                     │
│  Annotated: Full object với metadata (khuyên dùng)                 │
│  $ git tag -a v1.0.0 -m "Release version 1.0.0"                    │
│  - Có tagger name, email, date                                      │
│  - Có message                                                       │
│  - Có thể sign với GPG                                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.2. Các Thao Tác Với Tags

```bash
# Tạo annotated tag
git tag -a v1.0.0 -m "First stable release"

# Tạo tag cho commit cũ
git tag -a v0.9.0 abc123 -m "Beta release"

# List tags
git tag
git tag -l "v1.*"

# Xem tag details
git show v1.0.0

# Push tags
git push origin v1.0.0
git push origin --tags    # Push tất cả tags

# Xóa tag
git tag -d v1.0.0                    # Local
git push origin --delete v1.0.0     # Remote

# Checkout tag (detached HEAD)
git checkout v1.0.0

# Tạo branch từ tag
git checkout -b hotfix/v1.0.1 v1.0.0
```

### 5.3. Semantic Versioning

```
┌─────────────────────────────────────────────────────────────────────┐
│                  SEMANTIC VERSIONING (SemVer)                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│                    MAJOR.MINOR.PATCH                                │
│                      1   .  0  .  0                                 │
│                      │      │     │                                 │
│                      │      │     └── Bug fixes                     │
│                      │      └──────── New features (backward compat)│
│                      └─────────────── Breaking changes              │
│                                                                     │
│  Ví dụ:                                                             │
│  1.0.0 → 1.0.1  : Bug fix                                          │
│  1.0.1 → 1.1.0  : Thêm feature mới                                 │
│  1.1.0 → 2.0.0  : Breaking changes                                 │
│                                                                     │
│  Pre-release: 1.0.0-alpha, 1.0.0-beta.1, 1.0.0-rc.1                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 6. Reflog - Time Machine

### 6.1. Reflog Là Gì?

```
Reflog = Reference Log
- Ghi lại MỌI thay đổi của HEAD
- Kể cả reset --hard cũng recovery được
- Mặc định giữ 90 ngày
```

### 6.2. Các Lệnh Reflog

```bash
# Xem reflog
git reflog
# abc1234 HEAD@{0}: commit: Latest commit
# def5678 HEAD@{1}: reset: moving to HEAD~1
# ghi9012 HEAD@{2}: commit: The commit we "lost"

# Xem reflog của branch cụ thể
git reflog show main

# Xem reflog với thời gian
git reflog --date=relative

# Recovery commit đã "mất"
git reset --hard HEAD@{2}
# hoặc
git checkout -b recovery-branch HEAD@{2}
```

### 6.3. Kịch Bản Recovery

```bash
# Tình huống: Reset --hard nhầm
$ git reset --hard HEAD~5  # Oops! Mất 5 commits!

# Recovery:
$ git reflog
# abc1234 HEAD@{0}: reset: moving to HEAD~5
# def5678 HEAD@{1}: commit: Important commit 5
# ...

$ git reset --hard def5678  # Recovered!

# Hoặc cherry-pick từng commit
$ git cherry-pick HEAD@{1}
```

---

## 7. Submodules và Subtrees

### 7.1. Git Submodules

```bash
# Thêm submodule
git submodule add https://github.com/lib/library.git libs/library

# Clone repo có submodules
git clone --recursive <repo-url>

# Hoặc sau khi clone
git submodule init
git submodule update

# Update submodule
cd libs/library
git fetch
git checkout v2.0.0
cd ../..
git add libs/library
git commit -m "Update library to v2.0.0"

# Update tất cả submodules
git submodule update --remote

# Xóa submodule
git submodule deinit libs/library
git rm libs/library
rm -rf .git/modules/libs/library
```

### 7.2. Git Subtrees (Alternative)

```bash
# Thêm subtree
git subtree add --prefix=libs/library \
  https://github.com/lib/library.git main --squash

# Pull updates
git subtree pull --prefix=libs/library \
  https://github.com/lib/library.git main --squash

# Push changes back (nếu có quyền)
git subtree push --prefix=libs/library \
  https://github.com/lib/library.git main
```

### 7.3. So sánh

```
┌─────────────────────────────────────────────────────────────────────┐
│              SUBMODULE vs SUBTREE                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  SUBMODULE:                                                         │
│  ✅ Giữ riêng history của library                                   │
│  ✅ Dễ update version                                                │
│  ❌ Phức tạp khi clone/pull                                         │
│  ❌ Cần commands đặc biệt                                           │
│                                                                     │
│  SUBTREE:                                                           │
│  ✅ Code được copy vào repo chính                                   │
│  ✅ Clone/pull như bình thường                                      │
│  ❌ History trộn lẫn                                                 │
│  ❌ Khó tách riêng sau này                                          │
│                                                                     │
│  Khuyến nghị:                                                       │
│  - Library bên ngoài: Submodule                                    │
│  - Shared code nội bộ: Subtree hoặc package                        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 8. Chiến Lược Rollback

### 8.1. Rollback Scenarios

```
┌─────────────────────────────────────────────────────────────────────┐
│                  ROLLBACK DECISION TREE                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│                    Cần rollback?                                    │
│                         │                                           │
│          ┌──────────────┴──────────────┐                            │
│          │                             │                            │
│     Đã push?                      Chưa push                        │
│          │                             │                            │
│          ▼                             ▼                            │
│       REVERT                         RESET                          │
│          │                             │                            │
│   ┌──────┴──────┐              ┌───────┴───────┐                   │
│   │             │              │               │                   │
│  1 commit   Nhiều commits   Soft          Hard                     │
│   │             │           (giữ code)   (xóa code)                │
│   ▼             ▼              │               │                   │
│ git revert   git revert       git reset     git reset              │
│ HEAD        HEAD~3..HEAD     --soft HEAD~N  --hard HEAD~N          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 8.2. Rollback Production

```bash
# Tình huống: Deploy xong phát hiện bug

# Cách 1: Revert (tạo commit mới)
git checkout main
git revert HEAD              # Revert commit cuối
git push origin main
# Deploy lại

# Cách 2: Deploy tag cũ
git checkout v1.0.0         # Tag stable trước đó
# Deploy

# Cách 3: Hotfix branch
git checkout -b hotfix/urgent main
# Fix nhanh
git commit -m "hotfix: fix critical bug"
git checkout main
git merge hotfix/urgent
git push origin main
# Deploy
```

### 8.3. Rollback Merge

```bash
# Revert merge commit
# -m 1 = giữ main line, undo feature được merge
git revert -m 1 <merge-commit>

# Lưu ý: Sau này muốn merge lại feature
# cần revert cái revert commit!
git revert <revert-commit>
git merge feature/xxx
```

### 8.4. Emergency Procedures

```
┌─────────────────────────────────────────────────────────────────────┐
│                    EMERGENCY ROLLBACK                               │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. ĐỪNG PANIC                                                      │
│                                                                     │
│  2. Xác định commit/tag stable cuối                                │
│     $ git log --oneline -20                                        │
│     $ git tag                                                      │
│                                                                     │
│  3. Thông báo team                                                  │
│     "Đang rollback production, đừng deploy"                        │
│                                                                     │
│  4. Rollback                                                        │
│     Option A: Deploy tag cũ                                        │
│     Option B: Revert và push                                       │
│                                                                     │
│  5. Verify production                                               │
│                                                                     │
│  6. Post-mortem                                                     │
│     - Tại sao bug xảy ra?                                          │
│     - Làm sao ngăn chặn?                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📋 Quick Reference

### Advanced Commands

| Lệnh | Chức năng |
|------|-----------|
| `git rebase -i HEAD~N` | Interactive rebase |
| `git cherry-pick <commit>` | Copy commit |
| `git reflog` | Xem history của HEAD |
| `git reset --soft/mixed/hard` | Undo commits |
| `git revert <commit>` | Tạo undo commit |
| `git tag -a v1.0 -m "msg"` | Tạo annotated tag |
| `git bisect start` | Binary search bug |
| `git submodule add <url>` | Thêm submodule |

### Recovery Commands

| Tình huống | Lệnh |
|------------|------|
| Reset nhầm | `git reflog` → `git reset --hard HEAD@{N}` |
| Xóa branch nhầm | `git reflog` → `git checkout -b branch HEAD@{N}` |
| Revert merge | `git revert -m 1 <merge-commit>` |
| Khôi phục file | `git restore --source=<commit> <file>` |

---

## ✅ Checklist Git Master

```
□ Hiểu Git internals (objects, refs, HEAD)
□ Thành thạo interactive rebase
□ Biết dùng cherry-pick và patch
□ Phân biệt reset/revert/restore
□ Quản lý tags và versioning
□ Biết dùng reflog để recovery
□ Hiểu submodules/subtrees
□ Có chiến lược rollback rõ ràng
```

---

## ➡️ Bước Tiếp Theo

Học file **09-ci-cd-protection.md** - CI/CD và Branch Protection

---

*Tài liệu thuộc bộ Git Training Documentation v1.0*
