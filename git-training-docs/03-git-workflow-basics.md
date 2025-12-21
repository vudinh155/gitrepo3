# 03 - Git Workflow Basics: Quy Trình Làm Việc Cá Nhân

> **Đối tượng:** Junior mới onboard
> **Thời gian học:** 1 giờ
> **Mục tiêu:** Nắm vững quy trình làm việc Git hàng ngày

---

## 📖 Mục Lục

1. [Tổng Quan Workflow](#1-tổng-quan-workflow)
2. [Bước 1: Chuẩn Bị Trước Khi Code](#2-bước-1-chuẩn-bị-trước-khi-code)
3. [Bước 2: Tạo Branch](#3-bước-2-tạo-branch)
4. [Bước 3: Code và Commit](#4-bước-3-code-và-commit)
5. [Bước 4: Push và Tạo PR](#5-bước-4-push-và-tạo-pr)
6. [Bước 5: Sau Khi Merge](#6-bước-5-sau-khi-merge)
7. [Xử Lý Tình Huống](#7-xử-lý-tình-huống)

---

## 1. Tổng Quan Workflow

### Sơ đồ quy trình chuẩn

```
┌─────────────────────────────────────────────────────────────────────┐
│                      WORKFLOW CÁ NHÂN                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌───────────┐ │
│   │  1. PULL  │───▶│ 2. BRANCH │───▶│  3. CODE  │───▶│ 4. PUSH   │ │
│   │  cập nhật │    │  tạo mới  │    │  & commit │    │  & PR     │ │
│   └───────────┘    └───────────┘    └───────────┘    └───────────┘ │
│         │                                                  │        │
│         └──────────────────────────────────────────────────┘        │
│                          5. CLEANUP                                 │
│                      (sau khi merge)                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Nguyên tắc vàng

```
┌─────────────────────────────────────────────────────────────────────┐
│                     5 NGUYÊN TẮC VÀNG                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1️⃣  LUÔN pull trước khi bắt đầu làm việc                          │
│                                                                     │
│  2️⃣  LUÔN tạo branch mới cho mỗi task                              │
│                                                                     │
│  3️⃣  KHÔNG BAO GIỜ code trực tiếp trên main/develop                │
│                                                                     │
│  4️⃣  Commit thường xuyên, message rõ ràng                          │
│                                                                     │
│  5️⃣  Push và tạo PR, KHÔNG merge tự ý                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Bước 1: Chuẩn Bị Trước Khi Code

### Checklist đầu ngày

```bash
# 1. Chuyển về branch chính
git checkout develop
# hoặc
git checkout main

# 2. Lấy code mới nhất
git pull origin develop

# 3. Kiểm tra trạng thái
git status
# Phải thấy: "Your branch is up to date..."

# 4. Xem các branch đang có
git branch -a
```

### ⚠️ Lỗi hay gặp: Quên pull

```
❌ SAI:
$ git checkout develop
$ git checkout -b feature/login    ← Tạo branch từ code CŨ!

✅ ĐÚNG:
$ git checkout develop
$ git pull origin develop           ← Pull trước!
$ git checkout -b feature/login    ← Tạo branch từ code MỚI
```

---

## 3. Bước 2: Tạo Branch

### Quy tắc đặt tên branch

```
<type>/<short-description>

Ví dụ:
feat/user-login
feat/add-shopping-cart
fix/login-validation-error
fix/cart-total-calculation
hotfix/security-patch
```

| Type | Dùng khi | Ví dụ |
|------|----------|-------|
| `feat/` | Tính năng mới | `feat/add-payment` |
| `fix/` | Sửa bug | `fix/login-error` |
| `hotfix/` | Sửa bug khẩn cấp | `hotfix/security-fix` |
| `refactor/` | Cải thiện code | `refactor/cleanup-auth` |
| `docs/` | Tài liệu | `docs/api-guide` |
| `test/` | Thêm test | `test/auth-unit-tests` |

### Tạo branch mới

```bash
# Cách 1: Tạo và chuyển sang branch mới
git checkout -b feat/user-login

# Cách 2: Tạo branch (không chuyển)
git branch feat/user-login
git checkout feat/user-login

# Cách 3: Dùng switch (Git 2.23+)
git switch -c feat/user-login
```

### Kiểm tra branch

```bash
# Xem branch hiện tại
git branch
# Output:
#   develop
# * feat/user-login    ← Dấu * = branch hiện tại

# Xem tất cả branch (cả remote)
git branch -a
```

### ✅ Best practices

```
✅ Tên ngắn gọn, mô tả được việc đang làm
   feat/user-login

❌ Tên quá dài
   feat/add-user-login-feature-with-google-oauth-and-remember-me

❌ Tên không rõ nghĩa
   feat/abc
   fix/bug

❌ Có khoảng trắng hoặc ký tự đặc biệt
   feat/user login
   feat/user_login!
```

---

## 4. Bước 3: Code và Commit

### Quy trình code-commit

```
┌─────────────────────────────────────────────────────────────────────┐
│                      CHU KỲ CODE-COMMIT                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│                    ┌─────────────┐                                  │
│              ┌────▶│    CODE     │────┐                             │
│              │     │  (15-30p)   │    │                             │
│              │     └─────────────┘    │                             │
│              │                        ▼                             │
│     ┌─────────────┐            ┌─────────────┐                      │
│     │   COMMIT    │◀───────────│   REVIEW    │                      │
│     │             │            │  git diff   │                      │
│     └─────────────┘            └─────────────┘                      │
│                                                                     │
│  → Commit thường xuyên (mỗi 15-30 phút hoặc mỗi task nhỏ)          │
│  → Mỗi commit = 1 thay đổi logic                                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Ví dụ thực tế

```bash
# === Bước 1: Code UI ===
# (Viết code giao diện login...)

# Xem đã thay đổi gì
git status
git diff

# Stage và commit
git add src/components/LoginForm.jsx
git commit -m "feat: add login form UI"

# === Bước 2: Code validation ===
# (Thêm validation...)

git status
git diff

git add src/components/LoginForm.jsx
git commit -m "feat: add form validation for login"

# === Bước 3: Code API call ===
# (Thêm gọi API...)

git add src/services/auth.js src/components/LoginForm.jsx
git commit -m "feat: integrate login API"
```

### Khi nào nên commit?

```
✅ NÊN commit khi:
├── Hoàn thành 1 chức năng nhỏ
├── Code chạy được (không broken)
├── Trước khi thử nghiệm thay đổi lớn
├── Cuối ngày làm việc
└── Trước khi nghỉ trưa

❌ KHÔNG NÊN commit khi:
├── Code đang broken
├── Chưa test
├── Commit quá to (> 500 dòng)
└── Message không rõ ràng
```

### Commit message template

```bash
# Format chuẩn
<type>: <mô tả ngắn gọn>

# Ví dụ
feat: add login form with email/password
fix: resolve null error when user not found
refactor: extract validation to separate hook
docs: add API documentation for auth endpoints
test: add unit tests for login validation
```

### Git stash - Lưu tạm khi cần

```bash
# Tình huống: Đang code dở, có bug cần fix gấp

# 1. Lưu tạm code đang làm
git stash
# hoặc với message
git stash save "WIP: login form"

# 2. Chuyển sang fix bug
git checkout fix/urgent-bug
# ... fix bug ...
git checkout feat/user-login

# 3. Lấy lại code đang làm
git stash pop

# Xem danh sách stash
git stash list

# Xóa stash cụ thể
git stash drop stash@{0}
```

---

## 5. Bước 4: Push và Tạo PR

### Push lên remote

```bash
# Push lần đầu (set upstream)
git push -u origin feat/user-login

# Các lần sau
git push
```

### Tạo Pull Request

```
┌─────────────────────────────────────────────────────────────────────┐
│                    TẠO PULL REQUEST                                │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Vào GitHub/GitLab                                               │
│                                                                     │
│  2. Click "New Pull Request" / "Create Merge Request"              │
│                                                                     │
│  3. Chọn:                                                           │
│     - Base branch: develop (hoặc main)                             │
│     - Compare branch: feat/user-login                              │
│                                                                     │
│  4. Điền thông tin:                                                 │
│     - Title: [FEAT] Add user login feature                         │
│     - Description: Mô tả chi tiết                                   │
│     - Reviewers: Chọn người review                                 │
│     - Labels: feature, in-review...                                 │
│                                                                     │
│  5. Click "Create Pull Request"                                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### PR Description template

```markdown
## What does this PR do?
Add user login feature with email/password

## Changes
- Add LoginForm component
- Add form validation
- Integrate with auth API
- Add error handling

## How to test
1. Go to /login
2. Enter email and password
3. Click Submit
4. Should redirect to dashboard

## Screenshots (if UI changes)
[Attach screenshots]

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-reviewed
- [ ] Added tests
- [ ] Documentation updated
```

### Sau khi tạo PR

```bash
# Nếu reviewer yêu cầu sửa:

# 1. Sửa code theo feedback
# 2. Commit
git add .
git commit -m "fix: address PR feedback"

# 3. Push
git push

# PR tự động cập nhật
```

---

## 6. Bước 5: Sau Khi Merge

### Dọn dẹp branch

```bash
# 1. Chuyển về branch chính
git checkout develop

# 2. Pull code mới (bao gồm PR vừa merge)
git pull origin develop

# 3. Xóa branch local
git branch -d feat/user-login

# 4. Xóa branch remote (nếu chưa tự xóa)
git push origin --delete feat/user-login

# 5. Dọn các branch đã bị xóa trên remote
git fetch --prune
```

### Script dọn dẹp nhanh

```bash
# Xóa tất cả branch đã merge
git branch --merged | grep -v "main\|develop" | xargs git branch -d
```

### Workflow hoàn chỉnh - Ví dụ

```bash
# ======= NGÀY 1: BẮT ĐẦU TASK =======

# 1. Cập nhật code
git checkout develop
git pull origin develop

# 2. Tạo branch mới
git checkout -b feat/add-cart

# 3. Code và commit
# ... code ...
git add .
git commit -m "feat: add cart component"
# ... code tiếp ...
git add .
git commit -m "feat: add item to cart"

# 4. Push lên remote
git push -u origin feat/add-cart

# ======= NGÀY 2: TIẾP TỤC =======

# 1. Cập nhật branch hiện tại với develop mới
git checkout develop
git pull origin develop
git checkout feat/add-cart
git merge develop              # Merge develop vào branch

# 2. Tiếp tục code
# ... code ...
git add .
git commit -m "feat: calculate cart total"
git push

# ======= NGÀY 3: TẠO PR =======

# 1. Đảm bảo code mới nhất
git checkout develop
git pull origin develop
git checkout feat/add-cart
git merge develop              # Giải quyết conflict nếu có
git push

# 2. Tạo PR trên GitHub

# ======= SAU KHI PR MERGED =======

git checkout develop
git pull origin develop
git branch -d feat/add-cart
```

---

## 7. Xử Lý Tình Huống

### 7.1. Tạo branch nhầm từ branch khác

```bash
# Tình huống: Tạo branch từ feat/old thay vì develop

# Cách 1: Nếu chưa commit gì
git checkout develop
git checkout -b feat/correct-branch

# Cách 2: Nếu đã commit
git checkout develop
git cherry-pick abc1234        # Copy từng commit cần thiết
```

### 7.2. Commit nhầm vào branch khác

```bash
# Tình huống: Commit nhầm vào develop

# 1. Lưu commit hash
git log -1                     # Copy hash, vd: abc1234

# 2. Undo commit trên develop
git reset HEAD~1

# 3. Stash changes
git stash

# 4. Tạo branch đúng
git checkout -b feat/correct-branch

# 5. Lấy lại changes
git stash pop
git add .
git commit -m "correct message"
```

### 7.3. Cập nhật branch với code mới từ develop

```bash
# Cách 1: Merge (an toàn, giữ history)
git checkout feat/my-feature
git merge develop

# Cách 2: Rebase (sạch hơn, nhưng cần cẩn thận)
git checkout feat/my-feature
git rebase develop

# Sau rebase, cần force push
git push --force-with-lease
```

### 7.4. Muốn undo commit cuối

```bash
# Undo nhưng giữ thay đổi (staging area)
git reset --soft HEAD~1

# Undo nhưng giữ thay đổi (working directory)
git reset HEAD~1

# Undo hoàn toàn (XÓA thay đổi)
git reset --hard HEAD~1        # ⚠️ Cẩn thận!
```

### 7.5. File .gitignore không hoạt động

```bash
# Nguyên nhân: File đã được track trước khi thêm vào .gitignore

# Giải quyết:
git rm -r --cached node_modules
git add .
git commit -m "chore: remove node_modules from tracking"
```

---

## 📋 Quick Reference

### Quy trình 5 bước

```
1. git checkout develop && git pull
2. git checkout -b feat/xxx
3. (code) → git add . → git commit
4. git push -u origin feat/xxx → Tạo PR
5. (sau merge) → git checkout develop && git pull && git branch -d feat/xxx
```

### Lệnh hay dùng

| Tình huống | Lệnh |
|------------|------|
| Bắt đầu ngày | `git checkout develop && git pull` |
| Tạo branch | `git checkout -b feat/xxx` |
| Commit | `git add . && git commit -m "message"` |
| Push lần đầu | `git push -u origin feat/xxx` |
| Cập nhật từ develop | `git merge develop` |
| Lưu tạm | `git stash` |
| Lấy lại | `git stash pop` |

---

## ✅ Checklist Workflow

```
□ Luôn pull trước khi tạo branch mới
□ Đặt tên branch theo quy tắc
□ Commit thường xuyên với message rõ ràng
□ Push và tạo PR, không merge tự ý
□ Dọn dẹp branch sau khi merge
```

---

## ➡️ Bước Tiếp Theo

Học file **04-team-workflow.md** - Quy trình làm việc nhóm

---

*Tài liệu thuộc bộ Git Training Documentation v1.0*
