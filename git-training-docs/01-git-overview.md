# 01 - Git Overview: Git Là Gì & Tại Sao Cần Git?

> **Đối tượng:** Junior mới onboard
> **Thời gian học:** 30-45 phút
> **Mục tiêu:** Hiểu Git giải quyết vấn đề gì, không còn sợ Git

---

## 📖 Mục Lục

1. [Vấn Đề Trước Khi Có Git](#1-vấn-đề-trước-khi-có-git)
2. [Git Là Gì?](#2-git-là-gì)
3. [Git vs SVN](#3-git-vs-svn)
4. [Các Khái Niệm Cơ Bản](#4-các-khái-niệm-cơ-bản)
5. [Cài Đặt Git](#5-cài-đặt-git)
6. [Cấu Hình Ban Đầu](#6-cấu-hình-ban-đầu)

---

## 1. Vấn Đề Trước Khi Có Git

### 😱 Kịch bản kinh điển

```
project/
├── code_v1.zip
├── code_v2.zip
├── code_v2_final.zip
├── code_v2_final_FINAL.zip
├── code_v2_final_FINAL_fixed.zip
├── code_backup_20231225.zip
└── code_ĐỪNG_XÓA.zip          ← Ai cũng sợ xóa
```

### 🤯 Những vấn đề thường gặp

| Vấn đề | Hậu quả |
|--------|---------|
| Nhiều người sửa 1 file | Ghi đè code của nhau |
| Không biết ai sửa gì | "Ai xóa function này???" |
| Muốn quay lại version cũ | "Version cũ ở đâu?" |
| Code hỏng | "Hôm qua chạy được mà!" |
| Làm việc offline | "Không có mạng = không làm được" |

### 💡 Git giải quyết TẤT CẢ những vấn đề này!

---

## 2. Git Là Gì?

### Định nghĩa đơn giản

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  Git = Hệ thống quản lý phiên bản phân tán                     │
│        (Distributed Version Control System)                    │
│                                                                 │
│  Nói đơn giản: Git lưu lại MỌI thay đổi của code,              │
│  cho phép bạn quay lại BẤT KỲ thời điểm nào.                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Hình dung Git như "Time Machine" cho code

```
        Quá khứ                    Hiện tại                Tương lai
           │                          │                        │
           ▼                          ▼                        ▼
    ┌──────────┐    ┌──────────┐    ┌──────────┐
    │ Commit 1 │───▶│ Commit 2 │───▶│ Commit 3 │───▶  ...
    │ "Init"   │    │ "Add UI" │    │ "Fix bug"│
    └──────────┘    └──────────┘    └──────────┘
         │
         └──── Có thể quay lại đây bất cứ lúc nào!
```

### Git theo dõi gì?

```
✅ Theo dõi:                    ❌ KHÔNG theo dõi mặc định:
├── Source code (.js, .py...)   ├── node_modules/
├── Config files                ├── File build (.exe, .dll)
├── Documentation               ├── File secret (.env)
└── Assets (nếu cần)            └── File tạm (.log, .tmp)
```

---

## 3. Git vs SVN

> 💡 Nếu bạn chưa dùng SVN, có thể skip phần này

### So sánh nhanh

```
                    SVN (Tập trung)              Git (Phân tán)

                         ┌───┐                    ┌───┐
                         │ S │                    │ S │ Server
                         └─┬─┘                    └─┬─┘
                           │                        │
              ┌────────────┼────────────┐    ┌──────┼──────┐
              │            │            │    │      │      │
              ▼            ▼            ▼    ▼      ▼      ▼
            ┌───┐        ┌───┐        ┌───┐ ┌───┐ ┌───┐ ┌───┐
            │ A │        │ B │        │ C │ │ A │ │ B │ │ C │
            └───┘        └───┘        └───┘ └─┬─┘ └─┬─┘ └─┬─┘
                                              │     │     │
            Chỉ có code                     ┌─┴─┐ ┌─┴─┐ ┌─┴─┘
            Không có history                │Rep│ │Rep│ │Rep│
                                           └───┘ └───┘ └───┘
                                           Mỗi người có
                                           FULL history
```

### Bảng so sánh chi tiết

| Tiêu chí | SVN | Git |
|----------|-----|-----|
| Kiến trúc | Tập trung | Phân tán |
| Làm việc offline | ❌ Không | ✅ Có |
| Tốc độ | Chậm (phụ thuộc mạng) | Nhanh (local) |
| Branch | Chậm, nặng | Nhanh, nhẹ |
| History | Chỉ trên server | Mỗi máy đều có |
| Backup | 1 điểm chết | Nhiều bản sao |
| Độ phổ biến 2024 | ↓ Giảm | ↑ Thống trị |

### ⚠️ Tại sao team dùng Git?

```
1. Làm việc OFFLINE được      → Commit trên máy bay? OK!
2. Branch CỰC NHANH           → Thử nghiệm thoải mái
3. Merge THÔNG MINH           → Ít conflict hơn
4. BACKUP tự nhiên            → Mỗi clone = 1 backup
5. CỘNG ĐỒNG lớn              → GitHub, GitLab, Bitbucket
```

---

## 4. Các Khái Niệm Cơ Bản

### 4.1. Repository (Repo)

```
Repository = Thư mục dự án + Lịch sử Git

my-project/           ← Đây là Repository
├── .git/             ← Git lưu history ở đây (ĐỪNG ĐỘNG VÀO!)
├── src/
├── package.json
└── README.md
```

**Hai loại repo:**
- **Local repo:** Trên máy của bạn
- **Remote repo:** Trên server (GitHub, GitLab...)

### 4.2. Commit

```
Commit = Một "ảnh chụp" của code tại 1 thời điểm

┌─────────────────────────────────────────────────┐
│ Commit: abc123                                  │
├─────────────────────────────────────────────────┤
│ Author:  Nguyen Van A                           │
│ Date:    2024-01-15 10:30                       │
│ Message: "Add login feature"                   │
├─────────────────────────────────────────────────┤
│ Changes:                                        │
│   + src/login.js (new file)                     │
│   ~ src/app.js (modified)                       │
│   - src/old-auth.js (deleted)                   │
└─────────────────────────────────────────────────┘
```

**Commit tốt:**
- ✅ Nhỏ, tập trung 1 việc
- ✅ Message rõ ràng
- ✅ Code chạy được

**Commit xấu:**
- ❌ Quá to, nhiều thay đổi không liên quan
- ❌ Message "fix bug", "update"
- ❌ Code bị lỗi

### 4.3. Branch

```
Branch = Nhánh phát triển song song

                    main
                      │
        ──────────────●──────────────────────●────────▶
                      │                      │
                      │    feature/login     │
                      └──────●─────●─────────┘
                             │     │
                          Commit Commit

Mỗi branch là 1 "vũ trụ song song" của code
```

**Branches phổ biến:**
```
main (hoặc master)  → Code production, luôn stable
develop             → Code development, integration
feature/*           → Tính năng mới
fix/*               → Sửa bug
hotfix/*            → Sửa bug khẩn cấp trên production
```

### 4.4. Merge

```
Merge = Gộp code từ branch này vào branch khác

        main
          │
    ──────●───────────────────────────●──────────▶
          │                          ╱│
          │    feature/login        ╱ │
          └──────●─────●───────────╱  │
                 A     B     Merge ───┘

Lấy code từ feature/login gộp vào main
```

### 4.5. Clone, Pull, Push

```
┌─────────────────────────────────────────────────────────────────┐
│                         REMOTE (GitHub)                         │
│                              ☁️                                  │
└───────────────────────────────┬─────────────────────────────────┘
                                │
           ┌────────────────────┼────────────────────┐
           │                    │                    │
         clone               push                  pull
        (lần đầu)         (đẩy lên)            (kéo về)
           │                    │                    │
           ▼                    ▲                    ▼
┌─────────────────────────────────────────────────────────────────┐
│                         LOCAL (Máy bạn)                         │
│                              💻                                  │
└─────────────────────────────────────────────────────────────────┘
```

| Lệnh | Hành động | Khi nào dùng |
|------|-----------|--------------|
| `clone` | Tải repo về máy | Lần đầu lấy dự án |
| `pull` | Cập nhật code mới nhất | Trước khi làm việc |
| `push` | Đẩy code lên server | Sau khi commit xong |

---

## 5. Cài Đặt Git

### Windows

```bash
# Cách 1: Tải từ website
# https://git-scm.com/download/windows

# Cách 2: Dùng winget
winget install --id Git.Git -e --source winget

# Cách 3: Dùng Chocolatey
choco install git
```

### macOS

```bash
# Cách 1: Xcode Command Line Tools (khuyên dùng)
xcode-select --install

# Cách 2: Homebrew
brew install git
```

### Linux (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install git
```

### Kiểm tra cài đặt

```bash
git --version
# Output: git version 2.43.0 (hoặc version khác)
```

✅ Nếu thấy version → Cài đặt thành công!

---

## 6. Cấu Hình Ban Đầu

### 6.1. Cấu hình bắt buộc

```bash
# Tên của bạn (hiển thị trong commit)
git config --global user.name "Nguyen Van A"

# Email (phải trùng với email GitHub/GitLab)
git config --global user.email "a.nguyen@company.com"
```

### 6.2. Cấu hình khuyến nghị

```bash
# Default branch là main (thay vì master)
git config --global init.defaultBranch main

# Editor mặc định (chọn 1)
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "vim"          # Vim
git config --global core.editor "nano"         # Nano

# Xử lý line ending (QUAN TRỌNG cho team Windows + Mac)
# Windows:
git config --global core.autocrlf true
# Mac/Linux:
git config --global core.autocrlf input

# Màu sắc terminal
git config --global color.ui auto
```

### 6.3. Kiểm tra cấu hình

```bash
# Xem tất cả config
git config --list

# Xem config cụ thể
git config user.name
git config user.email
```

### 6.4. SSH Key (Khuyến nghị)

```bash
# Tạo SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Xem public key (copy key này lên GitHub/GitLab)
cat ~/.ssh/id_ed25519.pub
```

**Thêm SSH key vào GitHub:**
1. Vào GitHub → Settings → SSH and GPG keys
2. Click "New SSH key"
3. Paste public key vào

**Test SSH:**
```bash
ssh -T git@github.com
# Output: Hi username! You've successfully authenticated...
```

---

## 📝 Tổng Kết

### Những gì bạn đã học:

| Khái niệm | Ý nghĩa |
|-----------|---------|
| Git | Hệ thống quản lý version phân tán |
| Repository | Thư mục dự án + lịch sử |
| Commit | Ảnh chụp code tại 1 thời điểm |
| Branch | Nhánh phát triển song song |
| Merge | Gộp nhánh |
| Clone/Pull/Push | Đồng bộ local ↔ remote |

### Checklist trước khi tiếp tục:

```
□ Đã cài Git (git --version chạy OK)
□ Đã config user.name
□ Đã config user.email
□ Đã setup SSH key (nếu dùng GitHub/GitLab)
```

---

## ❓ FAQ - Câu Hỏi Thường Gặp

### Q: Git và GitHub khác nhau thế nào?

```
Git     = Phần mềm quản lý version (chạy trên máy bạn)
GitHub  = Dịch vụ hosting Git repo (giống Google Drive cho code)

Tương tự:
Git     = Email protocol
GitHub  = Gmail
```

### Q: Tôi có thể dùng Git mà không cần GitHub không?

✅ Có! Git hoạt động hoàn toàn offline. GitHub chỉ cần khi làm việc team.

### Q: Thư mục .git có thể xóa không?

🔴 **KHÔNG!** Xóa .git = Mất toàn bộ lịch sử Git của project.

### Q: Git lưu file ở đâu?

Git lưu trong thư mục `.git/` dưới dạng nén, không phải bản sao file.

---

## ➡️ Bước Tiếp Theo

Học file **02-git-basic-commands.md** - Các lệnh Git cơ bản

---

*Tài liệu thuộc bộ Git Training Documentation v1.0*
