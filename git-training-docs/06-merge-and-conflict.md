# 06 - Merge và Xử Lý Conflict

> **Đối tượng:** Tất cả thành viên team
> **Thời gian học:** 1-2 giờ
> **Mục tiêu:** Thành thạo merge, rebase, và xử lý conflict

---

## 📖 Mục Lục

1. [Merge vs Rebase](#1-merge-vs-rebase)
2. [Các Loại Merge](#2-các-loại-merge)
3. [Conflict Là Gì?](#3-conflict-là-gì)
4. [Xử Lý Conflict Từng Bước](#4-xử-lý-conflict-từng-bước)
5. [Công Cụ Xử Lý Conflict](#5-công-cụ-xử-lý-conflict)
6. [Nguyên Tắc Khi Nào Merge/Rebase](#6-nguyên-tắc-khi-nào-mergerebase)
7. [Tình Huống Thực Tế](#7-tình-huống-thực-tế)

---

## 1. Merge vs Rebase

### 1.1. So sánh trực quan

```
┌─────────────────────────────────────────────────────────────────────┐
│                         MERGE                                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  main     A───B───C─────────────M  (merge commit)                  │
│                \               /                                    │
│  feat           D───E───F─────┘                                    │
│                                                                     │
│  ✅ Giữ nguyên lịch sử                                               │
│  ✅ An toàn cho shared branches                                      │
│  ❌ History phức tạp hơn                                             │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                         REBASE                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  TRƯỚC:                                                             │
│  main     A───B───C                                                │
│                \                                                    │
│  feat           D───E───F                                          │
│                                                                     │
│  SAU: (rebase feat onto main)                                      │
│  main     A───B───C                                                │
│                    \                                                │
│  feat               D'──E'──F'  (commits được "di chuyển")         │
│                                                                     │
│  ✅ History tuyến tính, sạch                                         │
│  ✅ Dễ đọc git log                                                   │
│  ❌ Thay đổi commit hash                                             │
│  ⚠️ Nguy hiểm cho shared branches                                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 1.2. Khi nào dùng Merge vs Rebase?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    MERGE vs REBASE                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  DÙNG MERGE KHI:                                                    │
│  ├── Merge feature → develop/main                                  │
│  ├── Shared branch (nhiều người làm)                               │
│  ├── Muốn giữ lịch sử đầy đủ                                       │
│  └── PR merge (mặc định)                                            │
│                                                                     │
│  DÙNG REBASE KHI:                                                   │
│  ├── Cập nhật feature branch với develop mới                       │
│  ├── Branch chỉ MỘT MÌNH làm                                       │
│  ├── Muốn history sạch trước khi PR                                │
│  └── Squash commits                                                 │
│                                                                     │
│  ❌ KHÔNG BAO GIỜ REBASE:                                           │
│  ├── Branch main/develop                                           │
│  ├── Branch có người khác đang làm                                 │
│  └── Commits đã push mà người khác đã pull                         │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Các Loại Merge

### 2.1. Fast-forward Merge

```
TRƯỚC:
main     A───B───C
              \
feat           D───E

SAU: (fast-forward)
main     A───B───C───D───E
                          ↑
                     main pointer moved

Điều kiện: main không có commit mới kể từ khi tạo feat
Command: git merge feat (Git tự động fast-forward nếu có thể)
```

```bash
# Fast-forward merge
git checkout main
git merge feat/login
# Output: Fast-forward

# Nếu muốn tạo merge commit dù có thể fast-forward
git merge --no-ff feat/login
```

### 2.2. Three-way Merge

```
TRƯỚC:
main     A───B───C───X───Y     (có commits mới)
              \
feat           D───E

SAU: (three-way merge)
main     A───B───C───X───Y───M  (merge commit)
              \             /
feat           D───────E───┘

M = merge commit, kết hợp Y và E
```

```bash
# Three-way merge
git checkout main
git merge feat/login
# Output: Merge made by the 'ort' strategy.
```

### 2.3. Squash Merge

```
TRƯỚC:
main     A───B───C
              \
feat           D───E───F  (3 commits)

SAU: (squash merge)
main     A───B───C───S  (1 commit chứa tất cả changes)

S = squashed commit, không có connection với D, E, F
```

```bash
# Squash merge
git checkout main
git merge --squash feat/login
git commit -m "feat: add login feature"
```

**Khi nào dùng squash?**
```
✅ Dùng squash khi:
├── Feature branch có nhiều "WIP" commits
├── Muốn gộp thành 1 commit sạch
└── Commits không có giá trị lưu riêng

❌ Không dùng squash khi:
├── Mỗi commit có ý nghĩa riêng
├── Cần lịch sử chi tiết
└── Có thể cần revert từng commit
```

---

## 3. Conflict Là Gì?

### 3.1. Khi nào xảy ra conflict?

```
┌─────────────────────────────────────────────────────────────────────┐
│                    KHI NÀO CÓ CONFLICT?                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  2 người sửa CÙNG DÒNG trong CÙNG FILE                             │
│                                                                     │
│  main:                    feat:                                     │
│  ┌─────────────────┐     ┌─────────────────┐                       │
│  │ function add() {│     │ function add() {│                       │
│  │   return a + b; │     │   return x + y; │  ← Cùng dòng,        │
│  │ }               │     │ }               │    khác code          │
│  └─────────────────┘     └─────────────────┘                       │
│                                                                     │
│  → Git không biết chọn version nào → CONFLICT!                     │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2. Khi nào KHÔNG có conflict?

```
✅ KHÔNG conflict khi:

1. Sửa KHÁC FILE
   main: sửa app.js
   feat: sửa utils.js
   → OK, auto merge

2. Sửa KHÁC DÒNG trong cùng file
   main: sửa dòng 10
   feat: sửa dòng 50
   → OK, auto merge

3. Một bên chỉ THÊM code
   main: không đổi
   feat: thêm function mới
   → OK, auto merge
```

### 3.3. Conflict trông như thế nào?

```javascript
// File bị conflict
function calculate() {
<<<<<<< HEAD
  // Code từ branch hiện tại (main)
  return price * quantity * 1.1;
=======
  // Code từ branch đang merge vào (feat)
  return price * quantity * TAX_RATE;
>>>>>>> feat/tax-calculation
}

// Giải thích:
// <<<<<<< HEAD         = Bắt đầu phần code của branch hiện tại
// =======              = Ngăn cách giữa 2 versions
// >>>>>>> feat/...     = Kết thúc phần code của branch merge vào
```

---

## 4. Xử Lý Conflict Từng Bước

### 4.1. Quy trình chuẩn

```
┌─────────────────────────────────────────────────────────────────────┐
│                    QUY TRÌNH XỬ LÝ CONFLICT                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. MERGE và thấy conflict                                          │
│     $ git merge develop                                             │
│     CONFLICT (content): Merge conflict in src/app.js               │
│                                                                     │
│  2. XEM files bị conflict                                           │
│     $ git status                                                    │
│     both modified: src/app.js                                       │
│                                                                     │
│  3. MỞ file và XỬ LÝ                                                │
│     - Xóa markers (<<<, ===, >>>)                                  │
│     - Giữ code đúng                                                 │
│     - Có thể kết hợp cả 2                                           │
│                                                                     │
│  4. STAGE file đã resolve                                           │
│     $ git add src/app.js                                            │
│                                                                     │
│  5. COMMIT merge                                                    │
│     $ git commit -m "Merge develop into feat/xxx"                  │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2. Ví dụ thực tế

```bash
# Bước 1: Merge và thấy conflict
$ git checkout feat/tax-calc
$ git merge develop

Auto-merging src/pricing.js
CONFLICT (content): Merge conflict in src/pricing.js
Automatic merge failed; fix conflicts and then commit the result.

# Bước 2: Xem files conflict
$ git status

On branch feat/tax-calc
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   src/pricing.js

# Bước 3: Mở file và sửa
```

**TRƯỚC khi sửa (file conflict):**
```javascript
function calculateTotal(price, quantity) {
<<<<<<< HEAD
  const tax = price * 0.1;
  return (price + tax) * quantity;
=======
  const TAX_RATE = 0.08;
  return price * quantity * (1 + TAX_RATE);
>>>>>>> develop
}
```

**SAU khi sửa (chọn cách kết hợp tốt nhất):**
```javascript
function calculateTotal(price, quantity) {
  const TAX_RATE = 0.1; // Giữ rate từ HEAD
  return price * quantity * (1 + TAX_RATE); // Dùng formula từ develop
}
```

```bash
# Bước 4: Stage file
$ git add src/pricing.js

# Bước 5: Commit
$ git commit -m "Merge develop: combine tax calculation approaches"

# Bước 6: Tiếp tục push
$ git push
```

### 4.3. Các cách resolve conflict

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CÁC CÁCH RESOLVE                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  1. GIỮ CODE CỦA MÌNH (HEAD)                                        │
│     $ git checkout --ours src/app.js                               │
│     $ git add src/app.js                                            │
│                                                                     │
│  2. GIỮ CODE CỦA HỌ (incoming)                                      │
│     $ git checkout --theirs src/app.js                             │
│     $ git add src/app.js                                            │
│                                                                     │
│  3. SỬA TAY (phổ biến nhất)                                         │
│     - Mở file                                                       │
│     - Xóa markers                                                   │
│     - Chọn/kết hợp code phù hợp                                     │
│     - git add                                                       │
│                                                                     │
│  4. DÙNG TOOL (VS Code, IntelliJ...)                               │
│     - Có UI trực quan                                               │
│     - Accept Current / Accept Incoming / Accept Both               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.4. Abort merge nếu quá phức tạp

```bash
# Nếu conflict quá khó, muốn hủy merge
$ git merge --abort

# Quay về trạng thái trước khi merge
# Không mất code gì cả
```

---

## 5. Công Cụ Xử Lý Conflict

### 5.1. VS Code

```
VS Code tự động detect conflict và hiện UI:

┌─────────────────────────────────────────────────────────────────────┐
│  <<<<<<< HEAD (Current Change)                                     │
│  ┌─────────────────────────────────────────────────────────────────┐
│  │ Accept Current Change | Accept Incoming Change | Accept Both   │
│  │ Compare Changes                                                 │
│  └─────────────────────────────────────────────────────────────────┘
│    const tax = price * 0.1;                                         │
│    return (price + tax) * quantity;                                │
│  =======                                                            │
│    const TAX_RATE = 0.08;                                           │
│    return price * quantity * (1 + TAX_RATE);                       │
│  >>>>>>> develop (Incoming Change)                                 │
└─────────────────────────────────────────────────────────────────────┘

Click vào:
- "Accept Current Change": Giữ code của mình
- "Accept Incoming Change": Giữ code merge vào
- "Accept Both Changes": Giữ cả 2
- "Compare Changes": Xem side-by-side
```

### 5.2. Git Mergetool

```bash
# Cấu hình mergetool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Hoặc dùng meld, kdiff3, etc.
git config --global merge.tool meld

# Khi có conflict, chạy:
git mergetool

# Sẽ mở tool với 3 panels:
# - LOCAL (code của mình)
# - BASE (code gốc trước khi 2 bên sửa)
# - REMOTE (code merge vào)
# - OUTPUT (kết quả merge)
```

### 5.3. IntelliJ / WebStorm

```
Cũng có UI tương tự:

┌─────────────────────────────────────────────────────────────────────┐
│  Your version  │  Result  │  Their version                         │
├────────────────┼──────────┼────────────────────────────────────────┤
│ const tax =    │          │ const TAX_RATE =                       │
│   price * 0.1; │    ?     │   0.08;                                │
│                │          │                                         │
│ [Accept Left]  │          │ [Accept Right]                         │
│                │ [Accept] │                                         │
└────────────────┴──────────┴────────────────────────────────────────┘

Click vào >> hoặc << để chọn code
Hoặc edit trực tiếp ở panel giữa
```

---

## 6. Nguyên Tắc Khi Nào Merge/Rebase

### 6.1. Decision tree

```
                        Cần gộp code?
                             │
              ┌──────────────┴──────────────┐
              │                             │
     Feature → develop             Update feature với
        hoặc                         develop mới
     develop → main
              │                             │
              ▼                             ▼
           MERGE                    Branch chỉ mình làm?
                                           │
                               ┌───────────┴───────────┐
                               │                       │
                              Có                     Không
                               │                       │
                               ▼                       ▼
                            REBASE                  MERGE
```

### 6.2. Quy tắc cụ thể trong team

```
┌─────────────────────────────────────────────────────────────────────┐
│                    QUY TẮC MERGE/REBASE                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  MERGE trong các trường hợp:                                        │
│  ├── feat/* → develop (qua PR)                                     │
│  ├── develop → main (qua PR)                                       │
│  ├── hotfix/* → main và develop                                    │
│  └── Branch có nhiều người làm                                     │
│                                                                     │
│  REBASE trong các trường hợp:                                       │
│  ├── Cập nhật feature branch với develop mới                       │
│  │   $ git checkout feat/xxx                                       │
│  │   $ git rebase develop                                          │
│  │                                                                  │
│  ├── Trước khi tạo PR (clean history)                              │
│  │   $ git rebase -i HEAD~5  (squash commits)                      │
│  │                                                                  │
│  └── CHỈ KHI branch chưa được người khác pull                      │
│                                                                     │
│  ⛔ KHÔNG BAO GIỜ REBASE:                                           │
│  ├── main, develop                                                 │
│  ├── Branch đã push & người khác đã pull                           │
│  └── Shared branches                                                │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 6.3. Force push sau rebase

```bash
# Sau khi rebase, push sẽ bị reject
$ git push
! [rejected]        feat/xxx -> feat/xxx (non-fast-forward)

# Cần force push
$ git push --force-with-lease

# ⚠️ --force-with-lease an toàn hơn --force
# Sẽ fail nếu có người khác đã push
```

---

## 7. Tình Huống Thực Tế

### 7.1. Scenario: Hai người sửa cùng file

```
Dev A: Sửa function calculateTax()
Dev B: Sửa function calculateTax()

Ai merge trước? → Dev A
Dev B merge sau? → Có conflict

Quy trình Dev B:
1. $ git checkout feat/devB-tax
2. $ git merge develop          # Conflict!
3. Mở file, xem cả 2 version
4. Kết hợp code hợp lý (có thể hỏi Dev A)
5. $ git add .
6. $ git commit
7. $ git push
8. Cập nhật PR
```

### 7.2. Scenario: Conflict khi pull

```bash
$ git pull origin develop

CONFLICT (content): Merge conflict in src/api.js
Automatic merge failed; fix conflicts and then commit.

# Option 1: Resolve conflict
$ vim src/api.js          # Sửa file
$ git add src/api.js
$ git commit              # Commit merge

# Option 2: Abort và rebase
$ git merge --abort
$ git pull --rebase origin develop
# Resolve conflict...
$ git rebase --continue
```

### 7.3. Scenario: Conflict phức tạp, cần help

```
Khi conflict liên quan đến:
├── Logic phức tạp
├── Không hiểu code của người khác
├── Ảnh hưởng đến nhiều chức năng

→ DỪNG LẠI và hỏi!

1. Ping người sở hữu code gốc
2. Hoặc hỏi Git Master
3. Pair để resolve cùng nhau

Đừng đoán mò và merge sai!
```

### 7.4. Scenario: Muốn undo merge

```bash
# Nếu CHƯA push
$ git reset --hard HEAD~1

# Nếu ĐÃ push (tạo revert commit)
$ git revert -m 1 HEAD
$ git push

# -m 1 = giữ main line, revert changes từ merge
```

---

## 📋 Quick Reference

### Merge Commands

| Lệnh | Chức năng |
|------|-----------|
| `git merge <branch>` | Merge branch vào current |
| `git merge --no-ff <branch>` | Merge với merge commit |
| `git merge --squash <branch>` | Gộp tất cả thành 1 commit |
| `git merge --abort` | Hủy merge đang làm |

### Conflict Resolution

| Lệnh | Chức năng |
|------|-----------|
| `git status` | Xem files conflict |
| `git diff` | Xem chi tiết conflict |
| `git checkout --ours <file>` | Giữ version của mình |
| `git checkout --theirs <file>` | Giữ version merge vào |
| `git add <file>` | Mark as resolved |
| `git mergetool` | Mở conflict tool |

### Rebase Commands

| Lệnh | Chức năng |
|------|-----------|
| `git rebase <branch>` | Rebase onto branch |
| `git rebase -i HEAD~N` | Interactive rebase N commits |
| `git rebase --continue` | Tiếp tục sau resolve conflict |
| `git rebase --abort` | Hủy rebase |

---

## ✅ Checklist Xử Lý Conflict

```
□ Hiểu sự khác biệt merge vs rebase
□ Biết khi nào dùng merge, khi nào dùng rebase
□ Thành thạo resolve conflict thủ công
□ Biết dùng tool (VS Code, mergetool)
□ Biết abort khi cần
□ Không rebase shared branches
□ Hỏi khi conflict phức tạp
```

---

## ➡️ Bước Tiếp Theo

Học file **07-practical-exercises.md** - Bài Tập Thực Hành

---

*Tài liệu thuộc bộ Git Training Documentation v1.0*
