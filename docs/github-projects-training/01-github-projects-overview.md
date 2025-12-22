# 01 - GitHub Projects Overview

> **Mục tiêu**: Hiểu GitHub Projects là gì, tại sao dùng, và nó khác gì Jira/Trello

**Thời lượng**: 30 phút
**Đối tượng**: PM, QA (Tester), Dev, Stakeholder (tất cả)

---

## 🎯 GitHub Projects là gì?

**GitHub Projects (V2)** là công cụ quản lý dự án **tích hợp sẵn** trong GitHub, cho phép:

- 📋 **Quản lý công việc** (issues, pull requests) trực tiếp từ code repository
- 📊 **Theo dõi tiến độ** qua Board, Table, Roadmap view
- 🔗 **Liên kết** requirement → code → testing → release trong **1 nền tảng**
- 🤖 **Tự động hoá** workflow (auto-update status khi PR merge, etc.)

### Điểm đặc biệt

```
GitHub Projects = Project Management Tool + Code Repository
```

Khác với Jira/Trello/Excel:

- Code và Issue ở **CÙNG 1 NƠI**
- Developer không cần chuyển tab
- PM/QA thấy được code thật (PR, commit) ngay trong issue

---

## 🆚 So sánh với các công cụ khác

### GitHub Projects vs. Jira

| Tiêu chí          | GitHub Projects                      | Jira                      |
| ----------------- | ------------------------------------ | ------------------------- |
| **Tích hợp code** | ✅ Native (issue ↔ PR ↔ commit)      | ⚠️ Cần cấu hình webhook   |
| **Giá**           | ✅ Free (public repo) / Rẻ (private) | ❌ Đắt ($7-14/user/tháng) |
| **Độ phức tạp**   | ✅ Đơn giản, dễ học                  | ❌ Phức tạp, nhiều config |
| **Phù hợp team**  | ✅ 5-50 người                        | ✅ 10-1000 người          |
| **Customization** | ⚠️ Đủ dùng                           | ✅ Cực mạnh               |
| **Báo cáo**       | ⚠️ Cơ bản (insights)                 | ✅ Rất mạnh               |
| **Developer UX**  | ✅ Xuất sắc                          | ⚠️ Tạm ổn                 |

**Kết luận**:

- **Chọn GitHub Projects** nếu: Team < 50, code trên GitHub, cần đơn giản + rẻ
- **Chọn Jira** nếu: Team > 100, cần báo cáo phức tạp, nhiều stakeholder

---

### GitHub Projects vs. Trello

| Tiêu chí          | GitHub Projects          | Trello            |
| ----------------- | ------------------------ | ----------------- |
| **Tích hợp code** | ✅ Native                | ❌ Không có       |
| **Giá**           | ✅ Free/Rẻ               | ✅ Free/Rẻ        |
| **Độ phức tạp**   | ✅ Vừa phải              | ✅ Rất đơn giản   |
| **View**          | ✅ Board, Table, Roadmap | ⚠️ Chỉ Board      |
| **Custom field**  | ✅ Có                    | ⚠️ Giới hạn       |
| **Developer UX**  | ✅ Xuất sắc              | ❌ Developer ghét |

**Kết luận**:

- **Chọn GitHub Projects** nếu: Team dev, cần link code + issue
- **Chọn Trello** nếu: Team marketing/sales, không code

---

### GitHub Projects vs. Excel/Google Sheets

| Tiêu chí            | GitHub Projects           | Excel              |
| ------------------- | ------------------------- | ------------------ |
| **Collaboration**   | ✅ Real-time, nhiều người | ⚠️ Dễ conflict     |
| **Tích hợp code**   | ✅ Native                 | ❌ Không có        |
| **Automation**      | ✅ Có                     | ❌ Phải code macro |
| **Version control** | ✅ Git history            | ❌ Không có        |
| **Truy vết**        | ✅ Issue history          | ❌ Khó trace       |

**Kết luận**:

- **Đừng dùng Excel** để quản lý dự án software 😅

---

## 🏆 Tại sao chọn GitHub Projects?

### 1. **Single Source of Truth**

