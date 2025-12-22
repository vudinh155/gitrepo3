# 02 - Core Concepts (Khái niệm cốt lõi)

> **Mục tiêu**: Hiểu rõ các khái niệm cơ bản: Issue, Project, Status, Field, View

**Thời lượng**: 45 phút
**Đối tượng**: PM, QA, Dev (tất cả)

---

## 🎯 Các khái niệm bạn sẽ học

1. **Issue** - Đơn vị công việc cơ bản
2. **Project** - Bảng quản lý tổng thể
3. **Status** - Trạng thái của issue
4. **Field** - Thuộc tính của issue (custom field)
5. **View** - Cách hiển thị project (Board/Table/Roadmap)
6. **Label** - Tag phân loại issue
7. **Milestone** - Nhóm issue theo mốc thời gian
8. **Assignee** - Người phụ trách

---

## 📝 1. Issue - Đơn vị công việc cơ bản

### Issue là gì?

**Issue** = 1 đơn vị công việc có thể theo dõi được.

Trong GitHub Projects:
- 1 Issue = 1 Feature / 1 Task / 1 Bug / 1 Question
- Mỗi Issue có:
  - **Title** (tiêu đề)
  - **Description** (mô tả chi tiết)
  - **Assignee** (người làm)
  - **Labels** (tag)
  - **Status** (trạng thái)
  - **Comments** (thảo luận)

### Ví dụ Issue

```markdown
Issue #456: User không thể đăng nhập bằng Google

Repository: my-app/backend
Status: In Progress
Assignee: @dev-john
Labels: bug, high-priority, auth
Milestone: Sprint 10

---

## Mô tả
User click "Login with Google" nhưng bị redirect về trang chủ,
không đăng nhập được.

## Steps to reproduce
1. Vào trang /login
2. Click "Login with Google"
3. Cho phép quyền truy cập
4. Bị redirect về / (không login)

## Expected behavior
User được đăng nhập và redirect về /dashboard

## Actual behavior
User bị redirect về / (trang chủ), vẫn chưa login

## Environment
- Browser: Chrome 120
- OS: macOS 14
- Tested on: staging.myapp.com

## Linked PR
- PR #457: Fix Google OAuth redirect URL
```

### Các loại Issue phổ biến

| Loại | Mục đích | Label thường dùng | Ví dụ |
|------|----------|-------------------|-------|
| **Feature** | Tính năng mới | `feature`, `enhancement` | "Thêm chức năng export PDF" |
| **Bug** | Lỗi cần fix | `bug`, `critical` | "Crash khi upload ảnh > 10MB" |
| **Task** | Công việc kỹ thuật | `task`, `tech-debt` | "Refactor authentication module" |
| **Documentation** | Viết tài liệu | `docs` | "Viết API docs cho /api/users" |
| **Question** | Thảo luận, hỏi đáp | `question`, `discussion` | "Nên dùng Redis hay Memcached?" |

---

## 🗂️ 2. Project - Bảng quản lý tổng thể

### Project là gì?

**Project** = Workspace để quản lý nhiều Issue.

```
Project "Mobile App v2.0"
  ├── Issue #101: Redesign homepage
  ├── Issue #102: Add dark mode
  ├── Issue #103: Fix login bug
  └── Issue #104: Improve performance
```

### Đặc điểm Project V2 (mới)

- ✅ **Linh hoạt**: 1 Issue có thể nằm trong nhiều Project
- ✅ **Custom field**: Tạo field riêng (Priority, Estimation, etc.)
- ✅ **Multi-view**: Board, Table, Roadmap trong 1 project
- ✅ **Automation**: Tự động update status khi PR merge

### Sự khác biệt: Issue vs Project

| | Issue | Project |
|---|-------|---------|
| **Là gì?** | 1 đơn vị công việc | 1 nhóm nhiều issue |
| **Scope** | 1 task cụ thể | Cả feature/sprint/release |
| **Thuộc về** | 1 repository | Có thể span nhiều repo |
| **Ví dụ** | "Fix login bug" | "Sprint 15" hoặc "Mobile App v2" |

---

## 🚦 3. Status - Trạng thái của Issue

### Status là gì?

**Status** = Trạng thái hiện tại của issue.

