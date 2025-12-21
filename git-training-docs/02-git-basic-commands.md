# 02 - Git Basic Commands: Các Lệnh Cơ Bản Hàng Ngày

> **Đối tượng:** Junior mới onboard
> **Thời gian học:** 1-2 giờ
> **Mục tiêu:** Thành thạo các lệnh Git dùng hàng ngày

---

## 📖 Mục Lục

1. [Khởi Tạo & Clone Repo](#1-khởi-tạo--clone-repo)
2. [Vòng Đời Của File Trong Git](#2-vòng-đời-của-file-trong-git)
3. [Các Lệnh Cơ Bản Hàng Ngày](#3-các-lệnh-cơ-bản-hàng-ngày)
4. [Đọc Lịch Sử Git](#4-đọc-lịch-sử-git)
5. [Làm Việc Với Remote](#5-làm-việc-với-remote)
6. [Lỗi Newbie Hay Gặp](#6-lỗi-newbie-hay-gặp)

---

## 1. Khởi Tạo & Clone Repo

### 1.1. Clone repo có sẵn (90% trường hợp)

```bash
# Clone từ GitHub/GitLab
git clone git@github.com:company/project.git

# Clone với tên thư mục khác
git clone git@github.com:company/project.git my-project

# Clone 1 branch cụ thể
git clone -b develop git@github.com:company/project.git
```

✅ **Best practice:** Clone bằng SSH, không dùng HTTPS

### 1.2. Tạo repo mới (hiếm khi dùng)

```bash
# Trong thư mục dự án
cd my-project
git init

# Sau đó kết nối với remote
git remote add origin git@github.com:company/project.git
```

---

## 2. Vòng Đời Của File Trong Git

### Sơ đồ trạng thái file

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌───────────┐  │
│  │ Untracked │───▶│ Modified  │───▶│  Staged   │───▶│ Committed │  │
│  │           │    │           │    │           │    │           │  │
│  └───────────┘    └───────────┘    └───────────┘    └───────────┘  │
│       │                │                │                │          │
│       │    git add     │    git add     │   git commit   │          │
│       └────────────────┴────────────────┴────────────────┘          │
│                                                                     │
│   File mới        File đã sửa       Đã đánh dấu      Đã lưu vào    │
│   tạo ra          so với commit     để commit        lịch sử       │
│                   cuối                                              │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Giải thích từng trạng thái

| Trạng thái | Ý nghĩa | Ví dụ |
|------------|---------|-------|
| **Untracked** | File mới, Git chưa biết | Tạo file `new.js` |
| **Modified** | File đã sửa so với commit cuối | Sửa `app.js` |
| **Staged** | Đã đánh dấu để commit | `git add app.js` |
| **Committed** | Đã lưu vào lịch sử | `git commit` |

### Working Directory vs Staging Area vs Repository

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│   Working Directory        Staging Area         Local Repository   │
│   (Thư mục làm việc)       (Khu vực chờ)        (Lịch sử Git)     │
│                                                                     │
│   ┌─────────────┐         ┌─────────────┐      ┌─────────────┐     │
│   │   app.js    │         │   app.js    │      │  Commit 3   │     │
│   │   (sửa đổi) │ ──────▶ │   (staged)  │ ───▶ │  Commit 2   │     │
│   │             │ git add │             │      │  Commit 1   │     │
│   └─────────────┘         └─────────────┘      └─────────────┘     │
│                                   │                                 │
│                              git commit                            │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

💡 **Tại sao cần Staging Area?**
- Cho phép commit từng phần thay đổi
- Review trước khi commit
- Tách riêng các thay đổi không liên quan

---

## 3. Các Lệnh Cơ Bản Hàng Ngày

### 3.1. git status - Xem trạng thái

```bash
git status
```

**Output mẫu:**

```
On branch feature/login
Your branch is up to date with 'origin/feature/login'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   src/auth.js        ← Đã staged, sẵn sàng commit

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   src/app.js         ← Đã sửa, chưa staged

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        src/new-feature.js             ← File mới, Git chưa theo dõi
```

✅ **Tip:** Chạy `git status` TRƯỚC mỗi thao tác để biết đang ở đâu

### 3.2. git add - Đưa file vào Staging

```bash
# Add 1 file cụ thể
git add src/app.js

# Add nhiều file
git add src/app.js src/auth.js

# Add tất cả file thay đổi trong thư mục hiện tại
git add .

# Add tất cả file thay đổi (toàn repo)
git add -A

# Add từng phần của file (interactive)
git add -p
```

⚠️ **Cẩn thận với `git add .`**
- Có thể add nhầm file không mong muốn
- Luôn chạy `git status` để kiểm tra trước

### 3.3. git commit - Lưu thay đổi

```bash
# Commit với message ngắn
git commit -m "Add login feature"

# Commit với message dài (mở editor)
git commit

# Add và commit luôn (CHỈ cho file đã tracked)
git commit -am "Fix typo in login page"
```

### Viết Commit Message Tốt

**Cấu trúc chuẩn:**

```
<type>: <subject>

[body - optional]

[footer - optional]
```

**Các type phổ biến:**

| Type | Dùng khi |
|------|----------|
| `feat` | Thêm tính năng mới |
| `fix` | Sửa bug |
| `docs` | Thay đổi documentation |
| `style` | Format code, không thay đổi logic |
| `refactor` | Refactor code |
| `test` | Thêm/sửa test |
| `chore` | Cập nhật build, config |

**Ví dụ commit message tốt:**

```bash
# Ngắn gọn
git commit -m "feat: add user login with Google OAuth"
git commit -m "fix: resolve null pointer in checkout flow"
git commit -m "docs: update API documentation for v2"

# Có body
git commit -m "feat: add password reset feature

- Add forgot password form
- Send reset email via SendGrid
- Token expires after 24 hours

Closes #123"
```

**❌ Commit message XẤU:**

```bash
git commit -m "fix bug"           # Bug gì?
git commit -m "update"            # Update gì?
git commit -m "."                 # ???
git commit -m "asdfasdf"          # Nghiêm túc không?
git commit -m "WIP"               # Commit WIP không được push!
```

### 3.4. git diff - Xem thay đổi

```bash
# Xem thay đổi chưa staged
git diff

# Xem thay đổi đã staged (sẽ vào commit tiếp)
git diff --staged
# hoặc
git diff --cached

# So sánh với commit cụ thể
git diff abc1234

# So sánh 2 branch
git diff main..feature/login

# Chỉ xem tên file thay đổi
git diff --name-only
```

**Output mẫu:**

```diff
diff --git a/src/app.js b/src/app.js
index 1234567..abcdefg 100644
--- a/src/app.js
+++ b/src/app.js
@@ -10,7 +10,7 @@ function login() {
   const user = getUser();
-  console.log("Old log");        ← Dòng bị xóa (màu đỏ)
+  console.log("New log");        ← Dòng được thêm (màu xanh)
   return user;
 }
```

### 3.5. git restore - Hủy thay đổi

```bash
# Hủy thay đổi trong working directory (CHƯA staged)
git restore src/app.js

# Hủy tất cả thay đổi
git restore .

# Bỏ file khỏi staging (đã git add nhưng muốn bỏ ra)
git restore --staged src/app.js

# Khôi phục file từ commit cụ thể
git restore --source=abc1234 src/app.js
```

⚠️ **Cảnh báo:** `git restore` không thể undo. Code sẽ MẤT VĨNH VIỄN!

---

## 4. Đọc Lịch Sử Git

### 4.1. git log - Xem lịch sử commit

```bash
# Log cơ bản
git log

# Log 1 dòng mỗi commit
git log --oneline

# Log với graph (cây nhánh)
git log --oneline --graph

# Log với graph và tất cả branch
git log --oneline --graph --all

# Log N commit gần nhất
git log -5

# Log của 1 file cụ thể
git log -- src/app.js

# Log với diff
git log -p

# Log tìm kiếm theo message
git log --grep="login"

# Log tìm kiếm theo author
git log --author="Nguyen"
```

**Output mẫu `git log --oneline --graph`:**

```
* 7a8b9c0 (HEAD -> feature/login) feat: add remember me checkbox
* 1d2e3f4 feat: add login form validation
* 5g6h7i8 feat: create login page UI
|
| * 9j0k1l2 (feature/signup) feat: add signup form
| * 3m4n5o6 feat: create signup page
|/
* 7p8q9r0 (origin/main, main) Initial commit
```

### 4.2. Đọc Git Tree

```
          main
            │
    ────●───●───●─────────────────●───────────▶
        │       │                 │
        │       │ feature/login   │
        │       └────●────●───────┘
        │            A    B   (merge)
        │
        │ feature/signup
        └──────●────●────▶
               C    D   (chưa merge)

Giải thích:
●  = Commit
─▶ = Hướng phát triển
│  = Kết nối giữa các commit
```

### 4.3. git show - Xem chi tiết 1 commit

```bash
# Xem commit mới nhất
git show

# Xem commit cụ thể
git show abc1234

# Chỉ xem thông tin, không xem diff
git show --stat abc1234

# Xem file cụ thể trong commit
git show abc1234:src/app.js
```

### 4.4. git blame - Ai viết dòng này?

```bash
# Xem ai viết từng dòng
git blame src/app.js

# Xem từ dòng 10 đến 20
git blame -L 10,20 src/app.js
```

**Output mẫu:**

```
7a8b9c0d (Nguyen Van A 2024-01-15 10:30:00 +0700  1) function login() {
1d2e3f4g (Tran Van B  2024-01-14 09:20:00 +0700  2)   const user = getUser();
5g6h7i8j (Nguyen Van A 2024-01-15 11:00:00 +0700  3)   validateUser(user);
```

---

## 5. Làm Việc Với Remote

### 5.1. git remote - Quản lý remote

```bash
# Xem danh sách remote
git remote -v

# Thêm remote
git remote add origin git@github.com:company/project.git

# Đổi URL remote
git remote set-url origin git@github.com:company/new-project.git

# Xóa remote
git remote remove origin
```

### 5.2. git fetch - Lấy thông tin từ remote

```bash
# Fetch tất cả branch
git fetch

# Fetch branch cụ thể
git fetch origin main

# Fetch và xóa branch đã bị xóa trên remote
git fetch --prune
```

💡 `fetch` chỉ tải về, KHÔNG tự động merge

### 5.3. git pull - Lấy code và merge

```bash
# Pull branch hiện tại
git pull

# Pull branch cụ thể
git pull origin main

# Pull với rebase (sạch hơn merge)
git pull --rebase
```

**So sánh pull vs fetch:**

```
git fetch                    git pull
    │                            │
    ▼                            ▼
Tải về local            Tải về + Merge tự động
(không merge)

Khi nào dùng:            Khi nào dùng:
- Muốn xem trước         - Cập nhật nhanh
- Cẩn thận               - Tin tưởng remote
```

### 5.4. git push - Đẩy code lên remote

```bash
# Push branch hiện tại
git push

# Push lần đầu (set upstream)
git push -u origin feature/login

# Push tất cả branch
git push --all

# Push tags
git push --tags
```

⚠️ **Cấm push với force trên shared branch:**

```bash
# ❌ KHÔNG ĐƯỢC LÀM (trừ khi Tech Lead cho phép)
git push --force
git push -f
```

### Quy trình làm việc hàng ngày

```
┌─────────────────────────────────────────────────────────────────────┐
│                   QUY TRÌNH HÀNG NGÀY                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Đầu ngày:                                                       │
│     $ git pull                    ← Lấy code mới nhất              │
│                                                                     │
│  2. Làm việc:                                                       │
│     $ git status                  ← Xem đang ở đâu                 │
│     (... code ...)                                                  │
│     $ git status                  ← Xem đã thay đổi gì             │
│     $ git diff                    ← Review thay đổi                │
│                                                                     │
│  3. Commit:                                                         │
│     $ git add .                   ← Stage thay đổi                 │
│     $ git commit -m "message"     ← Commit                         │
│                                                                     │
│  4. Push:                                                           │
│     $ git push                    ← Đẩy lên remote                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 6. Lỗi Newbie Hay Gặp

### 6.1. Commit nhầm file

**Tình huống:** Commit nhầm file `.env` hoặc `node_modules`

```bash
# Nếu CHƯA push
git reset HEAD~1                    # Undo commit, giữ thay đổi
git restore --staged .env           # Bỏ file khỏi staging
git commit -m "message"             # Commit lại

# Nếu ĐÃ push (cần thận trọng)
# → Hỏi Git Master
```

### 6.2. Commit message sai

```bash
# Sửa commit message cuối (chưa push)
git commit --amend -m "New message"

# Đã push? → KHÔNG SỬA, để nguyên
```

### 6.3. Quên tạo branch mới

**Tình huống:** Code trên `main` thay vì tạo feature branch

```bash
# Tạo branch mới và chuyển sang
git checkout -b feature/my-feature

# Bây giờ commit sẽ vào branch mới
# main vẫn sạch
```

### 6.4. Pull bị conflict

```bash
# Khi pull báo conflict
$ git pull
CONFLICT (content): Merge conflict in src/app.js

# 1. Mở file conflict
# 2. Tìm và sửa các đoạn:
<<<<<<< HEAD
  (code của bạn)
=======
  (code trên remote)
>>>>>>> origin/main

# 3. Sau khi sửa xong
git add src/app.js
git commit -m "Resolve merge conflict"
```

### 6.5. Push bị reject

```bash
# Lỗi: push bị reject
$ git push
! [rejected]        main -> main (non-fast-forward)

# Nguyên nhân: Remote có commit mới mà local chưa có

# Giải quyết:
git pull --rebase
git push
```

### 6.6. Sửa nhầm file

```bash
# Hủy tất cả thay đổi chưa commit
git restore .

# Hủy 1 file cụ thể
git restore src/app.js
```

⚠️ **Cảnh báo:** Lệnh này KHÔNG thể undo!

### 6.7. Commit chưa xong việc

**Tình huống:** Đang code dở, cần chuyển branch gấp

```bash
# Lưu tạm thay đổi
git stash

# Chuyển branch
git checkout other-branch
# ... làm việc ...

# Quay lại và lấy code đã stash
git checkout feature/my-feature
git stash pop
```

---

## 📋 Tóm Tắt Lệnh

### Lệnh hay dùng nhất

| Lệnh | Chức năng |
|------|-----------|
| `git status` | Xem trạng thái |
| `git add .` | Stage tất cả |
| `git commit -m "msg"` | Commit |
| `git pull` | Lấy code mới |
| `git push` | Đẩy code lên |
| `git log --oneline` | Xem lịch sử |

### Lệnh cần nhớ

| Lệnh | Chức năng |
|------|-----------|
| `git diff` | Xem thay đổi |
| `git restore file` | Hủy thay đổi |
| `git stash` | Lưu tạm |
| `git branch` | Xem branch |
| `git checkout -b name` | Tạo branch mới |

---

## ✅ Checklist Trước Khi Tiếp Tục

```
□ Biết clone repo
□ Hiểu vòng đời file (Untracked → Modified → Staged → Committed)
□ Thành thạo: status, add, commit, pull, push
□ Biết đọc git log
□ Biết xử lý các lỗi cơ bản
```

---

## ➡️ Bước Tiếp Theo

Học file **03-git-workflow-basics.md** - Workflow cơ bản cho cá nhân

---

*Tài liệu thuộc bộ Git Training Documentation v1.0*