```
Trước đây:
  PM viết spec → Google Doc
  Dev code → GitHub
  QA test → Excel tracking
  Meeting sync → Ngày 3 cái không khớp nhau 🤦

Với GitHub Projects:
  Requirement → Issue (GitHub)
  Code → PR (GitHub)
  Test → Comment in Issue (GitHub)
  → TẤT CẢ Ở 1 NƠI
```

### 2. **Developer-Friendly**

Developer **GHÉT** phải:

- ❌ Chuyển tab sang Jira để update status
- ❌ Copy issue number vào commit message thủ công
- ❌ Sync giữa code và task manager

Với GitHub Projects:

- ✅ Tạo PR → auto link vào issue
- ✅ Merge PR → auto đóng issue
- ✅ Comment trong PR → team thấy ngay trong project board

### 3. **Truy vết từ Requirement → Production**

```
Requirement (Issue #123)
  ↓
Code (PR #124 linked to #123)
  ↓
Review (Comments in PR #124)
  ↓
Merge (Commit abc123)
  ↓
Deploy (Release v1.2.0 includes #123)
  ↓
Bug? (Issue #125 references #123)
```

**Tất cả đều clickable, traceable!**

### 4. **Miễn phí / Rẻ**

| Loại repo               | GitHub Projects                | Jira Cloud           |
| ----------------------- | ------------------------------ | -------------------- |
| Public                  | **FREE**                       | N/A                  |
| Private (team 10 người) | **$4/user/tháng** (GitHub Pro) | **$7-14/user/tháng** |
| Private (team 20 người) | $80/tháng                      | $140-280/tháng       |

### 5. **Đơn giản, dễ onboard**

- Jira: 2-4 tuần để team quen
- GitHub Projects: **3-5 ngày**

---

## 📊 Khi nào nên dùng GitHub Projects?

### ✅ **Phù hợp**

- Team size: 5-50 người
- Code trên GitHub
- Agile/Scrum workflow
- Cần liên kết code ↔ issue chặt chẽ
- Budget hạn chế
- Muốn đơn giản hoá tool stack

### ⚠️ **Cân nhắc**

- Team > 100 người (có thể dùng nhưng cần nhiều project)
- Cần báo cáo phức tạp cho C-level (Jira mạnh hơn)
- Nhiều stakeholder không tech (họ sợ GitHub)
- Code không trên GitHub (thì dùng luôn Jira đi)

### ❌ **Không phù hợp**

- Team không code (dùng Trello/Asana)
- Dự án không software (dùng Monday/ClickUp)
- Cần compliance audit report phức tạp (dùng Jira)

---

## 🎬 Workflow tổng quan

### PM → Dev → QA trong GitHub Projects

```
┌─────────────────────────────────────────────────────────────┐
│                     GitHub Projects Board                    │
├──────────────┬──────────────┬──────────────┬────────────────┤
│   Backlog    │  In Progress │    Review    │      Done      │
├──────────────┼──────────────┼──────────────┼────────────────┤
│              │              │              │                │
│ Issue #101   │ Issue #102   │ Issue #103   │ Issue #104     │
│ [PM tạo]     │ [Dev coding] │ [QA testing] │ [Released]     │
│              │              │              │                │
│ - Title      │ - Assigned   │ - PR merged  │ - Verified     │
│ - AC         │ - PR #150    │ - QA testing │ - In v1.2.0    │
│ - Priority   │   linked     │ - Bug found? │                │
│              │              │   → #105     │                │
└──────────────┴──────────────┴──────────────┴────────────────┘

Flow:
  PM tạo issue → Dev pick & code → Create PR → Review
  → Merge → QA test → Pass → Done → Release
```

---

## 🧪 Ví dụ thực tế

### Case study: Team E-commerce (10 người)

**Trước khi dùng GitHub Projects:**

- PM viết requirement trong Notion
- Dev track task trong Trello
- QA dùng Excel để track test case
- **3 nguồn dữ liệu** → mất 2h/tuần để sync
- Bug lọt production vì QA không biết feature đã merge

**Sau khi dùng GitHub Projects:**

- Tất cả requirement = GitHub Issue
- Dev tạo PR → auto link vào issue
- QA test ngay trên issue, tag dev nếu có bug
- **1 nguồn dữ liệu duy nhất**
- Sync meeting giảm từ 2h → 30 phút/tuần
- Bug lọt production giảm 60%

### Ví dụ issue thực tế

