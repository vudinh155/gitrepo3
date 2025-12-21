# 05 - Branching Strategy: Chiến Lược Branch

> **Đối tượng:** Tất cả thành viên team
> **Thời gian học:** 1 giờ
> **Mục tiêu:** Nắm vững cách đặt tên, quản lý branch theo chuẩn

---

## 📖 Mục Lục

1. [Tổng Quan Branching Model](#1-tổng-quan-branching-model)
2. [Quy Ước Đặt Tên Branch](#2-quy-ước-đặt-tên-branch)
3. [Long-lived vs Short-lived Branches](#3-long-lived-vs-short-lived-branches)
4. [Feature Branch Deep Dive](#4-feature-branch-deep-dive)
5. [Release Branch](#5-release-branch)
6. [Quản Lý Branch Trong Team](#6-quản-lý-branch-trong-team)

---

## 1. Tổng Quan Branching Model

### Git Flow (Simplified) của team

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SIMPLIFIED GIT FLOW                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  main ─────●────────────────────────●────────────────●─────▶        │
│            │                        ▲                ▲              │
│            │                        │                │              │
│            │                    [merge]          [merge]            │
│            │                        │                │              │
│  develop ──●────●────●────●────●────●────●────●────●─────▶          │
│            │    ▲    ▲    ▲    ▲         ▲    ▲                     │
│            │    │    │    │    │         │    │                     │
│  feat/A ───┴────┘    │    │    │         │    │                     │
│                      │    │    │         │    │                     │
│  feat/B ─────────────┴────┘    │         │    │                     │
│                                │         │    │                     │
│  fix/C ────────────────────────┴─────────┘    │                     │
│                                               │                     │
│  feat/D ──────────────────────────────────────┘                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

Legend:
●  = Commit
─▶ = Hướng phát triển
▲  = Merge point
```

### Tại sao cần branching strategy?

```
❌ Không có strategy:
├── "Code của tao đâu rồi?"
├── "Ai merge cái gì vào main?"
├── "Branch này là của ai, làm gì?"
└── "Production bị break!"

✅ Có strategy:
├── Biết mỗi branch làm gì
├── Biết ai chịu trách nhiệm
├── History sạch đẹp
└── Rollback dễ dàng
```

---

## 2. Quy Ước Đặt Tên Branch

### 2.1. Format chuẩn

```
<type>/<issue-number>-<short-description>

Hoặc nếu không có issue:
<type>/<short-description>
```

### 2.2. Các type branch

| Type | Mô tả | Ví dụ |
|------|-------|-------|
| `feat/` | Tính năng mới | `feat/123-user-login` |
| `fix/` | Sửa bug | `fix/456-null-pointer` |
| `hotfix/` | Sửa bug khẩn cấp (từ main) | `hotfix/critical-security` |
| `refactor/` | Cải thiện code, không đổi logic | `refactor/cleanup-auth` |
| `docs/` | Chỉ thay đổi documentation | `docs/api-guide` |
| `test/` | Thêm hoặc sửa test | `test/auth-unit-tests` |
| `chore/` | Build, config, dependencies | `chore/update-deps` |
| `ci/` | CI/CD changes | `ci/add-deploy-stage` |
| `style/` | Format code, không đổi logic | `style/eslint-fixes` |

### 2.3. Quy tắc đặt tên

```
✅ ĐÚNG:
feat/123-add-shopping-cart
fix/456-login-error
hotfix/security-patch
refactor/user-service-cleanup
docs/update-readme

❌ SAI:
feat/addShoppingCart        ← Không dùng camelCase
feature/add-cart            ← Sai prefix (feature thay vì feat)
fix/bug                     ← Quá chung chung
feat/add-shopping-cart-feature-with-payment-integration  ← Quá dài
feat/Add Shopping Cart      ← Có space
123-add-cart                ← Thiếu type
```

### 2.4. Các rule chi tiết

```
┌─────────────────────────────────────────────────────────────────────┐
│                     RULES ĐẶT TÊN BRANCH                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. Chỉ dùng lowercase                                              │
│     ✅ feat/add-cart  ❌ feat/Add-Cart                               │
│                                                                     │
│  2. Dùng dấu gạch ngang (-) thay vì gạch dưới (_) hoặc space       │
│     ✅ feat/add-cart  ❌ feat/add_cart  ❌ feat/add cart             │
│                                                                     │
│  3. Bắt đầu bằng type + slash (/)                                  │
│     ✅ feat/xxx  ❌ xxx                                              │
│                                                                     │
│  4. Có issue number nếu có                                          │
│     ✅ feat/123-add-cart  ✅ feat/add-cart (nếu không có issue)     │
│                                                                     │
│  5. Ngắn gọn nhưng mô tả được mục đích                             │
│     ✅ feat/user-auth  ❌ feat/a  ❌ feat/implement-user-auth-...   │
│                                                                     │
│  6. Không dùng ký tự đặc biệt                                       │
│     ✅ feat/add-cart  ❌ feat/add-cart!  ❌ feat/add-cart@v2        │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 3. Long-lived vs Short-lived Branches

### 3.1. Long-lived Branches (Nhánh dài hạn)

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LONG-LIVED BRANCHES                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Branch        Mục đích                   Tồn tại                   │
│  ──────        ────────                   ───────                   │
│  main          Production code            Vĩnh viễn                 │
│  develop       Integration branch         Vĩnh viễn                 │
│  release/*     Release preparation        Vài ngày - tuần          │
│                                                                     │
│  ⚠️ Đặc điểm:                                                       │
│  - Không bao giờ xóa                                                │
│  - Protected (không push trực tiếp)                                 │
│  - Nhiều người làm việc                                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2. Short-lived Branches (Nhánh ngắn hạn)

```
┌─────────────────────────────────────────────────────────────────────┐
│                   SHORT-LIVED BRANCHES                              │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Branch        Mục đích                   Tồn tại                   │
│  ──────        ────────                   ───────                   │
│  feat/*        Tính năng mới              Vài giờ - vài ngày       │
│  fix/*         Sửa bug                    Vài giờ                   │
│  hotfix/*      Sửa bug khẩn cấp           Vài giờ                   │
│  refactor/*    Refactoring                Vài giờ - vài ngày       │
│                                                                     │
│  ✅ Nguyên tắc:                                                      │
│  - Tạo → Code → PR → Merge → XÓA                                   │
│  - Càng sống ngắn càng tốt                                          │
│  - 1 branch = 1 task                                                │
│  - Xóa ngay sau khi merge                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.3. Vòng đời của Feature Branch

```
Ngày 1                      Ngày 2-3                    Ngày 3
  │                            │                          │
  ▼                            ▼                          ▼
┌─────┐    ┌─────┐    ┌─────┐    ┌─────┐    ┌─────┐    ┌─────┐
│ Tạo │───▶│Code │───▶│Push │───▶│ PR  │───▶│Merge│───▶│ Xóa │
│     │    │     │    │     │    │     │    │     │    │     │
└─────┘    └─────┘    └─────┘    └─────┘    └─────┘    └─────┘

Lý tưởng: 1-3 ngày
Tối đa: 1 tuần (cần chia nhỏ nếu lâu hơn)
```

---

## 4. Feature Branch Deep Dive

### 4.1. Khi nào tạo Feature Branch?

```
✅ Tạo branch khi:
├── Bắt đầu task mới
├── Thử nghiệm ý tưởng
├── Sửa bug (dù nhỏ)
└── Refactor code

❌ KHÔNG nên:
├── Code trực tiếp trên develop/main
├── Gộp nhiều task vào 1 branch
└── Để branch sống quá lâu (> 1 tuần)
```

### 4.2. Feature Branch lớn - Chia nhỏ thế nào?

```
Tình huống: Feature "User Authentication" quá lớn

❌ SAI: 1 branch lớn
feat/user-auth (2000+ lines, 2 tuần)
└── Login form
└── Register form
└── Forgot password
└── OAuth
└── 2FA

✅ ĐÚNG: Chia nhỏ
feat/123-login-form          (300 lines, 2 ngày)
feat/124-register-form       (400 lines, 2 ngày)
feat/125-forgot-password     (200 lines, 1 ngày)
feat/126-oauth-google        (300 lines, 2 ngày)
feat/127-two-factor-auth     (500 lines, 3 ngày)

Mỗi PR nhỏ → Review nhanh → Merge nhanh → Ít conflict
```

### 4.3. Feature Flags cho Feature lớn

```
Khi feature chưa hoàn thiện nhưng muốn merge dần:

// Dùng feature flag
if (featureFlags.NEW_AUTH_ENABLED) {
  showNewLogin();
} else {
  showOldLogin();
}

Lợi ích:
├── Merge code thường xuyên
├── Tránh branch dài
├── Test trên production (1 số users)
└── Rollback nhanh (tắt flag)
```

### 4.4. Stacked PRs (Nâng cao)

```
Khi feature phụ thuộc nhau:

feat/123-auth-base
       │
       └──▶ feat/124-login (based on 123)
                   │
                   └──▶ feat/125-register (based on 124)

Workflow:
1. PR #1: feat/123-auth-base → develop
2. PR #2: feat/124-login → feat/123-auth-base (chờ #1 merge)
3. Sau khi #1 merge, update target của #2 thành develop
```

---

## 5. Release Branch

### 5.1. Khi nào cần Release Branch?

```
✅ Cần release branch khi:
├── Prepare cho release lớn
├── Cần freeze features
├── QA cần test stable version
└── Có multiple versions cần maintain

❌ Không cần khi:
├── Deploy liên tục (CD)
├── Team nhỏ
└── Single version
```

### 5.2. Workflow với Release Branch

```
┌─────────────────────────────────────────────────────────────────────┐
│                   RELEASE BRANCH WORKFLOW                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  develop ────●────●────●────●────●────●────●────●─────▶             │
│              │                   ▲                                  │
│              │                   │ (merge back bugfixes)            │
│              │                   │                                  │
│  release/    └────●────●────●────┴────●─────▶ main                  │
│  1.0              │    │    │         │                             │
│                  fix  fix  fix     release                          │
│                                    & tag                            │
│                                                                     │
│  Timeline:                                                          │
│  1. Tạo release/1.0 từ develop                                     │
│  2. Chỉ fix bugs, không thêm features                              │
│  3. Mỗi fix merge về develop                                       │
│  4. Khi ready, merge vào main + tag                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.3. Commands cho Release Branch

```bash
# Tạo release branch
git checkout develop
git pull origin develop
git checkout -b release/1.0.0

# Fix bug trên release branch
git checkout -b fix/release-bug-xxx release/1.0.0
# ... fix ...
git checkout release/1.0.0
git merge fix/release-bug-xxx

# Merge fix về develop
git checkout develop
git merge release/1.0.0

# Khi ready, merge vào main và tag
git checkout main
git merge release/1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin main --tags

# Xóa release branch (optional)
git branch -d release/1.0.0
git push origin --delete release/1.0.0
```

---

## 6. Quản Lý Branch Trong Team

### 6.1. Xem Branch

```bash
# Branch local
git branch

# Branch remote
git branch -r

# Tất cả branch
git branch -a

# Branch đã merge vào develop
git branch --merged develop

# Branch chưa merge
git branch --no-merged develop

# Branch với commit cuối
git branch -v
```

### 6.2. Dọn dẹp Branch

```bash
# Xóa branch local đã merge
git branch --merged | grep -v "main\|develop" | xargs git branch -d

# Xóa branch remote đã merge
git branch -r --merged origin/develop | \
  grep -v "main\|develop" | \
  sed 's/origin\///' | \
  xargs -I {} git push origin --delete {}

# Sync với remote (xóa tracking của branch đã bị xóa)
git fetch --prune

# Script dọn dẹp weekly
#!/bin/bash
git checkout develop
git pull origin develop
git fetch --prune
git branch --merged | grep -v "main\|develop" | xargs git branch -d
```

### 6.3. Quy trình dọn dẹp

```
┌─────────────────────────────────────────────────────────────────────┐
│                    QUY TRÌNH DỌN DẸP BRANCH                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Hàng ngày (Developer):                                             │
│  ├── Xóa branch local sau khi PR merged                            │
│  └── git branch -d feat/xxx                                        │
│                                                                     │
│  Hàng tuần (Git Master):                                            │
│  ├── Xóa branch remote đã merge                                    │
│  ├── Review branch cũ (> 2 tuần không hoạt động)                   │
│  └── Nhắc owner update hoặc xóa                                    │
│                                                                     │
│  Hàng tháng (Tech Lead):                                            │
│  ├── Audit tất cả branch                                            │
│  ├── Xóa branch stale                                               │
│  └── Review branch protection rules                                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.4. Branch Naming Convention Enforcement

```yaml
# Ví dụ GitHub Actions để check branch name
name: Branch Name Check

on:
  push:
    branches-ignore:
      - main
      - develop

jobs:
  check-branch-name:
    runs-on: ubuntu-latest
    steps:
      - name: Check branch name
        run: |
          BRANCH=${GITHUB_REF#refs/heads/}
          PATTERN="^(feat|fix|hotfix|refactor|docs|test|chore|ci|style)/[a-z0-9-]+$"

          if [[ ! $BRANCH =~ $PATTERN ]]; then
            echo "❌ Branch name '$BRANCH' không đúng format!"
            echo "Format: <type>/<description>"
            echo "Types: feat, fix, hotfix, refactor, docs, test, chore, ci, style"
            exit 1
          fi

          echo "✅ Branch name OK: $BRANCH"
```

---

## 📋 Quick Reference

### Branch Types

| Type | Từ | Vào | Xóa sau merge |
|------|-----|-----|---------------|
| `feat/*` | develop | develop | ✅ |
| `fix/*` | develop | develop | ✅ |
| `hotfix/*` | main | main + develop | ✅ |
| `release/*` | develop | main + develop | ✅ |
| `refactor/*` | develop | develop | ✅ |

### Naming Examples

```
feat/123-user-authentication
feat/add-shopping-cart
fix/456-null-pointer-exception
fix/login-validation
hotfix/security-patch-xss
refactor/cleanup-legacy-auth
docs/update-api-docs
test/add-payment-tests
chore/update-dependencies
ci/add-staging-deploy
```

---

## ✅ Checklist Branching Strategy

```
□ Đặt tên branch theo format: <type>/<description>
□ Branch ngắn hạn (< 1 tuần)
□ Chia feature lớn thành nhiều branch nhỏ
□ Xóa branch ngay sau khi merge
□ Cập nhật branch với develop thường xuyên
□ Không push trực tiếp vào main/develop
```

---

## ➡️ Bước Tiếp Theo

Học file **06-merge-and-conflict.md** - Merge và Xử lý Conflict

---

*Tài liệu thuộc bộ Git Training Documentation v1.0*
