# 07 - Practical Exercises: Bài Tập Thực Hành

> **Đối tượng:** Tất cả thành viên team
> **Thời gian:** 2-4 giờ
> **Mục tiêu:** Thực hành các kỹ năng Git đã học

---

## 📖 Mục Lục

1. [Chuẩn Bị Môi Trường](#1-chuẩn-bị-môi-trường)
2. [Bài Tập Level Junior](#2-bài-tập-level-junior)
3. [Bài Tập Level Middle](#3-bài-tập-level-middle)
4. [Bài Tập Level Senior/Git Master](#4-bài-tập-level-seniorgit-master)
5. [Kịch Bản Team 3-5 Người](#5-kịch-bản-team-3-5-người)

---

## 1. Chuẩn Bị Môi Trường

### 1.1. Tạo Practice Repository

```bash
# Tạo repo mới để thực hành
mkdir git-practice
cd git-practice
git init

# Tạo file ban đầu
echo "# Git Practice Repo" > README.md
echo "console.log('Hello Git');" > app.js

# Commit đầu tiên
git add .
git commit -m "Initial commit"

# Tạo remote (nếu có GitHub/GitLab)
# git remote add origin git@github.com:your-username/git-practice.git
# git push -u origin main
```

### 1.2. Setup file để thực hành conflict

```bash
# Tạo file có nhiều dòng để dễ tạo conflict
cat > calculator.js << 'EOF'
// Calculator module

function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

function multiply(a, b) {
  return a * b;
}

function divide(a, b) {
  return a / b;
}

module.exports = { add, subtract, multiply, divide };
EOF

git add calculator.js
git commit -m "Add calculator module"
```

---

## 2. Bài Tập Level Junior

### 🎯 Bài 1: Clone và Commit Đầu Tiên

**Mục tiêu:** Làm quen với flow cơ bản

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 1                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nhiệm vụ:                                                          │
│  1. Clone repo từ GitHub (hoặc tạo mới)                            │
│  2. Tạo file hello.txt với nội dung "Hello, Git!"                 │
│  3. Commit file                                                     │
│  4. Xem lịch sử commit                                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# Bước 1: Clone hoặc tạo repo
git clone <repo-url>
# hoặc
mkdir my-repo && cd my-repo && git init

# Bước 2: Tạo file
echo "Hello, Git!" > hello.txt

# Bước 3: Kiểm tra status
git status

# Bước 4: Add và commit
git add hello.txt
git commit -m "feat: add hello.txt file"

# Bước 5: Xem lịch sử
git log --oneline
```

**Kết quả mong đợi:**

```
$ git log --oneline
abc1234 (HEAD -> main) feat: add hello.txt file
```

---

### 🎯 Bài 2: Tạo Branch và Merge

**Mục tiêu:** Hiểu flow branch cơ bản

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 2                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nhiệm vụ:                                                          │
│  1. Tạo branch mới: feat/add-greeting                              │
│  2. Thêm function greeting() vào app.js                            │
│  3. Commit                                                          │
│  4. Chuyển về main                                                  │
│  5. Merge branch vào main                                          │
│  6. Xóa branch                                                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# Bước 1: Tạo và chuyển sang branch mới
git checkout -b feat/add-greeting

# Bước 2: Thêm function vào app.js
cat >> app.js << 'EOF'

function greeting(name) {
  return `Hello, ${name}!`;
}
EOF

# Bước 3: Commit
git add app.js
git commit -m "feat: add greeting function"

# Bước 4: Chuyển về main
git checkout main

# Bước 5: Merge
git merge feat/add-greeting

# Bước 6: Xóa branch
git branch -d feat/add-greeting

# Kiểm tra
git log --oneline --graph
```

**Kết quả mong đợi:**

```
* def5678 (HEAD -> main) feat: add greeting function
* abc1234 Initial commit
```

---

### 🎯 Bài 3: Sử Dụng Git Stash

**Mục tiêu:** Biết cách lưu tạm công việc

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 3                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Kịch bản: Đang code dở, có bug cần fix gấp                        │
│                                                                     │
│  Nhiệm vụ:                                                          │
│  1. Đang ở feat/new-feature, code dở                               │
│  2. Stash code                                                      │
│  3. Chuyển sang fix/urgent-bug, sửa bug                            │
│  4. Quay lại feat/new-feature                                      │
│  5. Lấy code từ stash                                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# Setup: Tạo và sửa file (giả lập đang code dở)
git checkout -b feat/new-feature
echo "// Work in progress..." >> app.js

# Bước 1: Xem status
git status  # Modified: app.js

# Bước 2: Stash
git stash save "WIP: new feature"

# Bước 3: Chuyển sang fix bug
git checkout main
git checkout -b fix/urgent-bug
echo "// Bug fixed" >> app.js
git add app.js
git commit -m "fix: urgent bug"
git checkout main
git merge fix/urgent-bug
git branch -d fix/urgent-bug

# Bước 4: Quay lại feature
git checkout feat/new-feature

# Bước 5: Lấy lại code
git stash pop

# Xem stash list (phải rỗng)
git stash list
```

---

### 🎯 Bài 4: Undo Mistakes

**Mục tiêu:** Biết cách sửa sai

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 4                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nhiệm vụ:                                                          │
│  1. Sửa file và commit                                              │
│  2. Nhận ra commit sai → Undo commit (giữ changes)                 │
│  3. Sửa lại và commit đúng                                          │
│  4. Nhận ra message sai → Sửa message                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# Tạo commit sai
echo "wrong code" >> app.js
git add app.js
git commit -m "wrong commit message"

# Undo commit (giữ changes)
git reset --soft HEAD~1

# Kiểm tra - file vẫn staged
git status

# Sửa lại code
echo "correct code" > temp.txt && cat temp.txt >> app.js && rm temp.txt

# Commit lại với message sai
git commit -m "feat: add feture"

# Sửa message (chưa push)
git commit --amend -m "feat: add feature"

# Kiểm tra
git log --oneline -1
```

---

## 3. Bài Tập Level Middle

### 🎯 Bài 5: Xử Lý Conflict

**Mục tiêu:** Thành thạo resolve conflict

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 5                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nhiệm vụ: Tạo và resolve conflict                                 │
│                                                                     │
│  1. Từ main, tạo branch feat/change-add                            │
│  2. Sửa function add() trong calculator.js                         │
│  3. Quay về main, cũng sửa function add()                          │
│  4. Merge feat/change-add → Conflict!                              │
│  5. Resolve conflict                                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# Bước 1: Tạo branch và sửa add()
git checkout -b feat/change-add

# Sửa calculator.js - đổi function add
sed -i 's/return a + b;/return a + b; \/\/ Version A/' calculator.js
git add calculator.js
git commit -m "feat: update add function - version A"

# Bước 2: Quay về main và sửa cùng dòng
git checkout main
sed -i 's/return a + b;/return a + b; \/\/ Version B/' calculator.js
git add calculator.js
git commit -m "feat: update add function - version B"

# Bước 3: Merge - sẽ conflict!
git merge feat/change-add
# CONFLICT (content): Merge conflict in calculator.js

# Bước 4: Xem conflict
cat calculator.js

# Bước 5: Resolve - chọn cách kết hợp
# Mở file và sửa thủ công, xóa markers
# Hoặc:
git checkout --theirs calculator.js  # Chọn version A
# Hoặc:
git checkout --ours calculator.js    # Chọn version B

# Bước 6: Complete merge
git add calculator.js
git commit -m "Merge feat/change-add: resolve conflict"
```

---

### 🎯 Bài 6: Interactive Rebase

**Mục tiêu:** Biết squash và sửa commits

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 6                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nhiệm vụ:                                                          │
│  1. Tạo 4 commits nhỏ                                               │
│  2. Dùng interactive rebase để:                                     │
│     - Squash 3 commits đầu thành 1                                  │
│     - Reword commit cuối                                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# Tạo 4 commits
git checkout -b feat/multi-commits

echo "line 1" >> multi.txt && git add . && git commit -m "WIP: add line 1"
echo "line 2" >> multi.txt && git add . && git commit -m "WIP: add line 2"
echo "line 3" >> multi.txt && git add . && git commit -m "WIP: add line 3"
echo "line 4" >> multi.txt && git add . && git commit -m "add line 4 typo"

# Xem log
git log --oneline -4

# Interactive rebase 4 commits gần nhất
git rebase -i HEAD~4

# Editor sẽ mở:
# pick abc1234 WIP: add line 1
# pick def5678 WIP: add line 2
# pick ghi9012 WIP: add line 3
# pick jkl3456 add line 4 typo

# Sửa thành:
# pick abc1234 WIP: add line 1
# squash def5678 WIP: add line 2
# squash ghi9012 WIP: add line 3
# reword jkl3456 add line 4 typo

# Sau đó:
# - Nhập message mới cho squashed commit
# - Nhập message mới cho commit cuối

# Kiểm tra
git log --oneline -2
```

---

### 🎯 Bài 7: Cherry-pick

**Mục tiêu:** Biết lấy commit cụ thể từ branch khác

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 7                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Kịch bản:                                                          │
│  - Branch develop có bug fix quan trọng                            │
│  - Cần lấy chỉ bug fix đó vào main (không merge cả branch)         │
│                                                                     │
│  Nhiệm vụ:                                                          │
│  1. Tạo branch với nhiều commits                                    │
│  2. Cherry-pick chỉ 1 commit vào main                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# Tạo branch với nhiều commits
git checkout -b develop
echo "feature 1" >> features.txt && git add . && git commit -m "feat: add feature 1"
echo "bug fix" >> bugfix.txt && git add . && git commit -m "fix: critical bug fix"
echo "feature 2" >> features.txt && git add . && git commit -m "feat: add feature 2"

# Lấy hash của commit cần cherry-pick
git log --oneline
# yyy fix: critical bug fix   ← Cần commit này

# Chuyển về main
git checkout main

# Cherry-pick
git cherry-pick <commit-hash>

# Kiểm tra
git log --oneline
# main bây giờ có bug fix, không có feature 1, 2
```

---

## 4. Bài Tập Level Senior/Git Master

### 🎯 Bài 8: Revert và Reset Nâng Cao

**Mục tiêu:** Thành thạo undo trong mọi tình huống

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 8                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nhiệm vụ:                                                          │
│  1. Tạo 5 commits trên main                                        │
│  2. Reset về commit thứ 3 (soft, mixed, hard)                      │
│  3. Recover commits đã reset (reflog)                              │
│  4. Revert commit thứ 4 (tạo revert commit)                        │
│  5. Revert một merge commit                                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# Setup: Tạo 5 commits
git checkout -b test-reset
for i in 1 2 3 4 5; do
  echo "commit $i" >> history.txt
  git add . && git commit -m "Commit $i"
done

# Xem log
git log --oneline
# e5 Commit 5 (HEAD)
# d4 Commit 4
# c3 Commit 3
# b2 Commit 2
# a1 Commit 1

# --- RESET SOFT ---
# Undo commit 5, 4 nhưng giữ changes staged
git reset --soft HEAD~2
git status  # Changes staged
git log --oneline  # HEAD ở Commit 3

# Commit lại
git commit -m "Commit 4 and 5 combined"

# --- RESET MIXED (default) ---
git reset HEAD~1
git status  # Changes NOT staged
git add . && git commit -m "Re-commit"

# --- RESET HARD (cẩn thận!) ---
git reset --hard HEAD~1
# Changes DELETED!

# --- RECOVER với REFLOG ---
git reflog
# abc1234 HEAD@{0}: reset: moving to HEAD~1
# def5678 HEAD@{1}: commit: Re-commit  ← Muốn về đây

git reset --hard def5678  # Recover!

# --- REVERT (tạo undo commit) ---
# Revert commit cụ thể
git revert HEAD~1 --no-edit

# --- REVERT MERGE ---
# Giả sử có merge commit
git checkout main
git merge test-reset

# Revert merge (phải chỉ định parent)
git revert -m 1 HEAD  # -m 1 = giữ main, undo merge
```

---

### 🎯 Bài 9: Git Bisect (Tìm Bug)

**Mục tiêu:** Dùng binary search tìm commit gây bug

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 9                                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Kịch bản:                                                          │
│  - App bị bug, không biết từ commit nào                            │
│  - Biết commit A (cũ) hoạt động OK                                 │
│  - Commit Z (mới) bị bug                                           │
│                                                                     │
│  Nhiệm vụ: Dùng bisect tìm commit gây bug                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# Setup: Tạo history với bug ở giữa
git checkout -b test-bisect

# Commits 1-3: OK
for i in 1 2 3; do
  echo "good $i" >> app.txt
  git add . && git commit -m "Good commit $i"
done

# Commit 4: Introduce bug!
echo "BUG" >> app.txt
git add . && git commit -m "Bad commit - introduces bug"

# Commits 5-7: Cũng bad (vì bug vẫn còn)
for i in 5 6 7; do
  echo "more code $i" >> app.txt
  git add . && git commit -m "Commit $i"
done

# Bây giờ tìm bug với bisect
git bisect start
git bisect bad              # Commit hiện tại (7) là bad
git bisect good HEAD~6      # Commit 1 là good

# Git sẽ checkout commit giữa
# Kiểm tra xem có bug không (tìm "BUG" trong file)
grep -q "BUG" app.txt && echo "BAD" || echo "GOOD"

# Nếu bad:
git bisect bad

# Nếu good:
git bisect good

# Lặp lại cho đến khi tìm được commit gây bug
# Output: abc1234 is the first bad commit

# Kết thúc bisect
git bisect reset
```

---

### 🎯 Bài 10: Cleanup Repository

**Mục tiêu:** Dọn dẹp repo lớn, cũ

```
┌─────────────────────────────────────────────────────────────────────┐
│                         BÀI TẬP 10                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Nhiệm vụ:                                                          │
│  1. Xóa tất cả branches đã merge                                   │
│  2. Xóa file lớn khỏi history (filter-branch)                      │
│  3. Garbage collection                                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

**Hướng dẫn:**

```bash
# 1. Xóa branches đã merge
git checkout main
git branch --merged | grep -v "main\|develop" | xargs git branch -d

# 2. Xóa file lớn khỏi history (CẢNH BÁO: Thay đổi history!)
# Giả sử có file large.zip đã commit nhầm

# Cách mới với git-filter-repo (recommended)
# pip install git-filter-repo
git filter-repo --path large.zip --invert-paths

# Cách cũ với filter-branch
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch large.zip' \
  --prune-empty --tag-name-filter cat -- --all

# 3. Garbage collection
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Kiểm tra size
du -sh .git
```

---

## 5. Kịch Bản Team 3-5 Người

### 🎯 Simulation: Sprint Development

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KỊCH BẢN TEAM 5 NGƯỜI                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Vai trò:                                                           │
│  - Person A: Tech Lead / Git Master                                │
│  - Person B: Senior Developer                                       │
│  - Person C: Developer                                              │
│  - Person D: Developer                                              │
│  - Person E: Junior Developer                                       │
│                                                                     │
│  Sprint Goal: Build simple Todo App                                │
│                                                                     │
│  Tasks:                                                             │
│  1. [Person B] feat/todo-model - Todo data model                   │
│  2. [Person C] feat/todo-api - Todo API endpoints                  │
│  3. [Person D] feat/todo-ui - Todo UI components                   │
│  4. [Person E] feat/todo-list - Todo list component                │
│  5. [Person A] Review và merge tất cả                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Timeline

```
Ngày 1 (Setup):
────────────────────────────────────────────────────────────────────

Person A:
  $ git init todo-app
  $ git checkout -b develop
  $ echo "# Todo App" > README.md
  $ git add . && git commit -m "Initial commit"
  $ git push -u origin main
  $ git push -u origin develop

All:
  $ git clone <repo-url>
  $ git checkout develop


Ngày 2-3 (Development):
────────────────────────────────────────────────────────────────────

Person B (Todo Model):
  $ git checkout develop && git pull
  $ git checkout -b feat/todo-model
  $ # ... code model ...
  $ git add . && git commit -m "feat: add Todo model with CRUD"
  $ git push -u origin feat/todo-model
  $ # Tạo PR → develop

Person C (Todo API):
  $ git checkout develop && git pull
  $ git checkout -b feat/todo-api
  $ # ... code API ...
  $ git add . && git commit -m "feat: add REST API for todos"
  $ git push -u origin feat/todo-api
  $ # Tạo PR → develop

Person D (Todo UI):
  $ git checkout develop && git pull
  $ git checkout -b feat/todo-ui
  $ # ... code UI ...
  $ git add . && git commit -m "feat: add TodoItem component"
  $ git push -u origin feat/todo-ui
  $ # Tạo PR → develop

Person E (Todo List):
  $ # Đợi UI merge trước
  $ git checkout develop && git pull  # Có UI components
  $ git checkout -b feat/todo-list
  $ # ... code list ...
  $ git add . && git commit -m "feat: add TodoList component"
  $ git push -u origin feat/todo-list
  $ # Tạo PR → develop


Ngày 4 (Merge và Conflict):
────────────────────────────────────────────────────────────────────

Person A (Review):
  - Review PR #1 (Model) → Approve → Merge
  - Review PR #2 (API) → Request changes
    "Cần thêm validation cho todo title"

Person C (Fix):
  $ git checkout feat/todo-api
  $ # ... fix theo feedback ...
  $ git add . && git commit -m "feat: add title validation"
  $ git push
  # PR tự động cập nhật

Person A:
  - Re-review PR #2 → Approve → Merge
  - Review PR #3 (UI) → Conflict với develop!

Person D (Resolve conflict):
  $ git checkout feat/todo-ui
  $ git merge origin/develop
  # CONFLICT!
  $ # ... resolve ...
  $ git add . && git commit -m "Merge develop, resolve conflicts"
  $ git push
  # PR không còn conflict

Person A:
  - Merge PR #3
  - Review và merge PR #4


Ngày 5 (Release):
────────────────────────────────────────────────────────────────────

Person A:
  $ git checkout develop && git pull
  $ git checkout main
  $ git merge develop
  $ git tag -a v1.0.0 -m "Release version 1.0.0"
  $ git push origin main --tags

All:
  $ git checkout develop && git pull
  $ git checkout main && git pull
  $ git branch -d feat/xxx  # Xóa local branches
```

### Conflict Scenarios trong Simulation

```
Scenario 1: Person C và D cùng sửa package.json
──────────────────────────────────────────────────

Person C: Thêm dependency express
Person D: Thêm dependency react

Solution:
- Person merge trước không có vấn đề
- Person merge sau resolve bằng cách thêm cả 2 dependencies


Scenario 2: Person E làm việc trên code chưa merge
──────────────────────────────────────────────────

Person E cần UI components từ Person D
Nhưng Person D chưa merge

Solution A:
- E đợi D merge
- Pull develop
- Tiếp tục làm

Solution B (nếu gấp):
- E tạo branch từ D's branch
  $ git checkout feat/todo-ui
  $ git checkout -b feat/todo-list
- Sau khi D merge, rebase:
  $ git rebase develop


Scenario 3: Bug phát hiện sau merge
──────────────────────────────────────────────────

Person A phát hiện bug trong todo-model

Solution:
$ git checkout develop
$ git checkout -b fix/todo-model-bug
$ # ... fix ...
$ git push -u origin fix/todo-model-bug
$ # Tạo PR → develop
```

---

## 📋 Checklist Hoàn Thành

### Junior Level
```
□ Bài 1: Clone và Commit
□ Bài 2: Tạo Branch và Merge
□ Bài 3: Git Stash
□ Bài 4: Undo Mistakes
```

### Middle Level
```
□ Bài 5: Xử Lý Conflict
□ Bài 6: Interactive Rebase
□ Bài 7: Cherry-pick
```

### Senior/Git Master Level
```
□ Bài 8: Revert và Reset Nâng Cao
□ Bài 9: Git Bisect
□ Bài 10: Cleanup Repository
□ Team Simulation completed
```

---

## ➡️ Bước Tiếp Theo

Học file **08-advanced-git.md** - Git Nâng Cao

---

*Tài liệu thuộc bộ Git Training Documentation v1.0*