```markdown
Issue #123: Thêm chức năng "Save for later" trong giỏ hàng

**Status**: In Progress
**Assignee**: @dev-john
**Labels**: feature, high-priority
**Sprint**: Sprint 15
**Linked PR**: #124

---

## Description

User cần lưu sản phẩm để mua sau, không muốn mất khi thoát app.

## Acceptance Criteria

- [ ] User click "Save for later" → sản phẩm chuyển sang tab riêng
- [ ] Sản phẩm được lưu vào database (persist)
- [ ] User mở lại app → vẫn thấy sản phẩm đã save

## Technical notes

- API: POST /api/cart/save-for-later
- Database: thêm cột `saved_for_later_at` vào bảng `cart_items`

---

**Comments:**

- @dev-john: PR #124 ready for review
- @qa-alice: Tested on staging ✅ PASS
- @pm-bob: Merged to v1.5.0
```

**Lợi ích:**

- Dev comment PR → PM/QA thấy ngay
- QA comment test result → Dev biết ngay
- Ai cũng thấy history: ai làm gì, khi nào

---

## 🚦 Từ đâu bắt đầu?

### Roadmap học GitHub Projects

```
Bước 1: Đọc xong file này (01-overview.md) ✓
  ↓
Bước 2: Đọc file 02 (Core Concepts) - Hiểu Issue, Project, Status, View
  ↓
Bước 3: Theo role của bạn:
  - PM → File 03 (PM Workflow) + File 04 (Issue Writing)
  - QA → File 05 (QA Workflow) + File 06 (Bug Reporting)
  ↓
Bước 4: Đọc file 07 (PM-Dev-QA Collaboration)
  ↓
Bước 5: Thực hành file 10 (Practical Exercises)
  ↓
Bước 6: Áp dụng vào project thật
```

### Checklist trước khi bắt đầu

- [ ] Có GitHub account
- [ ] Đã tham gia ít nhất 1 repository
- [ ] Hiểu Git cơ bản (commit, branch, PR)
- [ ] Đọc xong file này
- [ ] Sẵn sàng thực hành

---

## ❓ FAQ

### **Q1: GitHub Projects có miễn phí không?**

**A**: Có!

- Public repository: **Hoàn toàn miễn phí**
- Private repository: Free cho cá nhân, team cần trả phí (~$4/user/tháng)

### **Q2: Có cần biết code để dùng GitHub Projects không?**

**A**: **Không bắt buộc** cho PM/QA.

- Bạn chỉ cần biết: tạo issue, comment, update status
- Dev sẽ lo phần code (PR, commit)
- Nhưng hiểu Git cơ bản sẽ giúp làm việc tốt hơn

### **Q3: GitHub Projects có thay thế được Jira không?**

**A**: Tuỳ team.

- Team < 50 người, workflow đơn giản: **Có**
- Team > 100, cần báo cáo phức tạp: **Không**
- Cả 2 đều tốt, quan trọng là team dùng đúng cách

### **Q4: Có thể integrate GitHub Projects với Slack/Teams không?**

**A**: Có!

- GitHub có Slack/Teams integration
- Notification khi issue update, PR merge, etc.
- Xem file 11 (Advanced Techniques) để biết chi tiết

### **Q5: Migration từ Jira/Trello sang GitHub Projects có dễ không?**

**A**: Khá dễ.

- Export Jira/Trello → CSV
- Import vào GitHub Projects (hoặc tạo issue bằng script)
- Mất ~1-2 ngày cho team 10 người
- Xem file 11 (Advanced) phần Migration

---

## 📚 Tài liệu tham khảo

- [GitHub Projects Official Docs](https://docs.github.com/en/issues/planning-and-tracking-with-projects)
- [GitHub Projects Roadmap](https://github.com/orgs/github/projects/4247)
- [GitHub Blog: Projects V2](https://github.blog/changelog/label/projects/)

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu GitHub Projects là gì
- [ ] Biết sự khác biệt vs Jira/Trello
- [ ] Hiểu tại sao GitHub Projects phù hợp với team dev
- [ ] Hình dung được workflow: PM → Dev → QA trên GitHub Projects
- [ ] Sẵn sàng đọc file tiếp theo

---

**🚀 Tiếp theo: [02-core-concepts.md](./02-core-concepts.md) - Các khái niệm cốt lõi**
