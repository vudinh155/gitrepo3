# 10 - Git Anti-patterns: Những Sai Lầm Cần Tránh

> **Đối tượng:** Tất cả thành viên team
> **Thời gian học:** 30-45 phút
> **Mục tiêu:** Nhận biết và tránh các sai lầm phổ biến khi dùng Git

---

## 📖 Mục Lục

1. [Anti-patterns Về Commit](#1-anti-patterns-về-commit)
2. [Anti-patterns Về Branch](#2-anti-patterns-về-branch)
3. [Anti-patterns Về Merge/Rebase](#3-anti-patterns-về-mergerebase)
4. [Anti-patterns Về History](#4-anti-patterns-về-history)
5. [Anti-patterns Về Workflow](#5-anti-patterns-về-workflow)
6. [Anti-patterns Về Security](#6-anti-patterns-về-security)
7. [Các Tình Huống Thực Tế](#7-các-tình-huống-thực-tế)

---

## 1. Anti-patterns Về Commit

### ❌ Anti-pattern #1: Commit message không rõ ràng

```bash
# ❌ SAI - Không ai hiểu commit làm gì
git commit -m "fix"
git commit -m "update"
git commit -m "."
git commit -m "asdf"
git commit -m "WIP"
git commit -m "changes"
git commit -m "done"

# ✅ ĐÚNG - Rõ ràng, cụ thể
git commit -m "fix: resolve null pointer in login validation"
git commit -m "feat: add password strength indicator"
git commit -m "refactor: extract auth logic to separate hook"
```

### ❌ Anti-pattern #2: Commit quá lớn

```
❌ SAI:
Commit abc123 - "Add entire payment feature"
├── 50 files changed
├── 3000 lines added
├── 500 lines deleted
└── Làm sao review được???

✅ ĐÚNG:
Commit 1: "feat: add payment form UI"          (200 lines)
Commit 2: "feat: add form validation"          (100 lines)
Commit 3: "feat: integrate Stripe API"         (300 lines)
Commit 4: "test: add payment tests"            (200 lines)
```

### ❌ Anti-pattern #3: Commit code broken

```bash
# ❌ SAI - Commit code không chạy được
git commit -m "WIP: half done feature"
# Code lỗi syntax, tests fail, build fail

# ✅ ĐÚNG - Mỗi commit phải chạy được
# Nếu chưa xong, dùng stash hoặc WIP branch riêng
git stash save "WIP: feature in progress"
```

### ❌ Anti-pattern #4: Commit files không cần thiết

```bash
# ❌ SAI - Commit nhầm files
git add .
git commit -m "feat: add feature"
# Bao gồm: node_modules/, .env, .DS_Store, *.log

# ✅ ĐÚNG - Review trước khi commit
git status                    # Xem có gì
git diff --staged             # Xem nội dung
git add <specific-files>      # Add cụ thể

# Sử dụng .gitignore đúng cách
echo "node_modules/" >> .gitignore
echo ".env" >> .gitignore
echo ".DS_Store" >> .gitignore
echo "*.log" >> .gitignore
```

---

## 2. Anti-patterns Về Branch

### ❌ Anti-pattern #5: Code trực tiếp trên main/develop

```bash
# ❌ SAI - Push thẳng vào main
git checkout main
# ... code ...
git add .
git commit -m "quick fix"
git push origin main
# → Không review, có thể break production!

# ✅ ĐÚNG - Luôn tạo branch
git checkout -b fix/quick-fix
# ... code ...
git add .
git commit -m "fix: quick fix"
git push -u origin fix/quick-fix
# → Tạo PR, review, rồi merge
```

### ❌ Anti-pattern #6: Branch sống quá lâu

```
❌ SAI:
feat/big-feature created: 2024-01-01
Last activity: 2024-03-15 (2.5 tháng!)
├── 150 commits
├── Conflict với develop: 50 files
└── Không ai dám merge

✅ ĐÚNG:
- Branch tối đa 1 tuần
- Chia feature lớn thành nhiều branch nhỏ
- Merge thường xuyên
```

### ❌ Anti-pattern #7: Đặt tên branch không theo quy ước

```bash
# ❌ SAI
git checkout -b test
git checkout -b my-changes
git checkout -b feature
git checkout -b john-branch
git checkout -b asdf123

# ✅ ĐÚNG
git checkout -b feat/user-login
git checkout -b fix/null-pointer-error
git checkout -b hotfix/security-patch
git checkout -b refactor/cleanup-auth
```

### ❌ Anti-pattern #8: Không xóa branch sau khi merge

```bash
# ❌ SAI - Để branch chất đống
git branch -a
# 100+ branches cũ, không ai biết cái nào còn dùng

# ✅ ĐÚNG - Xóa ngay sau merge
git branch -d feat/completed-feature
git push origin --delete feat/completed-feature

# Dọn dẹp định kỳ
git fetch --prune
git branch --merged | grep -v "main\|develop" | xargs git branch -d
```

---

## 3. Anti-patterns Về Merge/Rebase

### ❌ Anti-pattern #9: Force push lên shared branch

```bash
# ❌ CỰC KỲ NGUY HIỂM
git push --force origin develop
git push -f origin main
# → Xóa mất code của người khác!

# ⚠️ Chỉ dùng khi:
# 1. Branch CHỈ mình làm
# 2. Và đã thông báo team
git push --force-with-lease origin feat/my-feature  # An toàn hơn
```

### ❌ Anti-pattern #10: Rebase shared branch

```bash
# ❌ SAI - Rebase branch nhiều người dùng
git checkout develop
git rebase main
# → Thay đổi history của develop!

# ✅ ĐÚNG - Chỉ rebase branch cá nhân
git checkout feat/my-feature
git rebase develop
```

### ❌ Anti-pattern #11: Merge không update branch trước

```bash
# ❌ SAI
git checkout feat/my-feature
# (không pull develop mới)
git checkout develop
git merge feat/my-feature
# → Conflict xảy ra trên develop!

# ✅ ĐÚNG
git checkout develop
git pull origin develop
git checkout feat/my-feature
git merge develop           # Resolve conflict ở đây
git checkout develop
git merge feat/my-feature   # Clean merge
```

### ❌ Anti-pattern #12: Chọn bừa khi resolve conflict

```bash
# ❌ SAI - Không hiểu code, chọn đại
git checkout --ours .       # "Chọn hết của mình cho chắc"
git checkout --theirs .     # "Chọn hết của họ cho xong"
# → Mất code quan trọng!

# ✅ ĐÚNG
# 1. Đọc hiểu cả 2 versions
# 2. Hỏi author nếu không hiểu
# 3. Kết hợp logic đúng
# 4. Test sau khi resolve
```

---

## 4. Anti-patterns Về History

### ❌ Anti-pattern #13: Amend commit đã push

```bash
# ❌ SAI
git commit -m "feature"
git push origin feat/xxx
# Oops, quên file!
git add forgotten.js
git commit --amend
git push --force origin feat/xxx
# → Nếu ai đó đã pull, họ sẽ gặp vấn đề!

# ✅ ĐÚNG
git add forgotten.js
git commit -m "feat: add forgotten file"
git push origin feat/xxx
# Tạo commit mới thay vì amend
```

### ❌ Anti-pattern #14: Thay đổi public history

```bash
# ❌ SAI - Rebase interactive trên commits đã push
git rebase -i HEAD~10
# Squash, reorder, delete commits
git push --force
# → Làm loạn history của người khác!

# ✅ ĐÚNG
# Chỉ rebase commits CHƯA push
# Hoặc thông báo team và họ đồng ý
```

### ❌ Anti-pattern #15: Xóa .git

```bash
# ❌ CỰC KỲ SAI
rm -rf .git
git init
git add .
git commit -m "fresh start"
# → Mất TOÀN BỘ lịch sử!

# ✅ ĐÚNG
# Nếu muốn cleanup:
git gc --aggressive
# Hoặc hỏi Git Master
```

---

## 5. Anti-patterns Về Workflow

### ❌ Anti-pattern #16: Không pull trước khi làm việc

```bash
# ❌ SAI
git checkout develop
# (không pull)
git checkout -b feat/xxx
# ... code dựa trên code CŨ ...
# Sau đó mới phát hiện develop đã khác

# ✅ ĐÚNG
git checkout develop
git pull origin develop     # BẮT BUỘC!
git checkout -b feat/xxx
```

### ❌ Anti-pattern #17: Merge tự ý không qua PR

```bash
# ❌ SAI
git checkout develop
git merge feat/my-feature
git push origin develop
# → Không ai review, có thể có bug!

# ✅ ĐÚNG
git push -u origin feat/my-feature
# Tạo PR trên GitHub/GitLab
# Chờ review và approval
# Merge qua UI (hoặc Git Master merge)
```

### ❌ Anti-pattern #18: Ignore CI failures

```
❌ SAI:
PR #123
├── ❌ ci/test: 5 tests failed
├── ❌ ci/lint: 10 errors
└── Developer: "Merge anyway, I'll fix later"
    → Bug lên production!

✅ ĐÚNG:
- CI fail = KHÔNG merge
- Fix all issues trước
- Re-run CI
- Chỉ merge khi tất cả green
```

### ❌ Anti-pattern #19: Không test sau merge

```
❌ SAI:
1. Merge PR
2. Deploy ngay
3. Discover bug in production
4. Panic!

✅ ĐÚNG:
1. Merge PR
2. Test trên staging/develop
3. QA verification
4. Deploy production
5. Smoke test
```

---

## 6. Anti-patterns Về Security

### ❌ Anti-pattern #20: Commit secrets

```bash
# ❌ CỰC KỲ NGUY HIỂM
git add .env
git commit -m "add config"
# File chứa: API_KEY=sk-secret-key-12345
# → Lộ credentials!

# Dù có xóa sau, vẫn còn trong history!
git rm .env
git commit -m "remove secrets"
# → Vẫn có thể tìm được bằng: git log -p

# ✅ ĐÚNG
# 1. Thêm vào .gitignore TRƯỚC KHI commit
echo ".env" >> .gitignore
echo "credentials.json" >> .gitignore

# 2. Dùng .env.example (không có values thật)
# 3. Sử dụng secret management (Vault, AWS Secrets...)

# Nếu đã commit nhầm:
# → Rotate tất cả secrets ngay lập tức!
# → Dùng git-filter-repo để xóa khỏi history
```

### ❌ Anti-pattern #21: Commit code với hardcoded passwords

```javascript
// ❌ SAI
const DB_PASSWORD = "super-secret-password";
const API_KEY = "sk-1234567890";

// ✅ ĐÚNG
const DB_PASSWORD = process.env.DB_PASSWORD;
const API_KEY = process.env.API_KEY;
```

### ❌ Anti-pattern #22: Public repo với private data

```
❌ SAI:
- Repo public
- Chứa database dumps
- Chứa user data
- Chứa internal docs

✅ ĐÚNG:
- Repo public chỉ chứa code
- Sensitive data ở private storage
- Dùng private repo cho internal projects
```

---

## 7. Các Tình Huống Thực Tế

### Tình huống 1: "Tôi push nhầm vào main!"

```bash
# Đừng panic! Có thể fix.

# Bước 1: Thông báo team ngay
# "Tôi vừa push nhầm vào main, đang fix"

# Bước 2: Revert (KHÔNG dùng reset/force)
git checkout main
git revert HEAD
git push origin main

# Bước 3: Tạo branch đúng cách
git checkout -b feat/my-feature HEAD~1
```

### Tình huống 2: "Tôi commit nhầm secrets!"

```bash
# KHẨN CẤP!

# Bước 1: Rotate ALL secrets ngay lập tức
# - Đổi API keys
# - Đổi passwords
# - Revoke tokens

# Bước 2: Xóa khỏi history
pip install git-filter-repo
git filter-repo --path .env --invert-paths --force

# Bước 3: Force push (được phép trong trường hợp này)
git push --force origin main

# Bước 4: Thông báo team
# Bước 5: Review và tạo pre-commit hook
```

### Tình huống 3: "Branch của tôi quá lâu, quá nhiều conflict!"

```bash
# Bước 1: Backup branch
git checkout feat/old-feature
git checkout -b feat/old-feature-backup

# Bước 2: Tạo branch mới từ develop
git checkout develop
git pull origin develop
git checkout -b feat/old-feature-v2

# Bước 3: Cherry-pick từng commit cần thiết
git log feat/old-feature --oneline
git cherry-pick abc123 def456 ...

# Bước 4: Hoặc tách thành nhiều PRs nhỏ
```

### Tình huống 4: "Tôi reset --hard nhầm!"

```bash
# Đừng panic! Reflog cứu bạn.

# Xem reflog
git reflog
# abc123 HEAD@{0}: reset: moving to HEAD~10
# def456 HEAD@{1}: commit: My important work  ← Đây!

# Recover
git reset --hard def456
# Hoặc
git checkout -b recovery def456
```

### Tình huống 5: "CI cứ fail hoài, tôi muốn skip!"

```
❌ ĐỪNG LÀM:
git push --no-verify
[skip ci]

✅ NÊN LÀM:
1. Đọc CI error message
2. Fix root cause
3. Commit fix
4. Push lại
5. Nếu CI sai, báo Git Master
```

---

## 📋 Tóm Tắt Anti-patterns

### Commit

| ❌ Đừng làm | ✅ Nên làm |
|------------|-----------|
| Commit message "fix", "update" | Message rõ ràng, mô tả được việc |
| Commit quá lớn (1000+ lines) | Commit nhỏ, focused |
| Commit code broken | Chỉ commit code chạy được |
| Commit files thừa | Review trước, dùng .gitignore |

### Branch

| ❌ Đừng làm | ✅ Nên làm |
|------------|-----------|
| Code trên main/develop | Luôn tạo feature branch |
| Branch sống > 1 tuần | Merge thường xuyên |
| Tên branch "test", "abc" | Tên theo quy ước |
| Để branch cũ chất đống | Xóa sau khi merge |

### Merge

| ❌ Đừng làm | ✅ Nên làm |
|------------|-----------|
| Force push shared branch | Chỉ force branch cá nhân |
| Rebase develop/main | Chỉ rebase branch cá nhân |
| Merge không update | Pull develop trước khi merge |
| Chọn bừa khi conflict | Đọc hiểu, hỏi nếu cần |

### Security

| ❌ Đừng làm | ✅ Nên làm |
|------------|-----------|
| Commit .env | Dùng .gitignore |
| Hardcode passwords | Dùng env variables |
| Lưu data trong repo | Dùng external storage |

---

## ✅ Checklist Tránh Anti-patterns

```
Trước mỗi commit:
□ Message rõ ràng?
□ Commit nhỏ và focused?
□ Code chạy được?
□ Không có file thừa?
□ Không có secrets?

Trước mỗi merge:
□ Đã update branch với develop?
□ Đã tạo PR?
□ Đã được review?
□ CI pass?

Daily habits:
□ Pull develop mỗi sáng
□ Xóa branch đã merge
□ Không force push shared branches
```

---

## ➡️ Bước Tiếp Theo

Xem file **11-cheatsheet.md** - Bảng Tổng Hợp Lệnh Git

---

*Tài liệu thuộc bộ Git Training Documentation v1.0*