Ví dụ workflow đơn giản:

```
Backlog → Todo → In Progress → Review → Done
```

### Ví dụ Status trong team thực tế

#### Option A: Workflow đơn giản (cho team nhỏ)

```
┌─────────┐   ┌─────────┐   ┌──────┐
│ Backlog │ → │ In Prog │ → │ Done │
└─────────┘   └─────────┘   └──────┘
```

#### Option B: Workflow đầy đủ (cho team 10+ người)

```
┌─────────┐   ┌──────┐   ┌─────────┐   ┌────────┐   ┌─────────┐   ┌──────┐
│ Backlog │ → │ Todo │ → │ In Prog │ → │ Review │ → │ Testing │ → │ Done │
└─────────┘   └──────┘   └─────────┘   └────────┘   └─────────┘   └──────┘
                                             ↓
                                        ┌─────────┐
                                        │ Blocked │
                                        └─────────┘
```

### Ý nghĩa từng Status

| Status | Ý nghĩa | Ai phụ trách | Ví dụ |
|--------|---------|--------------|-------|
| **Backlog** | Chưa lên kế hoạch | PM | Feature tương lai |
| **Todo** | Đã lên kế hoạch sprint này | PM | Sẽ làm tuần này |
| **In Progress** | Đang code | Dev | Dev đang code |
| **Review** | Chờ review code | Dev + Reviewer | PR đã tạo, chờ approve |
| **Testing** | Đang test | QA | QA đang test |
| **Blocked** | Bị kẹt | Dev/QA | Thiếu API, chờ thiết kế |
| **Done** | Hoàn thành | - | Đã merge + test pass |

### ⚠️ Lưu ý về Status

**✅ Làm đúng:**
- Status phản ánh **hiện thực**, không phải ước muốn
- Chỉ người đang làm mới update status
- Status phải rõ ràng, không mơ hồ

**❌ Làm sai:**
- PM đổi status "In Progress" cho dev khi chưa bắt đầu
- Issue "Done" nhưng chưa test
- Quá nhiều status (>7 status = confusing)

---

## 🏷️ 4. Field - Thuộc tính của Issue

### Field là gì?

**Field** = Thuộc tính bổ sung cho Issue.

GitHub Projects có 2 loại field:

### A. **Default Fields** (Có sẵn)

| Field | Kiểu | Ví dụ | Mục đích |
|-------|------|-------|----------|
| **Title** | Text | "Fix login bug" | Tiêu đề |
| **Assignees** | People | @dev-john | Người làm |
| **Labels** | Tag | `bug`, `high-priority` | Phân loại |
| **Status** | Select | In Progress | Trạng thái |
| **Milestone** | Select | Sprint 15 | Nhóm theo mốc |
| **Repository** | Link | my-app/backend | Repo chứa issue |

### B. **Custom Fields** (Tự tạo)

Bạn có thể tạo thêm field riêng:

| Field | Kiểu | Ví dụ giá trị | Dùng để |
|-------|------|---------------|---------|
| **Priority** | Select | High, Medium, Low | Độ ưu tiên |
| **Estimation** | Number | 3 (story points) | Ước lượng |
| **Team** | Select | Frontend, Backend, QA | Phân team |
| **Epic** | Text | "User Authentication" | Nhóm feature |
| **Due Date** | Date | 2024-12-31 | Deadline |
| **Severity** (bug) | Select | Critical, Major, Minor | Mức độ nghiêm trọng |

### Ví dụ Issue với Custom Fields

```
Issue #789: Thêm chức năng đặt lịch hẹn

┌─────────────────────────────────────────┐
│ Default Fields:                         │
│  - Status: In Progress                  │
│  - Assignee: @dev-alice                 │
│  - Labels: feature, high-priority       │
│  - Milestone: Sprint 16                 │
│                                         │
│ Custom Fields:                          │
│  - Priority: High                       │
│  - Estimation: 5 points                 │
│  - Team: Backend                        │
│  - Epic: Calendar Module                │
│  - Due Date: 2024-06-15                 │
└─────────────────────────────────────────┘
```

---

## 👁️ 5. View - Cách hiển thị Project

### View là gì?

**View** = Cách nhìn khác nhau vào cùng 1 project.

GitHub Projects V2 có 3 loại view:

### A. **Board View** (Kanban Board)

```
┌───────────┬─────────────┬──────────┬──────┐
│  Backlog  │ In Progress │  Review  │ Done │
├───────────┼─────────────┼──────────┼──────┤
│           │             │          │      │
│ Issue #1  │ Issue #2    │ Issue #3 │ #4   │
│ Issue #5  │ Issue #6    │          │ #7   │
│ Issue #8  │             │          │ #9   │
│           │             │          │      │
└───────────┴─────────────┴──────────┴──────┘
```

**Dùng khi:**
- Daily standup
- Sprint planning
- Muốn thấy flow của công việc

**Ai dùng:** PM, Dev, QA (tất cả)

---

### B. **Table View** (Spreadsheet)

```
┌────────┬─────────────────────┬──────────┬─────────┬──────────┬────────┐
│   ID   │       Title         │  Status  │ Assignee│ Priority │  Est.  │
├────────┼─────────────────────┼──────────┼─────────┼──────────┼────────┤
│  #101  │ Fix login bug       │ Review   │ @john   │   High   │   3    │
│  #102  │ Add dark mode       │ In Prog  │ @alice  │  Medium  │   5    │
│  #103  │ Redesign homepage   │ Todo     │ @bob    │   High   │   8    │
│  #104  │ Write API docs      │ Backlog  │    -    │   Low    │   2    │
└────────┴─────────────────────┴──────────┴─────────┴──────────┴────────┘
```

**Dùng khi:**
- Cần sort/filter nhanh
- Export data
- Bulk edit nhiều issue

**Ai dùng:** PM Lead, QA Lead, Manager

---

### C. **Roadmap View** (Timeline)

```
Jan 2024        Feb 2024        Mar 2024        Apr 2024
    │               │               │               │
    ├─────────────────────────────┤               │
    │  Epic: Auth Module          │               │
    │                             │               │
    │   ├───────┤                 │               │
    │   │ #101  │ (Fix login)     │               │
    │       ├─────────┤            │               │
    │       │  #102   │ (OAuth)    │               │
    │                             │               │
    │                             ├───────────────────────┤
    │                             │ Epic: Mobile App      │
    │                             │                       │
```

**Dùng khi:**
- Planning release
- Stakeholder demo
- Thấy big picture

**Ai dùng:** PM, Stakeholder, Manager

---

### So sánh 3 View

| View | Dùng cho | Ưu điểm | Nhược điểm |
|------|----------|---------|------------|
| **Board** | Daily work | Trực quan, dễ drag-drop | Khó thấy big picture |
| **Table** | Analysis | Sort/filter mạnh, export được | Không trực quan |
| **Roadmap** | Planning | Thấy timeline rõ ràng | Khó edit nhanh |

**💡 Pro tip:** Tạo nhiều view cho các mục đích khác nhau!

---

## 🏷️ 6. Label - Tag phân loại Issue

### Label là gì?

**Label** = Tag để phân loại issue.

### Labels phổ biến trong team

#### A. **Theo loại công việc**

| Label | Màu | Ý nghĩa |
|-------|-----|---------|
| `feature` | 🟦 Blue | Tính năng mới |
| `bug` | 🟥 Red | Lỗi cần fix |
| `enhancement` | 🟩 Green | Cải tiến feature cũ |
| `docs` | 📘 Light Blue | Tài liệu |
| `test` | 🟨 Yellow | Viết test |

#### B. **Theo độ ưu tiên**

| Label | Màu | Ý nghĩa |
|-------|-----|---------|
| `critical` | 🟥 Red | Phải fix ngay |
| `high-priority` | 🟧 Orange | Làm tuần này |
| `medium-priority` | 🟨 Yellow | Làm sprint này |
| `low-priority` | 🟩 Green | Làm khi rảnh |

#### C. **Theo team**

| Label | Ý nghĩa |
|-------|---------|
| `team:frontend` | Dành cho Frontend team |
| `team:backend` | Dành cho Backend team |
| `team:qa` | Dành cho QA team |
| `team:design` | Cần design |

#### D. **Theo trạng thái đặc biệt**

| Label | Ý nghĩa |
|-------|---------|
| `blocked` | Bị kẹt, cần giải quyết dependency |
| `wontfix` | Quyết định không fix |
| `duplicate` | Trùng với issue khác |
| `help-wanted` | Cần hỗ trợ |
| `good-first-issue` | Phù hợp người mới |

### ⚠️ Best Practices với Label

**✅ Làm đúng:**
- Mỗi issue có 2-4 labels (không quá nhiều)
- Label có ý nghĩa rõ ràng
- Màu sắc nhất quán (bug = đỏ, feature = xanh)

**❌ Làm sai:**
- Quá nhiều label (>20 labels = confusing)
- Label trùng nghĩa nhau (`urgent` vs `critical`)
- Không dùng label (khó filter)

---

## 🎯 7. Milestone - Nhóm Issue theo mốc thời gian

### Milestone là gì?

**Milestone** = Nhóm nhiều issue theo 1 mốc thời gian/mục tiêu.

```
Milestone: "Sprint 15" (2024-06-01 → 2024-06-14)
  ├── Issue #101: Fix login bug
  ├── Issue #102: Add dark mode
  ├── Issue #103: Redesign homepage
  └── Issue #104: Write API docs

  Progress: 2/4 issues completed (50%)
```

### Milestone vs Epic

| | Milestone | Epic |
|---|-----------|------|
| **Định nghĩa** | Mốc thời gian | Nhóm tính năng |
| **Ví dụ** | Sprint 15, Release v2.0 | User Authentication, Payment Module |
| **Có deadline** | ✅ Có | ❌ Không |
| **GitHub native** | ✅ Có | ❌ Không (dùng label/field) |

### Ví dụ Milestone thực tế

```
Milestone: Release v2.0 (Due: 2024-06-30)
  Progress: ███████░░░ 70% (14/20 issues)

  Completed ✅:
    - #101: User authentication
    - #102: Dashboard redesign
    - #103: API v2 migration
    ...

  In Progress 🏃:
    - #110: Payment integration (80% done)
    - #111: Mobile responsive (50% done)

  Todo 📋:
    - #115: Beta testing
    - #116: Security audit
    - #117: Documentation
    - #118: Release notes
```

---

## 👤 8. Assignee - Người phụ trách

### Assignee là gì?

**Assignee** = Người chịu trách nhiệm hoàn thành issue.

### Rules về Assignee

**✅ Làm đúng:**
- 1 issue = 1 assignee (owner duy nhất)
- Assignee phải là người **thực sự làm**, không phải người "theo dõi"
- Chuyển assignee khi handover công việc

**❌ Làm sai:**
- 1 issue có 3-4 assignee (ai cũng nghĩ người khác làm)
- Assign cho PM (PM không code, nên là reporter thôi)
- Không assign (issue bỏ không)

### Ví dụ workflow Assignee

```
Issue #123: Fix login bug

Created by: @pm-bob (Reporter)
Assigned to: @dev-john (Owner)

Comments:
  @pm-bob: John, bạn fix issue này nhé
  @dev-john: OK, tôi đang xem
  @dev-john: PR #124 ready for review
  @dev-john: /assign @qa-alice (chuyển cho QA test)

Assignee changed: @dev-john → @qa-alice

Comments:
  @qa-alice: Tested ✅ PASS
  @qa-alice: /assign @pm-bob (chuyển cho PM verify)

Assignee changed: @qa-alice → @pm-bob

Comments:
  @pm-bob: Verified on staging ✅ Close issue
  (Issue closed)
```

---

## 🔗 Mối quan hệ giữa các khái niệm

### Sơ đồ tổng quan

```
┌─────────────────────────────────────────────────────────────┐
│                         PROJECT                             │
│  (ví dụ: "Mobile App v2.0")                                 │
│                                                             │
│  ┌───────────────────────────────────────────────────┐     │
│  │                 MILESTONE                         │     │
│  │  (ví dụ: "Sprint 15")                             │     │
│  │                                                   │     │
│  │  ┌─────────────────────────────────────────┐     │     │
│  │  │          ISSUE #101                     │     │     │
│  │  │                                         │     │     │
│  │  │  Title: "Fix login bug"                 │     │     │
│  │  │  Assignee: @dev-john                    │     │     │
│  │  │  Labels: bug, high-priority, auth       │     │     │
│  │  │  Status: In Progress                    │     │     │
│  │  │                                         │     │     │
│  │  │  Custom Fields:                         │     │     │
│  │  │    - Priority: High                     │     │     │
│  │  │    - Estimation: 3 points               │     │     │
│  │  │    - Team: Backend                      │     │     │
│  │  │                                         │     │     │
│  │  │  Linked PR: #124                        │     │     │
│  │  └─────────────────────────────────────────┘     │     │
│  │                                                   │     │
│  │  (+ 15 issues khác)                              │     │
│  └───────────────────────────────────────────────────┘     │
│                                                             │
│  Views:                                                     │
│    📋 Board View                                            │
│    📊 Table View                                            │
│    📅 Roadmap View                                          │
└─────────────────────────────────────────────────────────────┘
```

---

## 🧪 Thực hành: Tạo Issue đầu tiên

### Bài tập 1: Tạo Issue "Hello World"

**Mục tiêu:** Làm quen với việc tạo Issue.

**Các bước:**

1. Vào repository của bạn
2. Click **Issues** → **New Issue**
3. Điền thông tin:

```markdown
Title: [Practice] Test tạo issue đầu tiên

Description:
Đây là issue practice để học GitHub Projects.

## Checklist
- [ ] Đọc file 02-core-concepts.md
- [ ] Tạo issue này
- [ ] Thêm label
- [ ] Assign cho bản thân
- [ ] Comment "Done"
- [ ] Close issue

Labels: practice, good-first-issue
Assignee: (chọn bản thân)
```

4. Click **Submit new issue**
5. Thực hiện checklist
6. Close issue khi done

**Kết quả mong đợi:**
- ✅ Biết cách tạo issue
- ✅ Biết cách add label
- ✅ Biết cách assign
- ✅ Biết cách comment & close

---

## ❓ FAQ

### **Q1: Issue và Task khác gì nhau?**

**A**: Trong GitHub:
- **Issue** = đơn vị công việc chung (có thể là feature, bug, task)
- **Task** = 1 loại issue (thường là công việc kỹ thuật)

→ Task là 1 dạng của Issue (dùng label `task` để phân biệt)

### **Q2: Nên dùng bao nhiêu Status?**

**A**: Tuỳ team, nhưng khuyến nghị:
- Team nhỏ (< 5 người): 3-4 status (Backlog → In Progress → Done)
- Team vừa (5-15 người): 5-6 status (Backlog → Todo → In Progress → Review → Done)
- Team lớn (> 15 người): 6-8 status (thêm Testing, Blocked)

→ **Đừng quá 8 status**, sẽ rối!

### **Q3: Custom Field và Label khác gì nhau?**

**A**:
- **Label**: Tag đơn giản, nhiều người thấy, filter dễ
- **Custom Field**: Structured data, có type (number, date, select), filter mạnh hơn

**Ví dụ:**
- Priority → Nên dùng **Custom Field** (vì có 3 giá trị cố định: High/Medium/Low)
- Team → Nên dùng **Label** (vì nhiều người cần filter nhanh)

### **Q4: Milestone có bắt buộc không?**

**A**: **Không bắt buộc**, nhưng nên dùng nếu:
- Team làm theo Sprint/Release
- Cần track deadline
- Cần báo cáo tiến độ cho stakeholder

Nếu team làm Kanban (không sprint), có thể không cần Milestone.

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu Issue là gì, các loại issue
- [ ] Hiểu Project là workspace chứa nhiều issue
- [ ] Hiểu Status và workflow cơ bản
- [ ] Hiểu Field (default + custom)
- [ ] Hiểu 3 loại View: Board, Table, Roadmap
- [ ] Hiểu Label và cách phân loại
- [ ] Hiểu Milestone và Epic
- [ ] Hiểu Assignee
- [ ] Đã tạo ít nhất 1 issue practice

---

**🚀 Tiếp theo:**
- **PM** → Đọc [03-pm-role-and-workflow.md](./03-pm-role-and-workflow.md)
- **QA** → Đọc [05-qa-role-and-workflow.md](./05-qa-role-and-workflow.md)
- **Dev** → Đọc [07-pm-dev-qa-collaboration.md](./07-pm-dev-qa-collaboration.md)
